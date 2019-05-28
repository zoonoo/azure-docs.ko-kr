---
title: '자습서: R에서 예측 모델 배포'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 세 부분으로 이루어진 자습서의 3부에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델을 배포합니다.
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
ms.date: 05/02/2019
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978697"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 배포

이 세 부분으로 이루어진 자습서의 3부에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델을 배포합니다.

포함된 R 스크립트를 사용하여 이 모델로 예측하는 저장 프로시저를 만듭니다. 모델은 Azure SQL Database에서 실행되므로 데이터베이스에 저장된 데이터를 통해 쉽게 학습할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 예측 모델을 데이터베이스 테이블에 저장
> * 모델을 생성하는 저장 프로시저 만들기
> * 모델을 사용하여 예측하는 저장 프로시저 만들기
> * 새 데이터로 모델 실행

[1부](sql-database-tutorial-predictive-model-prepare-data.md)에서는 샘플 데이터베이스를 Azure SQL Database로 가져오고, R에서 예측 모델을 학습하는 데 사용할 데이터를 준비하는 방법을 살펴보았습니다.

[2부](sql-database-tutorial-predictive-model-build-compare.md)에서는 여러 모델을 만들고 학습한 후 가장 정확한 모델을 선택하는 방법을 알아보았습니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

* 이 자습서 시리즈의 3부에서는 [**1부**](sql-database-tutorial-predictive-model-prepare-data.md) 및 [**2부**](sql-database-tutorial-predictive-model-build-compare.md)를 완료했다고 가정합니다.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>모델을 생성하는 저장 프로시저 만들기

이 자습서 시리즈의 2부에서는 의사 결정 트리(dtree) 모델이 가장 정확하다고 확인했습니다. 이제 RevoScaleR 패키지에서 rxDTree를 사용하여 dtree 모델을 학습하고 생성하는 저장 프로시저(`generate_rental_rx_model`)를 만듭니다.

Azure Data Studio 또는 SSMS에서 다음 명령을 실행합니다.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>데이터베이스 테이블에 모델 저장

TutorialDB 데이터베이스에 테이블을 만들고 테이블에 모델을 저장합니다.

1. 모델을 저장하기 위한 테이블(`rental_rx_models`)을 만듭니다.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. 모델 이름을 "rxDTree"로 지정하여 모델을 테이블에 이진 개체로 저장합니다.

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>예측을 수행하는 저장 프로시저 만들기

학습된 모델 및 새 데이터 세트를 사용하여 예측하는 저장 프로시저(`predict_rentalcount_new`)를 만듭니다.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>새 데이터로 모델 실행

이제 저장 프로시저 `predict_rentalcount_new`를 사용하여 새 데이터에서 대여 수를 예측할 수 있습니다.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

다음과 유사한 결과가 표시되어야 합니다.

```results
RentalCount_Predicted
332.571428571429
```

Azure SQL Database에서 모델을 만들고, 학습하고, 배포했습니다. 그런 후 저장 프로시저에서 해당 모델을 사용하여 새 데이터에 따라 값을 예측했습니다.

## <a name="clean-up-resources"></a>리소스 정리

TutorialDB 데이터베이스 사용이 끝난 경우 Azure SQL Database 서버에서 삭제합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **TutorialDB**를 입력하고 구독을 선택합니다.
1. TutorialDB 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 제3부에서는 다음 단계를 완료했습니다.

* 예측 모델을 데이터베이스 테이블에 저장
* 모델을 생성하는 저장 프로시저 만들기
* 모델을 사용하여 예측하는 저장 프로시저 만들기
* 새 데이터로 모델 실행

Azure SQL Database Machine Learning Services(미리 보기)에서 R을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성](sql-database-machine-learning-services-functions.md)
* [Azure SQL Database Machine Learning Services(미리 보기)에서 R 및 SQL 데이터 사용](sql-database-machine-learning-services-data-issues.md)
* [R 패키지를 Azure SQL Database Machine Learning Services(미리 보기)에 추가](sql-database-machine-learning-services-add-r-packages.md)
