---
title: '자습서: R에서 클러스터링 모델 배포'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 3부작 자습서 시리즈의 3부인 이 자습서에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링 모델을 배포하겠습니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420221"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링 모델 배포

3부작 자습서 시리즈의 3부인 이 자습서에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링 모델을 배포하겠습니다.

클러스터링을 수행하는 임베디드 R 스크립트를 사용하여 저장 프로시저를 만듭니다. 모델은 Azure SQL Database에서 실행되므로 데이터베이스에 저장된 데이터를 통해 쉽게 학습할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 모델을 생성하는 저장 프로시저 만들기
> * SQL Database에서 클러스터링 수행
> * 클러스터링 정보 사용

[1부](sql-database-tutorial-clustering-model-prepare-data.md)에서는 R에서 클러스터링을 수행할 수 있도록 Azure SQL 데이터베이스의 데이터를 준비하는 방법을 알아보았습니다.

[2부](sql-database-tutorial-clustering-model-build.md)에서는 클러스터링을 수행하는 K-평균 모델을 빌드하는 방법을 알아보았습니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

* 이 자습서 시리즈의 3부에서는 [**1부**](sql-database-tutorial-clustering-model-prepare-data.md) 및 [**2부**](sql-database-tutorial-clustering-model-build.md)를 완료했다고 가정합니다.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>모델을 생성하는 저장 프로시저 만들기

다음 T-SQL 스크립트를 실행하여 저장 프로시저를 만듭니다. 이 프로시저는 이 자습서 시리즈의 1부 및 2부에서 개발된 단계를 다시 만듭니다.

* 구매 및 환불 기록에 따라 고객 분류
* K-평균 알고리즘을 사용하여 네 가지 고객 클러스터 생성

이 프로시저는 이전 작업의 결과로 얻은 고객 클러스터 매핑을 데이터베이스 테이블 **customer_return_clusters**에 저장합니다.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>SQL Database에서 클러스터링 수행

저장 프로시저를 만들었으므로, 이제 다음 스크립트를 실행하여 클러스터링을 수행합니다.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

스크립트가 제대로 작동하는지, 실제로 고객 목록과 클러스터 매핑이 생기는지 확인합니다.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>클러스터링 정보 사용

앞에서 클러스터링 프로시저를 데이터베이스에서 저장했기 때문에, 같은 데이터베이스에 저장된 고객 데이터에 대해 효율적으로 클러스터링을 수행할 수 있습니다. 고객 데이터가 업데이트될 때마다 프로시저를 실행하고 업데이트된 클러스터링 정보를 사용할 수 있습니다.

환불 동작이 적극적으로 이루어지는 그룹인 클러스터 3에 속한 고객에게 홍보 이메일을 보내려 한다고 가정해 봅시다(네 가지 클러스터에 대한 설명은 [2부](sql-database-tutorial-clustering-model-build.md#analyze-the-results)에서 확인 가능). 다음 코드는 클러스터 3에 속한 고객의 이메일 주소를 선택합니다.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

다른 클러스터에 속한 고객의 이메일 주소를 반환하도록 **r.cluster** 값을 변경할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후에는 Azure SQL Database 서버에서 tpcxbb_1gb 데이터베이스를 삭제해도 됩니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **tpcxbb_1gb**를 입력하고 구독을 선택합니다.
1. **tpcxbb_1gb** 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 제3부에서는 다음 단계를 완료했습니다.

* 모델을 생성하는 저장 프로시저 만들기
* SQL Database에서 클러스터링 수행
* 클러스터링 정보 사용

Azure SQL Database Machine Learning Services(미리 보기)에서 R을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델을 학습하기 위한 데이터 준비](sql-database-tutorial-predictive-model-prepare-data.md)
* [Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성](sql-database-machine-learning-services-functions.md)
* [Azure SQL Database Machine Learning Services(미리 보기)에서 R 및 SQL 데이터 사용](sql-database-machine-learning-services-data-issues.md)
* [R 패키지를 Azure SQL Database Machine Learning Services(미리 보기)에 추가](sql-database-machine-learning-services-add-r-packages.md)
