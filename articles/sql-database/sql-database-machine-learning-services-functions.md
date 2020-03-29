---
title: 고급 R 함수 작성
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 기계 학습 서비스(미리 보기)를 사용하여 Azure SQL Database에서 고급 통계 계산을 위한 R 함수를 작성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702455"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Machine Learning Services(미리 보기)를 사용하여 Azure SQL Database에서 고급 R 함수 작성

이 문서에서는 SQL 저장 프로시저에 R 수학 및 유틸리티 함수를 포함하는 방법을 설명합니다. T-SQL에서 구현하기에 복잡한 고급 통계 함수는 단일 줄의 코드만으로 R에서 수행할 수 있습니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요.](https://azure.microsoft.com/free/)

- 이러한 연습에서 예제 코드를 실행하려면 먼저 Machine Learning Services(R 포함)를 사용하도록 설정된 Azure SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>난수를 생성하는 저장 프로시저 만들기

간단히 하기 위해 기계 `stats` 학습 서비스(미리 보기)를 사용하여 Azure SQL Database에서 기본적으로 설치 및 로드된 R 패키지를 사용해 보겠습니다. 패키지에는 일반적인 통계 작업에 대한 수백 개의 `rnorm` 함수가 포함되어 있습니다. 이 함수는 표준 편차 및 평균이 주어지면 정규 분포를 사용하여 지정된 수의 난수를 생성합니다.

예를 들어 다음 R 코드는 표준 편차 3을 고려하여 평균 50에 대한 100개의 숫자를 반환합니다.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

T-SQL에서 이 R 줄을 호출하려면 다음과 같이 R 스크립트 매개 변수에 R 함수를 실행하고 `sp_execute_external_script` 추가합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

다른 난수 집합을 더 쉽게 생성할 수 있다면 어떻게 될까요?

SQL과 결합하면 간단합니다. 사용자로부터 인수를 가져오는 저장 프로시저를 정의한 다음, 해당 인수를 R 스크립트에 변수로 전달합니다.

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

기본적으로 `utils` 설치된 패키지는 현재 R 환경을 조사하기 위한 다양한 유틸리티 기능을 제공합니다. 이러한 함수는 SQL 및 외부 환경에서 R 코드가 수행하는 방식에서 불일치를 찾는 경우에 유용할 수 있습니다. 예를 들어 R `memory.limit()` 함수를 사용하여 현재 R 환경에 사용할 메모리를 가져올 수 있습니다.

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
> 많은 사용자가 R 프로세스에서 사용되는 시간을 캡처하고 `system.time` `proc.time`성능 문제를 분석하기 위해 와 같은 R의 시스템 타이밍 함수를 사용하는 것을 좋아합니다.
