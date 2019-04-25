---
title: R에서 예측 모델 만들기 및 학습
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 간단한 예측 모델을 만든 다음, 새 데이터를 사용하여 결과를 예측합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 97309a24c0ab12720f968409856a16cab4ff7ac7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013098"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 만들기 및 학습

이 빠른 시작에서는 R을 사용하여 예측 모델을 만들고 학습시키고, 모델을 SQL 데이터베이스의 테이블에 저장한 다음, 모델을 통해 [Azure SQL Database에서 Machine Learning Services(R 포함)](sql-database-machine-learning-services-overview.md)의 공개 미리 보기를 사용하여 새 데이터의 값을 예측합니다. 

이 빠른 시작에서 사용할 모델은 속도에 따라 자동차의 정지 거리를 예측하는 간단한 회귀 모델입니다. 작고 이해하기 쉬우므로 R에 포함된 **cars** 데이터 세트를 사용합니다.

> [!TIP]
> R 런타임에는 크고 작은 여러 데이터 세트가 포함되어 있습니다. R과 함께 데이터 세트의 목록을 가져오려면 R 명령 프롬프트에서 `library(help="datasets")`를 입력하세요.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행하려면 먼저 Machine Learning Services(R 포함)를 사용하도록 설정된 Azure SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만 이 빠른 시작에서는 SSMS를 사용합니다.

- 이 빠른 시작을 수행하려면 서버 수준 방화벽 규칙을 구성해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [서버 수준 방화벽 규칙 만들기](sql-database-server-level-firewall-rule.md)를 참조하세요.

## <a name="create-and-train-a-predictive-model"></a>예측 모델 만들기 및 학습

**cars** 데이터 세트의 자동차 속도 데이터에는 모두 숫자 형식인 **dist**와 **speed**의 두 열이 있습니다. 이 데이터에는 속도가 다른 여러 개의 정지 관측 값이 포함되어 있습니다. 이 데이터를 사용하여 자동차 속도와 자동차를 멈추는 데 필요한 거리 간의 관계를 설명하는 선형 회귀 모델을 만들 것입니다.

선형 모델의 요구 사항은 간단합니다.
- *speed* 종속 변수와 *distance* 독립 변수 사이의 관계를 설명하는 수식을 정의합니다.
- 모델을 교육하는 데 사용할 입력 데이터를 제공합니다.

> [!TIP]
> 선형 모델에 리프레셔가 필요하면 rxLinMod를 사용하여 모델을 적절하게 조정하는 프로세스를 설명하는 [선형 모델 맞춤](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) 자습서를 사용해 보세요.

다음 단계에서는 학습 데이터를 설정하고, 회귀 모델을 만들고, 학습 데이터를 사용하여 모델을 학습시킨 다음, 모델을 SQL 테이블에 저장합니다.

1. **SQL Server Management Studio**를 열고 SQL 데이터베이스에 연결합니다.

   연결에 도움이 필요한 경우 [빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.

1. **CarSpeed** 테이블을 만들어 학습 데이터를 저장합니다.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. `rxLinMod`를 사용하여 회귀 모델을 만듭니다. 

   모델을 작성하려면 R 코드 내에서 수식을 정의한 다음, **CarSpeed** 학습 데이터를 입력 매개 변수로 전달합니다.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     rxLinMod의 첫 번째 인수는 거리를 속도의 종속 변수로 정의하는 *formula* 매개 변수입니다. 입력 데이터는 SQL 쿼리에 의해 채워지는 `CarsData` 변수에 저장됩니다.

1. 나중에 예측에 사용할 수 있도록 모델을 저장할 테이블을 만듭니다. 

   모델을 만드는 R 패키지의 출력은 일반적으로 **이진 개체**이므로 테이블에는 **VARBINARY(max)** 형식의 열이 있어야 합니다.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. 이제 저장 프로시저를 호출하고, 모델을 생성한 다음, 테이블에 저장합니다.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   이 코드를 두 번째로 실행하면 다음 오류가 표시됩니다.

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   이 오류를 방지하는 한 가지 옵션은 새 모델마다 이름을 업데이트하는 것입니다. 예를 들어 좀 더 구체적인 이름으로 변경하고 모델 유형, 만든 날짜 등을 포함할 수 있습니다.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>계수 테이블 보기

일반적으로 저장 프로시저 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)의 R 출력은 단일 데이터 프레임으로 제한됩니다. 그러나 데이터 프레임 외에도 스칼라 같은 다른 유형의 출력을 반환할 수 있습니다.

