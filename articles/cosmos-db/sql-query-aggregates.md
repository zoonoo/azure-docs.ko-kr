---
title: Azure 코스모스 DB의 집계 함수
description: Azure Cosmos DB에서 지원하는 SQL 집계 함수 구문, 집계 함수 유형에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464464"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure 코스모스 DB의 집계 함수

집계 함수는 `SELECT` 절의 값 집합에 대한 계산을 수행하고 단일 값을 반환합니다. 예를 들어 다음 쿼리는 컨테이너 내의 `Families` 항목 수를 반환합니다.

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

VALUE 키워드를 사용하여 집계의 스칼라 값만 반환할 수도 있습니다. 예를 들어 다음 쿼리는 단일 숫자로 값의 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [ 2 ]
```

집계를 필터와 결합할 수도 있습니다. 예를 들어 다음 쿼리는 의 주소 상태가 있는 `WA`항목 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

결과는 다음과 같습니다.

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>집계 함수의 유형

SQL API는 다음 집계 함수를 지원합니다. `SUM`및 `AVG` 숫자 값에서 작동 `COUNT` `MIN`및 `MAX` . 및 숫자, 문자열, 부울 및 null에서 작업합니다.

| 함수 | 설명 |
|-------|-------------|
| 개수 | 식에서 항목 수를 반환합니다. |
| 합계   | 식에서 모든 값의 합계를 반환합니다. |
| 최소   | 식의 최소값을 반환합니다. |
| 최대   | 식의 최대값을 반환합니다. |
| 평균   | 식에서 평균값을 반환합니다. |

배열 반복의 결과를 집계할 수도 있습니다.

> [!NOTE]
> Azure 포털의 데이터 탐색기에서 집계 쿼리는 하나의 쿼리 페이지에 만 부분 결과를 집계할 수 있습니다. SDK는 모든 페이지에서 단일 누적 값을 생성합니다. 코드를 사용하여 집계 쿼리를 수행하려면 .NET SDK 1.12.0, .NET 코어 SDK 1.1.0 또는 Java SDK 1.9.5 이상이 필요합니다.

## <a name="remarks"></a>설명

이러한 집계 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 얻을 수 있습니다. 을 `COUNT`수행할 `SUM` `MIN` `MAX`것으로 예상되는 경우 에서 `AVG` 를 수행할 수 있는 경우 또는 속성에 대해 [인덱싱 정책에 관련 경로를 포함해야](index-policy.md#includeexclude-strategy)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 소개](introduction.md)
- [시스템 기능](sql-query-system-functions.md)
- [사용자 정의 기능](sql-query-udfs.md)