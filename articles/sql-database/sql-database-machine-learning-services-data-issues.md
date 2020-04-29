---
title: R 및 SQL 데이터 형식 및 개체 작업
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 발생할 수 있는 일반적인 문제를 포함 하 여 Machine Learning Services (미리 보기)를 사용 하 여 Azure SQL Database에서 R의 데이터 형식 및 데이터 개체를 사용 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e81cca3e20d5b6c050489e80b91d013d5e934cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453202"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Azure SQL Database Machine Learning Services(미리 보기)에서 R 및 SQL 데이터 사용

이 문서에서는 [Azure SQL Database에서 r을 사용 하 여 Machine Learning Services](sql-database-machine-learning-services-overview.md)에서 r과 SQL Database 간에 데이터를 이동할 때 발생할 수 있는 일반적인 문제 중 일부에 대해 설명 합니다. 이 연습은 고유 스크립트로 데이터 작업을 수행할 때 필요한 기본 배경 지식을 제공합니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

발생할 수 있는 일반적인 문제는 다음과 같습니다.

- 경우에 따라 데이터 형식이 일치하지 않음
- 암시적 변환이 발생할 수 있음
- 경우에 따라 캐스트 및 변환 작업이 필요함
- R 및 SQL이 서로 다른 데이터 개체를 사용함

## <a name="prerequisites"></a>전제 조건

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행 하려면 먼저 [Machine Learning Services (R 사용)를](sql-database-machine-learning-services-overview.md) 사용 하도록 설정 된 Azure SQL Database 있어야 합니다.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="working-with-a-data-frame"></a>데이터 프레임 사용

스크립트에서 R을 SQL로 반환 하는 결과를 반환 하는 경우 데이터를 **데이터 프레임**으로 반환 해야 합니다. 스크립트에서 생성하는 다른 형식의 개체는 목록, 요소, 벡터 또는 이진 데이터이든 관계없이 저장 프로시저 결과의 일부로 출력하려면 데이터 프레임으로 변환되어야 합니다. 다행히도 기타 개체를 데이터 프레임으로 변경하는 기능을 지원하는 여러 R 함수가 있습니다. 이진 모델을 직렬화 하 고 데이터 프레임에 반환할 수도 있습니다 .이는이 문서의 뒷부분에서 수행할 수 있습니다.

먼저, 몇 가지 기본 R 개체인 벡터, 행렬 및 목록을 실험 하 고 데이터 프레임으로 변환 하면 SQL에 전달 된 출력이 어떻게 변경 되는지 알아보겠습니다.

R에서 두 가지 "Hello World" 스크립트를 비교합니다. 두 스크립트가 거의 동일하게 보이지만 첫 번째 스크립트는 3개 값의 단일 열을 반환하고, 두 번째 스크립트는 각각 하나의 단일 값이 포함된 세 개의 열을 반환합니다.

**예 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**예제 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

결과가 다른 이유는 무엇일까요?

일반적으로 R `str()` 명령을 사용하여 그 대답을 찾을 수 있습니다. R 스크립트에 `str(object_name)` 함수를 추가하여 지정된 R 개체의 데이터 스키마가 정보 메시지로 반환되도록 합니다. SSMS의 **메시지** 탭에서 메시지를 볼 수 있습니다.

예제 1 및 예제 2의 결과가 다른 이유를 확인하려면 다음과 같이 각 문에서 `@script` 변수 정의의 끝 부분에 `str(OutputDataSet)` 줄을 삽입합니다.

**str 함수가 추가된 예제 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**str 함수가 추가된 예제 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

이제 **메시지**의 텍스트를 검토하여 출력이 다른 이유를 확인합니다.

**결과 - 예제 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**결과 - 예제 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