예를 들어 모델을 학습시키지만 모델의 계수 테이블을 즉시 보려고 한다고 가정합니다. 이렇게 하려면 계수 테이블을 기본 결과 집합으로 만들고, 학습된 모델을 SQL 변수에 출력합니다. 변수를 호출하여 모델을 즉시 다시 사용하거나 다음과 같이 모델을 테이블에 저장할 수 있습니다.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**결과**

![추가 출력이 있는 학습된 모델](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>학습된 모델을 사용하여 새 데이터 점수 매기기

*채점*은 데이터 과학에서 학습된 모델에 제공되는 새 데이터를 기반으로 하여 예측, 확률 또는 다른 값을 생성한다는 의미로 사용되는 용어입니다. 이전 섹션에서 만든 모델을 사용하여 새 데이터에 대한 예측을 채점합니다.

원래 교육 데이터가 시속 25마일의 속도에서 정지하는 것을 보셨나요? 원래 데이터가 1920년의 실험을 기반으로 하기 때문입니다. 1920년대의 자동차가 60mph 속도로 달리거나 심지어 100mph 속도로 빠르게 달릴 수 있으면 정지 거리는 얼마나 될까요? 이 질문에 답하기 위해 새로운 몇 가지 속도 값을 모델에 제공할 수 있습니다.

1. 새 속도 데이터를 사용하여 테이블을 만듭니다.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. 이러한 새 속도 값에서 정지 거리를 예측합니다.

   모델은 **RevoScaleR** 패키지의 일부로 제공된 **rxLinMod** 알고리즘을 기반으로 하므로 제네릭 `predict` R 함수 대신 [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) 함수를 호출합니다.

   이 예제 스크립트는 다음과 같습니다.
   - SELECT 문을 사용하여 테이블에서 단일 모델을 가져옵니다.
   - 모델을 입력 매개 변수로 전달합니다.
   - 모델에서 `unserialize` 함수를 호출합니다.
   - 적절한 인수가 포함된 `rxPredict` 함수를 모델에 적용합니다.
   - 새 입력 데이터를 제공합니다.

   > [!TIP]
   > 실시간 채점은 RevoScaleR에서 제공하는 [직렬화 함수](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)를 참조하세요.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **결과**

   ![장지 거리를 예측하는 결과 집합](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> 이 예제 스크립트에서는 R에서 반환되는 데이터의 스키마를 확인하기 위해 `str` 함수가 테스트 단계에서 추가됩니다. 이 명령문은 나중에 제거할 수 있습니다.
>
> R 스크립트에 사용되는 열 이름이 반드시 저장 프로시저 출력에 전달되는 것은 아닙니다. 여기서는 WITH RESULTS 절에서 새로운 몇 가지 열 이름을 정의합니다.

## <a name="next-steps"></a>다음 단계

Machine Learning Services에 대한 자세한 내용은 아래 문서를 참조하세요. 이러한 문서 중 일부는 SQL Server와 관련되어 있지만, 대부분의 정보가 Azure SQL Database의 Machine Learning Services(R 포함)에도 적용됩니다.

- [Azure SQL Database Machine Learning Services(R 포함)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning 서비스](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [자습서: SQL Server에서 R을 사용하여 데이터베이스 내 분석 알아보기](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [R 및 SQL Server에 대한 종합적인 데이터 과학 연습](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [자습서: SQL Server 데이터에 RevoScaleR R 함수 사용](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
