---
title: 고급 R 함수 작성
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Machine Learning Services (미리 보기)를 사용 하 여 Azure SQL Database에서 고급 통계 계산을 위해 R 함수를 작성 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84035644"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 SQL 저장 프로시저에 R 수치 연산 및 유틸리티 함수를 포함 하는 방법을 설명 합니다. T-SQL에서 구현하기에 복잡한 고급 통계 함수는 단일 줄의 코드만으로 R에서 수행할 수 있습니다.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행 하려면 먼저 [Machine Learning Services (R 사용)를](machine-learning-services-overview.md) 사용 하도록 설정 된 Azure SQL Database 있어야 합니다.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>난수를 생성하는 저장 프로시저 만들기

간단히 하기 위해 `stats` Machine Learning Services (미리 보기)를 사용 하 Azure SQL Database와 함께 기본적으로 설치 및 로드 되는 R 패키지를 사용 하겠습니다. 패키지에는 함수 중에 일반적인 통계 태스크에 대 한 수백 개의 함수가 포함 되어 있습니다 `rnorm` . 이 함수는 표준 편차와 의미를 감안 하 여 정규 분포를 사용 하 여 지정 된 수의 난수를 생성 합니다.

예를 들어 다음 R 코드는 표준 편차 3을 고려하여 평균 50에 대한 100개의 숫자를 반환합니다.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

T-sql에서 R의이 줄을 호출 하려면를 실행 하 `sp_execute_external_script` 고 다음과 같이 r 스크립트 매개 변수에 r 함수를 추가 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

다른 난수 집합을 더 쉽게 생성할 수 있다면 어떻게 될까요?

SQL과 함께 사용 하는 것이 쉽습니다. 사용자로부터 인수를 가져오는 저장 프로시저를 정의한 다음, 해당 인수를 R 스크립트에 변수로 전달합니다.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 첫 번째 줄은 저장 프로시저가 실행될 때 필요한 각 SQL 입력 매개 변수를 정의합니다.

- `@params`로 시작하는 줄은 R 코드에서 사용되는 모든 변수 및 해당하는 SQL 데이터 형식을 정의합니다.

- 바로 뒤에 오는 줄은 SQL 매개 변수 이름을 해당 R 변수 이름에 매핑합니다.

이제 저장 프로시저에서 R 함수를 래핑했으므로 다음과 같이 쉽게 함수를 호출하고 다른 값을 전달할 수 있습니다.

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>문제 해결에 R 유틸리티 함수 사용

`utils`기본적으로 설치 되는 패키지는 현재 R 환경을 조사 하기 위한 다양 한 유틸리티 함수를 제공 합니다. 이러한 함수는 R 코드가 SQL 및 외부 환경에서 수행 하는 방식에서 불일치를 발견 하는 경우에 유용할 수 있습니다. 예를 들어 R `memory.limit()` 함수를 사용하여 현재 R 환경에 사용할 메모리를 가져올 수 있습니다.

`utils` 패키지는 설치되지만 기본적으로 로드되지 않으므로 먼저 `library()` 함수를 사용하여 로드해야 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 대부분의 사용자는 r에서 시스템 타이밍 함수 (예: 및)를 사용 하 여 `system.time` `proc.time` r 프로세스에 사용 되는 시간을 캡처하고 성능 문제를 분석 하는 것입니다.