확인한 것처럼 R 구문을 약간 변경한 것이 결과의 스키마에 큰 영향을 미쳤습니다. 모든 세부 정보에 대 한 자세한 내용은 [Hadley Wickham의 "Advanced R"](http://adv-r.had.co.nz)의 *데이터 구조* 섹션에서 자세히 설명 되어 있습니다.

우선은 R 개체를 데이터 프레임으로 강제 변환할 때 예상 결과를 확인해야 합니다.

> [!TIP]
> 또한 `is.matrix`, `is.vector`와 같은 R id 함수를 사용 하 여 내부 데이터 구조에 대 한 정보를 반환할 수 있습니다.

## <a name="implicit-conversion-of-data-objects"></a>데이터 개체의 암시적 변환

각 R 데이터 개체에는 두 개의 개체에 같은 수의 차원이 포함되거나 데이터 개체에 다른 데이터 형식이 포함될 경우 다른 데이터 개체와 결합될 때 값을 처리하는 방법에 대한 자체 규칙이 있습니다.

예를 들어 R을 사용 하 여 행렬 곱셈을 수행 하려는 경우를 가정해 보겠습니다. 단일 열 행렬을 4 개의 값이 있는 배열에 의해 3 개의 값과 곱하고 4x3 매트릭스가 결과로 나타날 것으로 간주 합니다.

먼저 작은 테스트 데이터 테이블을 만듭니다.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

이제 다음 스크립트를 실행 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

내부적으로 3개 열이 포함된 열이 단일 열 행렬로 변환됩니다. 행렬은 R에서 특별한 경우인 배열이므로 두 인수가 일치하도록 `y`는 암시적으로 단일 열 행렬로 강제 변환됩니다.

**결과**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

하지만 `y` 배열의 크기를 변경할 경우 발생하는 상황을 확인하세요.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

이제 R는 결과로 단일 값을 반환합니다.

**결과**
    
|Col1|
|---|
|1542|

그 이유는 이 경우 두 인수가 동일한 길이의 벡터로 처리될 수 있기 때문에 R는 내부 곱을 행렬로 반환합니다.  이는 선형 대 수 규칙에 따라 예상 되는 동작입니다. 그러나 다운스트림 응용 프로그램에서 출력 스키마를 변경 하지 않을 것으로 예상 하는 경우 문제가 발생할 수 있습니다.

## <a name="merge-or-multiply-columns-of-different-length"></a>다른 길이의 열 병합 또는 곱하기

R은 다양한 크기의 벡터를 사용하고 이러한 열 유사 구조를 데이터 프레임으로 결합하는 뛰어난 유연성을 제공합니다. 벡터 목록은 테이블처럼 보일 수 있지만 데이터 테이블에 적용되는 모든 규칙을 따르는 것은 아닙니다.

예를 들어 다음 스크립트는 길이가 6인 숫자 배열을 정의하고 R 변수 `df1`에 저장합니다. 그러면 숫자 배열이 3 개의 값이 포함 된 RTestData 테이블 (위에서 만든)의 정수와 결합 되어 새 데이터 프레임을 `df2`만듭니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

데이터 프레임을 채우기 위해 R는 RTestData에서 검색된 요소를 `df1` 배열의 요소 수와 일치하도록 필요한 횟수만큼 반복합니다.

**결과**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

데이터 프레임은 테이블 처럼 보이지만 실제로 벡터 목록입니다.

## <a name="cast-or-convert-sql-data"></a>SQL 데이터 캐스트 또는 변환

R 및 SQL에서는 동일한 데이터 형식을 사용 하지 않으므로 SQL에서 쿼리를 실행 하 여 데이터를 가져온 다음 R 런타임으로 전달 하면 일반적으로 일부 형식의 암시적 변환이 수행 됩니다. 다른 변환 집합은 R에서 SQL로 데이터를 반환할 때 발생 합니다.

- SQL은 쿼리에서 R 프로세스로 데이터를 푸시하고 효율성 향상을 위해 내부 표현으로 변환 합니다.
- R 런타임이 데이터를 data.frame 변수에 로드하고 데이터에서 고유한 작업을 수행합니다.
- 데이터베이스 엔진은 보안 내부 연결을 사용 하 여 데이터를 SQL로 반환 하 고 SQL 데이터 형식에 따라 데이터를 표시 합니다.
- SQL 쿼리를 실행 하 고 테이블 형식 데이터 집합을 처리할 수 있는 클라이언트 또는 네트워크 라이브러리를 사용 하 여 SQL에 연결 하 여 데이터를 가져옵니다. 이 클라이언트 애플리케이션은 다른 방식으로 데이터에 영향을 미칠 수 있습니다.

이 기능이 어떻게 작동 하는지 확인 하려면 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 데이터 웨어하우스에서 다음과 같은 쿼리를 실행 합니다. 이 뷰는 예측 생성에 사용된 매출 데이터를 반환합니다.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 모든 AdventureWorks 버전을 사용하거나 고유 데이터베이스를 사용해서 다른 쿼리를 만들 수 있습니다. 여기서는 텍스트, 날짜/시간 및 숫자 값이 포함 된 일부 데이터를 처리 하려고 합니다.

이제이 쿼리를 저장 프로시저에 대 한 입력으로 사용해 보세요.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

오류가 발생하면 쿼리 텍스트를 약간 편집해야 할 수 있습니다. 예를 들어 WHERE 절의 문자열 조건자는 두 개의 작은따옴표 집합으로 묶어야 합니다.

쿼리를 실행한 후 `str` 함수의 결과를 검토하여 R에서 입력 데이터가 어떻게 처리되는지 확인합니다.

**결과**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- datetime 열은 R 데이터 형식 **POSIXct**를 사용하여 처리되었습니다.
- "제품 시리즈" 텍스트 열은 범주 변수를 의미 하는 **요소로**식별 되었습니다. 문자열 값은 기본적으로 요소로 처리됩니다. R에 문자열을 전달하면 문자열은 내부에서 사용하도록 정수로 변환되고 다시 출력의 문자열에 매핑됩니다.

## <a name="summary"></a>요약

이러한 간단한 예제로부터 SQL 쿼리를 입력으로 전달할 때 데이터 변환으로 인한 효과를 확인할 필요성이 있다는 것을 알 수 있습니다. 일부 SQL 데이터 형식은 R에서 지원 되지 않으므로 오류를 방지 하려면 다음과 같은 방법을 고려 하십시오.

- 데이터를 미리 테스트하고 R 코드에 전달될 때 문제가 될 수 있는 스키마의 열 또는 값을 확인합니다.
- `SELECT *`를 사용하지 않고 입력 데이터 원본에서 열을 개별적으로 지정하고 각 열이 어떻게 처리되는지 알아봅니다.
- 문제를 방지하려면 입력 데이터를 준비할 때 필요에 따라 명시적 캐스트를 수행합니다.
- 오류를 일으키고 모델링에 유용하지 않은 데이터 열 전달(예: GUIDS 또는 rowguids)을 방지합니다.

지원 되거나 지원 되지 않는 R 데이터 형식에 대 한 자세한 내용은 [r 라이브러리 및 데이터 형식](/sql/advanced-analytics/r/r-libraries-and-data-types)을 참조 하세요.
