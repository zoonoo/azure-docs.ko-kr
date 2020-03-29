---
title: Azure 코스모스 DB 쿼리 언어의 수학 함수
description: Azure Cosmos DB의 수학 함수에 대해 알아보고 인수로 제공되는 입력 값을 기반으로 계산을 수행하고 숫자 값을 반환합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873270"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>수학적 함수(Azure 코스모스 DB)  

수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.

다음 예제와 같은 쿼리를 실행할 수 있습니다.

```sql
    SELECT VALUE ABS(-4)
```

결과는 다음과 같습니다.

```json
    [4]
```

## <a name="functions"></a>함수

다음 지원되는 기본 제공 수학 함수는 일반적으로 입력 인수를 기반으로 계산을 수행하고 숫자 식을 반환합니다.
  
||||  
|-|-|-|  
|[아 bs](sql-query-abs.md)|[Acos](sql-query-acos.md)|[Asin](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[특급](sql-query-exp.md)|[바닥](sql-query-floor.md)|[로그](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[전원](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[라운드](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[죄](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[사각형](sql-query-square.md)|[탄](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
RAND를 제외한 모든 산술 함수는 결정적 함수입니다. 이는 지정된 입력 값 집합을 사용하여 호출할 때마다 동일한 결과를 반환함을 의미합니다.

## <a name="next-steps"></a>다음 단계

- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
