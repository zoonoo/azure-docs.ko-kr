---
title: Azure SQL Database(미리 보기)에서 Machine Learning Services(R 포함) 사용 빠른 시작 | Microsoft Docs
description: 이 토픽에서는 Azure SQL Database에서 Machine Learning Services를 사용하는 방법 및 R 스크립트를 실행하여 시스템 규모에 관계 없이 고급 분석을 제공하는 방법, 그리고 네트워크를 통해 데이터를 끌어올 필요가 없도록 데이터가 상주하는 위치에 계산 및 처리를 제공하는 기능을 보여줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 04/04/2019
ms.openlocfilehash: f5d43794ba80e4791c8bf24ad404069a8329518e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051226"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>빠른 시작: Azure SQL Database(미리 보기)에서 Machine Learning Services(R 포함) 사용

이 문서에서는 [Azure SQL Database에서 Machine Learning Services(R 포함)](sql-database-machine-learning-services-overview.md) 공개 미리 보기를 사용하는 방법을 설명합니다. SQL 데이터베이스와 R 간에 데이터를 이동하는 기본 사항을 안내합니다. 잘 구성된(Well-Formed) R 코드를 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 저장 프로시저에 래핑하여 SQL 데이터베이스에서 기계 학습 모델을 빌드, 교육 및 사용하는 방법도 설명합니다.

강력한 R 언어를 사용하여 데이터베이스에 대한 고급 분석 및 기계 학습을 제공합니다. 이 기능은 데이터가 상주하는 위치에서 계산 및 처리를 수행하기 때문에 네트워크를 통해 데이터를 끌어올 필요가 없습니다. 또한 강력한 엔터프라이즈 R 패키지를 활용하여 고급 분석을 대규모로 제공합니다.

Machine Learning Services는 Microsoft의 엔터프라이즈 R 패키지가 오버레이된 기본 R 배포판을 포함하고 있습니다. Microsoft의 R 함수 및 알고리즘은 규모 및 유용성을 중심으로 엔지니어링되었으며 예측 분석, 통계 모델링, 데이터 시각화 및 첨단 기계 학습 알고리즘을 제공합니다.

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup)하세요.

## <a name="prerequisites"></a>필수 조건

이 연습의 예제 코드를 실행하려면 Machine Learning Services(R 포함)가 사용되는 SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

SQL Database에 연결하고 T-SQL 쿼리 또는 저장 프로시저를 실행할 수 있는 한, 사용자는 SQL Database에 연결하고 R 스크립트 데이터베이스 관리 또는 쿼리 도구를 실행할 수 있습니다. 이 빠른 시작에서는 [SQL Server Management Studio](sql-database-connect-query-ssms.md)를 사용합니다.

