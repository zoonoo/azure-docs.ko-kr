---
title: Azure Cosmos DB의 수학 함수 쿼리 언어
description: 인수로 제공되는 입력 값을 기반으로 계산을 수행하고 숫자 값을 반환하는 Azure Cosmos DB의 수학 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e3114220b6250afb9d633b073342ba6f2cf92b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567668"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>수학 함수(Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.

다음 예제와 같이 쿼리를 실행할 수 있습니다.

```sql
    SELECT VALUE ABS(-4)
```

결과는 다음과 같습니다.

```json
    [4]
```

## <a name="functions"></a>Functions

다음의 지원되는 기본 제공 수학 함수는 일반적으로 입력 인수를 기반으로 계산을 수행하고 숫자 식을 반환합니다. **인덱스 사용** 열은 해당하는 경우, 사용자가 수학 시스템 함수를 같음 필터가 있는 다른 값과 비교한다고 가정합니다.
 
| 시스템 함수                 | 인덱스 사용량 | [스칼라 집계 함수가 있는 쿼리의 인덱스 사용량](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 설명                                                      |
| ------------------------------- | ----------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| [ABS](sql-query-abs.md)         | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [ACOS](sql-query-acos.md)       | 전체 검사   | 전체 검사                                              |                                                              |
| [ASIN](sql-query-asin.md)       | 전체 검사   | 전체 검사                                              |                                                              |
| [ATAN](sql-query-atan.md)       | 전체 검사   | 전체 검사                                              |                                                              |
| [ATN2](sql-query-atn2.md)       | 전체 검사   | 전체 검사                                              |                                                              |
| [CEILING](sql-query-ceiling.md) | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [COS](sql-query-cos.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [COT](sql-query-cot.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [DEGREES](sql-query-degrees.md) | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [EXP](sql-query-exp.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [FLOOR](sql-query-floor.md)     | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [LOG](sql-query-log.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [LOG10](sql-query-log10.md)     | 전체 검사   | 전체 검사                                              |                                                              |
| [PI](sql-query-pi.md)           | 해당 없음         | 해당 없음                                                    | PI ()는 상수 값을 반환합니다. 결과는 결정적이므로 PI ()를 사용한 비교는 인덱스를 사용할 수 있습니다. |
| [POWER](sql-query-power.md)     | 전체 검사   | 전체 검사                                              |                                                              |
| [RADIANS](sql-query-radians.md) | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [RAND](sql-query-rand.md)       | 해당 없음         | 해당 없음                                                    | Rand()는 난수를 반환합니다. 결과가 비결정적이기 때문에 Rand()를 포함하는 비교는 인덱스를 사용할 수 없습니다. |
| [ROUND](sql-query-round.md)     | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [SIGN](sql-query-sign.md)       | 인덱스 이동  | 인덱스 이동                                             |                                                              |
| [SIN](sql-query-sin.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [SQRT](sql-query-sqrt.md)       | 전체 검사   | 전체 검사                                              |                                                              |
| [SQUARE](sql-query-square.md)   | 전체 검사   | 전체 검사                                              |                                                              |
| [TAN](sql-query-tan.md)         | 전체 검사   | 전체 검사                                              |                                                              |
| [TRUNC](sql-query-trunc.md)     | 인덱스 이동  | 인덱스 이동                                              |                                                              |
## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregate-functions.md)
