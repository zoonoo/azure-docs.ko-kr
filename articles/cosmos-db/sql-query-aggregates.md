---
title: Azure Cosmos DB 집계 함수
description: Azure Cosmos DB에서 지 원하는 집계 함수 유형 및 SQL 집계 함수 구문에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79464464"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 집계 함수

집계 함수는 절의 값 집합에 대해 계산을 수행 `SELECT` 하 고 단일 값을 반환 합니다. 예를 들어 다음 쿼리는 컨테이너 내의 항목 수를 반환 합니다 `Families` .

## <a name="examples"></a>예

```sql
    SELECT COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "$1": 2
    }]
```

VALUE 키워드를 사용 하 여 집계의 스칼라 값만 반환할 수도 있습니다. 예를 들어 다음 쿼리는 단일 숫자로 값의 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [ 2 ]
```

또한 집계를 필터와 결합할 수 있습니다. 예를 들어 다음 쿼리는의 주소 상태가 인 항목의 수를 반환 합니다 `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

결과는 다음과 같습니다.

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>집계 함수 유형

SQL API는 다음과 같은 집계 함수를 지원 합니다. `SUM`및 `AVG` 는 숫자 값에 대해 작동 `COUNT` 하며 `MIN` `MAX` 숫자, 문자열, 부울 및 null에 대 한 작업을 수행 합니다.

| 기능 | 설명 |
|-------|-------------|
| 개수 | 식에서 항목 수를 반환합니다. |
| 합계   | 식에서 모든 값의 합계를 반환합니다. |
| 최소   | 식의 최소값을 반환합니다. |
| 최대   | 식의 최대값을 반환합니다. |
| 평균   | 식에서 평균값을 반환합니다. |

배열 반복의 결과를 집계할 수도 있습니다.

> [!NOTE]
> Azure Portal의 데이터 탐색기에서 집계 쿼리는 하나의 쿼리 페이지에 대 한 일부 결과만 집계할 수 있습니다. SDK는 모든 페이지에 걸쳐 단일 누적 값을 생성 합니다. 코드를 사용 하 여 집계 쿼리를 수행 하려면 .NET SDK 1.12.0, .NET Core SDK 1.1.0 또는 Java SDK 1.9.5 이상이 필요 합니다.

## <a name="remarks"></a>설명

이러한 집계 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)를 활용 합니다. 속성에서,,, 또는를 수행할 것으로 간주 되는 경우 `COUNT` `SUM` `MIN` `MAX` `AVG` [인덱싱 정책에 관련 경로를 포함](index-policy.md#includeexclude-strategy)해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)