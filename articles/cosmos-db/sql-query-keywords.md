---
title: Azure 코스모스 DB의 SQL 키워드
description: Azure Cosmos DB의 SQL 키워드에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261570"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure 코스모스 DB의 키워드

이 문서에서는 Azure Cosmos DB SQL 쿼리에서 사용할 수 있는 키워드에 대해 자세히 설명합니다.

## <a name="between"></a>BETWEEN

키워드를 `BETWEEN` 사용하여 문자열 또는 숫자 값 범위에 대한 쿼리를 표현할 수 있습니다. 예를 들어 다음 쿼리는 첫 번째 하위 등급이 1-5인 모든 항목을 반환합니다( 포함).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

다음 예제와 `BETWEEN` 같이 `SELECT` 절의 키워드를 사용할 수도 있습니다.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API에서 ANSI SQL과 달리 혼합 형식의 속성에 대해 범위 쿼리를 표현할 수 있습니다. 예를 들어 `grade` 일부 항목과 `5` 같은 숫자와 다른 `grade4` 항목과 같은 문자열일 수 있습니다. 이러한 경우 JavaScript에서와 같이 두 가지 유형 간의 `Undefined`비교결과 에 대한 항목이 건너뜁니다.

> [!TIP]
> 쿼리 실행 시간을 더 빠르게 만들려면 `BETWEEN` 절이 필터링하는 숫자 속성 또는 경로에 대해 range index 형식을 사용하는 인덱싱 정책을 만듭니다.

## <a name="distinct"></a>DISTINCT

키워드는 `DISTINCT` 쿼리의 프로젝션에서 중복을 제거합니다.

이 예제에서 쿼리는 각 성에 대한 값을 투영합니다.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

결과는 다음과 같습니다.

```json
[
    "Andersen"
]
```

고유한 객체를 투영할 수도 있습니다. 이 경우 lastName 필드는 두 문서 중 하나에 없으므로 쿼리가 빈 개체를 반환합니다.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

결과는 다음과 같습니다.

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT는 하위 쿼리 내의 프로젝션에도 사용할 수 있습니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

이 쿼리는 중복제거된 각 자식의 givenName을 포함하는 배열을 투영합니다. 이 배열은 자식 이름으로 별칭이 지정되고 외부 쿼리에 투영됩니다.

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

집계 시스템 함수와 하위 쿼리가 `DISTINCT` 있는 쿼리는 지원되지 않습니다. 예를 들어 다음 쿼리는 지원되지 않습니다.

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

IN 키워드를 사용하여 지정된 값이 목록의 모든 값과 일치하는지 확인합니다. 예를 들어 다음 쿼리는 `id` `WakefieldFamily` 또는 `AndersenFamily`을 있는 모든 패밀리 항목을 반환합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

다음 예제에서는 상태가 지정된 값 중 하나입니다 모든 항목을 반환합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API는 FROM 소스의 in 키워드를 통해 추가된 새 구문과 함께 [JSON 배열을 반복하는](sql-query-object-array.md#Iteration)지원을 제공합니다.

필터에 파티션 키를 `IN` 포함하면 쿼리가 관련 파티션으로만 자동으로 필터링됩니다.

## <a name="top"></a>맨 위로 이동

TOP 키워드는 첫 `N` 번째 쿼리 결과 수를 정의되지 않은 순서로 반환합니다. 가장 좋은 방법은 `ORDER BY` TOP을 절과 함께 사용하여 `N` 결과를 정렬된 값의 첫 번째 수로 제한합니다. 이 두 절을 결합하는 것이 TOP에 영향을 미치는 행을 예측할 수 있는 유일한 방법입니다.

다음 예제와 같이 상수 값으로 TOP를 사용하거나 매개 변수화된 쿼리를 사용하여 변수 값을 사용할 수 있습니다.

```sql
    SELECT TOP 1 *
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [조인](sql-query-join.md)
- [하위 쿼리](sql-query-subquery.md)