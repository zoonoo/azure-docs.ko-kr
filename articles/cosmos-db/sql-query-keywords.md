---
title: Azure Cosmos DB에 대 한 SQL 키워드
description: Azure Cosmos DB에 대 한 SQL 키워드에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 1f3c4ef56feb77e9b01375b8b5dbdb567f5bfadb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179972"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB의 키워드
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 SQL 쿼리에서 Azure Cosmos DB는 데 사용할 수 있는 키워드에 대해 자세히 설명 합니다.

## <a name="between"></a>BETWEEN

키워드를 사용 `BETWEEN` 하 여 문자열 또는 숫자 값의 범위에 대해 쿼리를 표현할 수 있습니다. 예를 들어 다음 쿼리는 첫 번째 자식의 등급이 1-5 (포함) 인 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

`BETWEEN` `SELECT` 다음 예제와 같이 절에서 키워드를 사용할 수도 있습니다.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API에서 ANSI SQL과 달리 혼합 형식의 속성에 대해 범위 쿼리를 표현할 수 있습니다. 예를 들어는 `grade` `5` 일부 항목에서와 같은 숫자와 다른 문자열의 문자열 일 수 있습니다 `grade4` . 이러한 경우 JavaScript에서와 같이 서로 다른 두 형식 간의 비교로 인해 `Undefined` 항목이 생략 됩니다.

## <a name="distinct"></a>DISTINCT

`DISTINCT`키워드는 쿼리의 프로젝션에서 중복 요소를 제거 합니다.

이 예제에서 쿼리는 각 성에 대 한 값을 프로젝션 합니다.

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

고유한 개체를 프로젝션 할 수도 있습니다. 이 경우 두 문서 중 하나에 lastName 필드가 없으므로 쿼리가 빈 개체를 반환 합니다.

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

`DISTINCT` 는 하위 쿼리 내의 프로젝션에서 사용할 수도 있습니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

이 쿼리는 중복 항목을 제거 하 고 각 자식의 givenName 포함 된 배열을 프로젝션 합니다. 이 배열은 ChildNames로 별칭이 지정 되며 외부 쿼리에서 프로젝션 됩니다.

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

집계 시스템 함수를 사용하는 쿼리와 `DISTINCT`를 사용하는 하위 쿼리는 지원되지 않습니다. 예를 들어 다음 쿼리는 지원되지 않습니다.

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>LIKE

특정 문자열이 지정 된 패턴과 일치 하는지 여부에 따라 부울 값을 반환 합니다. 패턴은 일반 문자와 와일드카드 문자를 포함할 수 있습니다. `LIKE`키워드 또는 [RegexMatch](sql-query-regexmatch.md) 시스템 함수를 사용 하 여 논리적으로 동일한 쿼리를 작성할 수 있습니다. 선택한 항목에 관계 없이 동일한 인덱스 사용률을 확인할 수 있습니다. 따라서 `LIKE` 구문을 정규식 보다 더 선호 하는 경우를 사용 해야 합니다.

> [!NOTE]
> 는 인덱스를 사용할 수 있기 때문에를 `LIKE` 사용 하 여 비교 하는 속성에 대해 [범위 인덱스를 만들어야](./index-policy.md) 합니다 `LIKE` .

다음과 같은 와일드 카드 문자를 사용할 수 있습니다.

| 와일드카드 문자 | Description                                                  | 예제                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | 0 개 이상의 문자를 가진 문자열                      | WHERE c. "%,% PS%"와 같은 설명      |
| _ (밑줄)     | 임의의 단일 문자                                       | WHERE c. "% SO_PS%"와 같은 설명      |
| [ ]                  | 지정 된 범위 ([a-f]) 또는 집합 ([abcdef]) 내의 단일 문자입니다. | WHERE c. 설명: "%)  |
| [^]                  | 지정 된 범위 ([^ a-f]) 또는 집합 ([^ abcdef])에 속하지 않는 임의의 단일 문자입니다. | WHERE c. "% [^ abc] PS%"와 같은 설명 |


### <a name="using-like-with-the--wildcard-character"></a>LIKE와 % 와일드카드 문자 사용

`%`문자는 0 개 이상의 문자를 갖는 모든 문자열과 일치 합니다. 예를 `%` 들어, 패턴의 시작과 끝에를 배치 하면 다음 쿼리는가 포함 된 설명이 있는 모든 항목을 반환 합니다 `fruit` .

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

`%`패턴 끝에 문자만 사용 하는 경우로 시작 하는 설명이 포함 된 항목만 반환 합니다 `fruit` .

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>좋아요 사용 안 함

아래 예제에서는 설명이 포함 되지 않은 모든 항목을 반환 합니다 `fruit` .

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Escape 절 사용

이스케이프 절을 사용 하 여 하나 이상의 와일드 카드 문자를 포함 하는 패턴을 검색할 수 있습니다. 예를 들어 문자열을 포함 하는 설명을 검색 하려면를 `20-30%` 와일드 카드 문자로 해석 하지 않는 것이 좋습니다 `%` .

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>와일드 카드 문자를 리터럴로 사용

와일드 카드 문자를 대괄호로 묶어 리터럴 문자로 취급할 수 있습니다. 와일드 카드 문자를 대괄호로 묶으면 특수 한 특성을 모두 제거 합니다. 몇 가지 예제는 다음과 같습니다.

| 무늬           | 의미 |
| ----------------- | ------- |
| LIKE "20-30 [%]" | 20-30%  |
| LIKE "[_] n"     | _n      |
| LIKE "[[]"    | [       |
| LIKE "]"        | ]       |

## <a name="in"></a>IN

IN 키워드를 사용 하 여 지정 된 값이 목록에 있는 값과 일치 하는지 여부를 확인 합니다. 예를 들어 다음 쿼리는가 또는 인 모든 패밀리 항목을 반환 합니다 `id` `WakefieldFamily` `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

다음 예에서는 상태가 지정 된 값을 갖는 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API는 [JSON 배열](sql-query-object-array.md#Iteration)에 대 한 반복을 지원 하며, FROM 소스에 있는 in 키워드를 통해 새 구문을 추가 합니다.

필터에 파티션 키를 포함 하는 경우 `IN` 쿼리는 관련 파티션만 자동으로 필터링 합니다.

## <a name="top"></a>맨 위로 이동

TOP 키워드는 `N` 정의 되지 않은 순서로 쿼리 결과의 첫 번째 수를 반환 합니다. 모범 사례로 절과 함께 TOP을 사용 `ORDER BY` 하 여 결과를 정렬 된 첫 번째 값으로 제한할 수 `N` 있습니다. 이러한 두 절을 결합 하는 것이 가장 높은 영향을 주는 행을 예측 가능 하 게 나타내는 유일한 방법입니다.

다음 예제와 같이 상수 값 이나 매개 변수가 있는 쿼리를 사용 하는 변수 값으로 TOP을 사용할 수 있습니다.

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
