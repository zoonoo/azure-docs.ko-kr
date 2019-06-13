---
title: '자습서: R에서 클러스터링을 수행할 데이터 준비'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 3부작 자습서 시리즈의 1부인 이 자습서에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링을 수행할 수 있도록 Azure SQL 데이터베이스의 데이터를 준비하겠습니다.
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
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419801"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링을 수행하기 위한 데이터 준비

3부작 자습서 시리즈의 1부인 이 자습서에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링을 수행할 수 있도록 Azure SQL 데이터베이스의 데이터를 준비하겠습니다.

*클러스터링*이란 데이터를 서로 비슷한 멤버로 이루어진 그룹으로 구성하는 것이라고 설명할 수 있습니다.
여기서는 **K-평균** 알고리즘을 사용하여 제품 구매 및 환불 데이터 세트의 고객을 클러스터링하겠습니다. 고객을 클러스터링하면 특정 그룹을 대상으로 정해서 마케팅 활동을 보다 효과적으로 집중할 수 있습니다.
K-평균 클러스터링은 유사성을 기반으로 데이터의 패턴을 찾는 *자율 학습* 알고리즘입니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 샘플 데이터베이스를 Azure SQL Database로 가져오기
> * 다양한 기준에 따라 고객 분류
> * R을 사용하여 Azure SQL Database의 데이터를 데이터 프레임으로 로드

[2부](sql-database-tutorial-clustering-model-build.md)에서는 K-평균 클러스터링 모델을 만들고 학습시키는 방법을 알아봅니다.

[3부](sql-database-tutorial-clustering-model-deploy.md)에서는 Azure SQL 데이터베이스에 새 데이터를 기반으로 클러스터링을 수행할 수 있는 저장 프로시저를 만드는 방법을 알아봅니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 [계정을 만듭니다](https://azure.microsoft.com/free/).

* Machine Learning Services가 사용하도록 설정된 Azure SQL Database 서버 - Microsoft는 위에서 설명한 것처럼 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 머신 러닝을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

* RevoScaleR 패키지 - 이 패키지를 로컬로 설치하기 위한 옵션에 대해서는 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms)을 참조하세요.

* R IDE - 이 자습서에서는 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)을 사용합니다.

* SQL 쿼리 도구 - 이 자습서에서는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 또는 SSMS([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms))를 사용하고 있다고 가정합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="import-the-sample-database"></a>샘플 데이터베이스 가져오기

이 자습서에서 사용된 샘플 데이터 세트는 사용자가 다운로드하여 사용할 수 있도록 **.bacpac** 데이터베이스 백업 파일에 저장되었습니다. 이 데이터 세트는 [TPC(Transaction Processing Performance Council)](http://www.tpc.org/default.asp)에서 제공한 [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) 데이터 세트에서 파생되었습니다.

1. [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac) 파일을 다운로드합니다.

1. 다음 세부 정보를 사용하여 [BACPAC 파일을 가져와 Azure SQL Database 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-import)의 지침을 따르세요.

   * 다운로드한 **tpcxbb_1gb.bacpac** 파일에서 가져오기
   * 공개 미리 보기 동안 새 데이터베이스에 대해 **Gen5/vCore** 구성 선택
   * 새 데이터베이스 이름을 "tpcxbb_1gb"로 지정

## <a name="separate-customers"></a>고객 분류

RStudio에서 새 RScript 파일을 만든 후 다음 스크립트를 실행합니다.
SQL 쿼리에서 다음 기준에 따라 고객을 분류합니다.

* **orderRatio** = 환불 주문 비율(일부 환불 또는 전체 환불한 주문 수 대비 총 주문 수)
* **itemsRatio** = 환불 항목 비율(환불한 항목 수 대비 구매한 총 항목 수)
* **monetaryRatio** = 환불 금액 비율(환불한 금액 대비 총 구매 금액)
* **frequency** = 환불 빈도

**paste** 함수에서 **Server**, **UID** 및 **PWD**를 해당 연결 정보로 바꿉니다.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>데이터 프레임으로 데이터 로드

이제 다음 스크립트를 통해 **rxSqlServerData** 함수를 사용하여 쿼리 결과를 R 데이터 프레임에 반환합니다.
이 과정에서, 형식이 올바르게 R로 전송되도록 선택한 열의 형식을 정의하겠습니다(colClasses를 사용하여).

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

다음과 유사한 결과가 표시됩니다.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>리소스 정리

***이 자습서를 더 이상 진행하지 않을 분들은*** Azure SQL Database 서버에서 tpcxbb_1gb 데이터베이스를 삭제합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **tpcxbb_1gb**를 입력하고 구독을 선택합니다.
1. **tpcxbb_1gb** 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 제1부에서는 다음 단계를 완료했습니다.

* 샘플 데이터베이스를 Azure SQL Database로 가져오기
* 다양한 기준에 따라 고객 분류
* R을 사용하여 Azure SQL Database의 데이터를 데이터 프레임으로 로드

이 고객 데이터를 사용하는 기계 학습 모델을 만들려면 이 자습서 시리즈의 2부를 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 만들기](sql-database-tutorial-clustering-model-build.md)