[패키지 추가](#add-package) 연습에서는 로컬 컴퓨터에 [R](https://www.r-project.org/) 및 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)을 설치해야 합니다.

이 빠른 시작에서는 서버 수준 방화벽 규칙도 구성해야 합니다. 이 작업 방법을 보여주는 빠른 시작은 [서버 수준 방화벽 규칙 만들기](sql-database-server-level-firewall-rule.md)를 참조하세요.

## <a name="verify-r-exists"></a>R이 있는지 확인

SQL 데이터베이스에 Machine Learning Services(R 포함)를 사용하도록 설정되었는지 확인할 수 있습니다. 아래 단계를 따르세요.

1. SQL Server Management Studio를 열고 SQL 데이터베이스에 연결합니다. 연결 방법에 대한 자세한 내용은 [빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.

1. 아래 코드를 실행합니다. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    모두 정상이면 다음과 비슷한 결과 메시지가 표시됩니다.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. 오류가 발생하면 SQL 데이터베이스에 Machine Learning Services(R 포함) 공개 미리 보기를 사용하도록 설정되지 않은 것입니다. 위에서 공개 미리 보기에 등록하는 방법을 살펴보세요.

## <a name="grant-permissions"></a>권한 부여

관리자는 외부 코드를 자동으로 실행할 수 있지만, 다른 사용자는 모두 권한을 부여받아야 합니다.

명령을 실행하기 전에 `<username>`을 올바른 데이터베이스 사용자 로그인으로 바꿉니다.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>기본 R 상호 작용

두 가지 방법으로 SQL Database에서 R 코드를 실행할 수 있습니다.

+ R 스크립트를 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 시스템 저장 프로시저의 인수로 추가합니다.
+ [원격 R 클라이언트](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client)에서 SQL 데이터베이스에 연결하고 SQL Database를 계산 컨텍스트로 사용하여 코드를 실행합니다.

다음 연습에서는 첫 번째 상호 작용 모델 저장 프로시저에 R 코드를 전달하는 방법을 집중적으로 다룹니다.

1. 간단한 스크립트를 실행하여 SQL 데이터베이스에서 R 스크립트가 실행되는 방법을 확인합니다.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

2. 모든 것을 올바르게 설정했다면 올바른 결과가 계산되고 R `print` 함수가 **메시지** 창에 결과를 반환합니다.

    **결과**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    코드를 테스트할 때 **stdout** 메시지를 가져오면 도움이 되지만, 애플리케이션에서 사용할 수 있도록 또는 테이블에 쓸 수 있도록 결과를 테이블 형식으로 반환해야 하는 경우가 자주 있습니다. 자세한 내용은 아래의 입력 및 출력 섹션을 참조하세요.

`@script` 인수 내부의 모든 항목이 유효한 R 코드여야 합니다.

## <a name="inputs-and-outputs"></a>입력 및 출력

기본적으로 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)는 단일 입력 데이터 세트를 수락하며, 이 데이터 세트는 일반적으로 유효한 SQL 쿼리 형식으로 입력합니다. 다른 유형의 입력은 SQL 변수로 전달할 수 있습니다.

저장 프로시저는 단일 R 데이터 프레임을 출력으로 반환하지만, 스칼라 및 모델을 변수로 출력할 수도 있습니다. 예를 들어 학습된 모델을 이진 변수로 출력하여 T-SQL INSERT 문에 전달하고, 해당 모델을 테이블에 쓸 수 있습니다. 플롯(이진 형식) 또는 스칼라(날짜 및 시간, 모델을 교육하는 데 걸린 시간 등의 개별 값)를 생성할 수도 있습니다.

지금은 sp_execute_external_script의 기본 입력 및 출력 변수인 `InputDataSet` 및 `OutputDataSet`만 살펴보겠습니다.

1. 다음 T-SQL 문을 실행하여 작은 테스트 데이터 테이블을 만듭니다.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    테이블이 생성되면 다음 명령문을 사용하여 테이블을 쿼리합니다.
  
    ```sql
    SELECT * FROM RTestData
    ```

    **결과**

    ![RTestData 테이블의 콘텐츠](./media/sql-database-connect-query-r/select-rtestdata.png)

2. 테이블의 데이터를 R 스크립트의 입력으로 가져올 수 있습니다. 아래 명령문을 실행합니다. 이 명령문은 테이블에서 데이터를 가져오고, R 런타임을 왕복하고, 열 이름이 *NewColName*인 값을 반환합니다.

    쿼리에서 반환하는 데이터는 R 런타임에 전달되고, 이 런타임은 데이터를 SQL Database에 데이터 프레임으로 반환합니다. WITH RESULT SETS 절은 SQL Database에 대해 반환되는 데이터 테이블의 스키마를 정의합니다.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **결과**

    ![테이블의 데이터를 반환하는 R 스크립트의 출력](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. 입력 또는 출력 변수의 이름을 변경해 보겠습니다. 위의 스크립트에서는 기본 입력 및 출력 변수 이름으로 _InputDataSet_ 및 _OutputDataSet_를 사용했습니다. _InputDatSet_와 연결된 입력 데이터를 정의하려면 *\@input_data_1* 변수를 사용합니다.

    이 스크립트에서 저장 프로시저의 출력 및 입력 변수 이름이 *SQL_out* 및 *SQL_in*으로 변경되었습니다.

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    R은 대/소문자를 구분하므로 `@input_data_1_name` 및 `@output_data_1_name`의 입력 및 출력 변수가 `@script`의 R 코드 대소문자와 일치해야 합니다. 

    매개 변수 순서도 중요합니다. 선택적 매개 변수 *\@input_data_1_name* 및 *\@output_data_1_name*을 사용하려면 먼저 필수 매개 변수 *\@input_data_1* 및 *\@output_data_1*을 지정해야 합니다.

    한 데이터 세트만 매개 변수로 전달할 수 있으며, 한 데이터 세트만 반환할 수 있습니다. 그러나 R 코드 내에서 다른 데이터 세트를 호출할 수 있으며 데이터 세트 외에도 다른 유형의 출력을 반환할 수 있습니다. 또한 모든 매개 변수에 OUTPUT 키워드를 추가하여 결과와 함께 반환되게 만들 수 있습니다. 

    `WITH RESULT SETS` 문은 SQL Database에 사용되는 데이터의 스키마를 정의합니다. R에서 반환하는 각 열에 대한 SQL 호환 데이터 형식을 제공해야 합니다. 스키마 정의를 사용하여 새 열 이름을 제공할 수 있습니다. 반드시 R 데이터 프레임의 열 이름을 사용할 필요는 없습니다.

4. 또한 R 스크립트를 사용하여 값을 생성하고 _@input_data_1_의 입력 쿼리 문자열을 비워 둘 수 있습니다.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **결과**

    ![@script를 입력으로 사용하는 쿼리 결과](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>R 버전 확인

SQL 데이터베이스에 어떤 R 버전이 설치되었는지 확인하려면 다음을 수행합니다.

1. SQL 데이터베이스에서 아래 스크립트를 실행합니다.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. R `print` 함수는 **메시지** 창에 버전을 반환합니다. 아래 예제 출력에서는 이 예의 SQL Database에 R 버전 3.4.4가 설치된 것을 볼 수 있습니다.

    **결과**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>R 패키지 나열

Microsoft는 SQL 데이터베이스에 Machine Learning Services가 미리 설치된 다양한 R 패키지를 제공합니다. 버전, 종속성, 라이선스 및 라이브러리 경로 정보를 포함하여 어떤 R 패키지가 설치되었는지 확인하려면 아래 단계를 수행합니다. 패키지를 추가하려면 [패키지 추가](#add-package) 섹션을 참조하세요.

1. SQL 데이터베이스에서 아래 스크립트를 실행합니다.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. 출력은 R의 `installed.packages()`이며 결과 집합으로 반환되었습니다.

    **결과**

    ![R에 설치된 패키지](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>예측 모델 만들기

R을 사용하여 모델을 교육하고 SQL 데이터베이스의 테이블에 모델을 저장할 수 있습니다. 이 연습에서는 속도를 기반으로 자동차의 정지 거리를 예측하는 단순 회귀 모델을 교육합니다. R에 포함된 작고 이해하기 쉬운 `cars` 데이터 세트를 사용하겠습니다.

1. 먼저 교육 데이터를 저장할 테이블을 만듭니다.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    R 런타임에는 크고 작은 여러 데이터 세트가 포함되어 있습니다. R을 사용하여 설치된 데이터 세트 목록을 가져오려면 R 명령 프롬프트에서 `library(help="datasets")` 명령을 입력합니다.

2. 회귀 모델 만들기 자동차 속도 데이터에는 `dist` 및 `speed` 열이 포함되며, 두 열 모두 숫자입니다. 일부 속도는 여러 차례 관찰됩니다. 이 데이터를 사용하여 자동차의 속도와 자동차를 멈추는 데 필요한 거기 간의 관계를 설명하는 선형 회귀 모델을 만들 것입니다.

    선형 모델의 요구 사항은 간단합니다.

   - 종속 변수 `speed`와 독립 변수 `distance` 사이의 관계를 설명하는 수식을 정의합니다.

   - 모델을 교육하는 데 사용할 입력 데이터를 제공합니다.

     > [!TIP]
     > 선형 모델에 리프레셔가 필요한 경우 rxLinMod를 사용하여 모델을 맞추는 프로세스를 설명하는 [선형 모델 맞춤](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) 자습서를 권장합니다.

     모델을 빌드하려면 R 코드 내에서 수식을 정의하고, 데이터를 입력 매개 변수로 전달합니다.

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     rxLinMod의 첫 번째 인수는 거리를 속도의 종속 변수로 정의하는 *formula* 매개 변수입니다. 입력 데이터는 SQL 쿼리에 의해 채워지는 `CarsData` 변수에 저장됩니다. 입력 데이터에 구체적인 이름을 할당하지 않으면 기본 변수 이름 _InputDataSet_가 사용됩니다.

2. 다음으로, 모델을 재교육하거나 예측에 사용할 수 있도록 모델을 저장할 테이블을 만듭니다. 모델을 만드는 R 패키지의 출력은 일반적으로 **이진 개체**입니다. 따라서 테이블에서 **VARBINARY(max)** 형식의 열을 제공해야 합니다.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. 모델을 저장하려면 다음 Transact-SQL 문을 실행하여 저장 프로시저를 호출하고, 모델을 생성하고, 테이블에 저장합니다.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    이 코드를 두 번째로 실행하면 다음 오류가 표시됩니다.

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    이 오류를 방지하는 한 가지 옵션은 각 새 모델의 이름을 업데이트하는 것입니다. 예를 들어 좀 더 구체적인 이름으로 변경하고 모델 유형, 만든 날짜 등을 포함할 수 있습니다.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. 일반적으로 저장 프로시저 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)의 R 출력은 단일 데이터 프레임으로 제한됩니다.

    그러나 데이터 프레임 외에도 스칼라 같은 다른 유형의 출력을 반환할 수 있습니다.

    예를 들어 모델을 교육하고 싶지만 모델의 계수 테이블을 즉시 보려 한다고 가정해 봅시다. 계수 테이블을 기본 결과 집합으로 만들고, 학습된 모델을 SQL 변수에 출력할 수 있습니다. 변수를 호출하여 모델을 즉시 재사용할 수도 있고, 다음과 같이 테이블에 모델을 저장할 수도 있습니다.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **결과**

    ![추가 출력이 있는 학습된 모델](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>예측

이전 섹션에서 만든 모델을 사용하여 새 데이터에 대 한 예측을 채점합니다. 새 데이터를 사용하여 _채점_을 수행하려면 테이블에서 학습된 모델 중 하나를 가져온 다음, 예측의 기반으로 사용할 새 데이터 집합을 호출합니다. 채점은 종종 데이터 과학에서 학습된 모델에 제공되는 새 데이터를 기반으로 예측, 확률 또는 기타 값을 생성한다는 의미로 사용됩니다.

1. 먼저 새 속도 데이터를 사용하여 테이블을 만듭니다. 원래 교육 데이터가 시속 25마일의 속도에서 정지하는 것을 보셨나요? 원래 데이터가 1920년의 실험을 기반으로 하기 때문입니다. 1920년대 자동차가 60mph 또는 100mph로 이동할 수 있다고 가정할 때 정지 거리는 얼마나 될까요? 이 질문에 답하려면 새로운 속도 값을 제공해야 합니다.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    이 예제의 모델은 **RevoScaleR** 패키지의 일부로 제공된 **rxLinMod** 알고리즘을 기반으로 하기 때문에 일반 R `predict` 함수가 아닌 [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) 함수를 호출합니다.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    위의 스크립트는 다음 단계를 수행합니다.

   + SELECT 문을 사용하여 테이블에서 단일 모델을 가져온 후 입력 매개 변수로 전달합니다.

   + 테이블에서 모델을 검색한 후 모델에서 `unserialize` 함수를 호출합니다.

       > [!TIP] 
       > RevoScaleR에서 제공하는 새 [직렬화 함수](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)도 확인합니다. 이 함수는 실시간 채점을 지원합니다.
   + 적절한 인수를 사용하여 `rxPredict` 함수를 모델에 적용하고, 새 입력 데이터를 제공합니다.

   + 예제에서 `str` 함수는 R에서 반환되는 데이터의 스키마를 확인하기 위해 테스트 단계에서 추가됩니다. 명령문을 나중에 제거할 수 있습니다.

   + R 스크립트에 사용되는 열 이름이 반드시 저장 프로시저 출력에 전달되는 것은 아닙니다. 여기서는 WITH RESULTS 절을 사용하여 새 열 이름을 정의했습니다.

     **결과**

     ![장지 거리를 예측하는 결과 집합](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     [Transact-SQL의 PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)를 사용하여 저장된 모델을 기반으로 예측 값 또는 점수를 생성할 수 있습니다.

<a name="add-package"></a>

## <a name="add-a-package"></a>패키지 추가

SQL 데이터베이스에 설치되지 않은 패키지를 사용해야 하는 경우 [sqlmlutils](https://github.com/Microsoft/sqlmlutils)를 사용하여 설치할 수 있습니다. 아래 단계에 따라 패키지를 설치합니다.

1. [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist)에서 최신 **sqlmlutils** Zip 파일을 로컬 컴퓨터에 다운로드합니다. 파일의 압축을 풀 필요는 없습니다.

1. R이 설치되지 않은 경우 [www.r-project.org](https://www.r-project.org/)에서 R을 다운로드하여 로컬 컴퓨터에 설치합니다. R은 Windows, MacOS 및 Linux에서 사용할 수 있습니다. 이 예제에서는 Windows를 사용합니다.

1. 먼저, **sqlmlutils**의 필수 구성 요소인 **RODBCext** 패키지를 설치합니다. **RODBCext**는 종속 요소인 **RODBC** 패키지지도 설치합니다. **명령 프롬프트**를 열고 다음 명령을 실행합니다.

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    "'R'은 내부 또는 외부 명령, 실행할 수 있는 프로그램 또는 배치 파일이 아닙니다"라는 오류가 발생하면 R.exe 경로가 Windows의 **PATH** 환경 변수에 포함되지 않은 것일 수 있습니다. 명령을 실행하기 전에 환경 변수에 디렉터리를 추가하거나 명령 프롬프트에서 디렉터리로 이동할 수 있습니다(예: `cd C:\Program Files\R\R-3.5.1\bin`).

1. **R CMD INSTALL** 명령을 사용하여 **sqlmlutils**를 설치합니다. Zip 파일을 다운로드한 디렉터리 경로 및 Zip 파일 이름을 지정합니다. 예: 

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    다음과 비슷한 출력이 표시됩니다.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. 이 예제에서는 RStudio Desktop을 IDE로 사용합니다. 원한다면 다른 IDE를 사용해도 됩니다. 아직 RStudio를 설치하지 않은 경우 [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/)에서 RStudio Desktop을 다운로드하여 설치합니다.

1. **RStudio**를 열고 새 **R 스크립트** 파일을 만듭니다. 다음 R 코드를 사용하여 sqlmlutils를 사용하는 패키지를 설치합니다. 아래 예제에서는 문자열을 포맷하고 보간할 수 있는 [glue](https://cran.r-project.org/web/packages/glue/) 패키지를 설치합니다.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **범위**는 **공용** 또는 **전용**입니다. 공용 범위는 데이터베이스 관리자가 모든 사용자가 사용할 수 있는 패키지를 설치할 때 유용합니다. 전용 범위를 선택하면 패키지를 설치하는 사용자만 패키지를 사용할 수 있습니다. 범위를 지정하지 않으면 기본 범위인 **전용**이 사용됩니다.

1. 이제 **glue** 패키지가 설치되었는지 확인합니다.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **결과**

    ![RTestData 테이블의 콘텐츠](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. 패키지를 설치한 후에는 **sp_execute_external_script**를 통해 R 스크립트에서 패키지를 사용할 수 있습니다. **SQL Server Management Studio**를 열고 SQL 데이터베이스에 연결합니다. 다음 스크립트를 실행합니다.

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    [메시지] 탭에 다음 결과가 표시됩니다.

    **결과**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. 패키지를 제거하려면 로컬 컴퓨터의 **RStudio**에서 다음 R 스크립트를 실행합니다.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> SQL 데이터베이스에 R 패키지를 설치하는 또 다른 방법은 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)를 사용하여 R 패키지를 바이트 스트림에 업로드하는 것입니다.

## <a name="next-steps"></a>다음 단계

Machine Learning Services에 대한 자세한 내용은 아래 문서를 참조하세요. 이러한 문서 중 일부는 SQL Server와 관련되어 있지만, 대부분의 정보가 Azure SQL Database의 Machine Learning Services(R 포함)에도 적용됩니다.

- [Azure SQL Database Machine Learning Services(R 포함)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning 서비스](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [자습서: SQL Server에서 R을 사용하여 데이터베이스 내 분석 알아보기](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [R 및 SQL Server에 대한 엔드투엔드 데이터 과학 연습](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [자습서: SQL Server 데이터에 RevoScaleR R 함수 사용](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
