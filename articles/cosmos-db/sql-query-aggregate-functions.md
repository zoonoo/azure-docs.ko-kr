---
title: Azure Cosmos DB 집계 함수
description: Azure Cosmos DB에서 지 원하는 집계 함수 유형 및 SQL 집계 함수 구문에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555418"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 집계 함수
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

집계 함수는 절의 값 집합에 대해 계산을 수행 `SELECT` 하 고 단일 값을 반환 합니다. 예를 들어 다음 쿼리는 컨테이너 내의 항목 수를 반환 합니다.

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>집계 함수 유형

SQL API는 다음과 같은 집계 함수를 지원 합니다. `SUM` 및 `AVG` 는 숫자 값에 대해 작동 `COUNT` 하며 `MIN` `MAX` 숫자, 문자열, 부울 및 null에 대 한 작업을 수행 합니다.

| 함수 | 설명 |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | 식에서 평균값을 반환합니다. |
| [COUNT](sql-query-aggregate-count.md) | 식에서 항목 수를 반환합니다. |
| [MAX](sql-query-aggregate-max.md) | 식의 최대값을 반환합니다. |
| [MIN](sql-query-aggregate-min.md) | 식의 최소값을 반환합니다. |
| [SUM](sql-query-aggregate-sum.md) | 식에서 모든 값의 합계를 반환합니다. |


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

## <a name="remarks"></a>설명

이러한 집계 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)를 활용 합니다. 속성에서,,, 또는를 수행할 것으로 간주 되는 경우 `AVG` `COUNT` `MAX` `MIN` `SUM` [인덱싱 정책에 관련 경로를 포함](index-policy.md#includeexclude-strategy)해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)