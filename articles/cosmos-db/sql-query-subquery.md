---
title: Azure 코스모스 DB에 대한 SQL 하위 쿼리
description: Azure Cosmos DB의 SQL 하위 쿼리 및 일반적인 사용 사례 및 다양한 유형의 하위 쿼리에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870567"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure 코스모스 DB에 대한 SQL 하위 쿼리 예제

하위 쿼리는 다른 쿼리 내에 중첩된 쿼리입니다. 하위 쿼리를 내부 쿼리 또는 내부 선택이라고도 합니다. 하위 쿼리를 포함하는 문을 일반적으로 외부 쿼리라고 합니다.

이 문서에서는 Azure Cosmos DB의 SQL 하위 쿼리 및 일반적인 사용 사례에 대해 설명합니다. 이 문서의 모든 샘플 쿼리는 [Azure Cosmos DB 쿼리 놀이터에서](https://www.documentdb.com/sql/demo)미리 로드된 영양 데이터 집합에 대해 실행할 수 있습니다.

## <a name="types-of-subqueries"></a>하위 쿼리 유형

하위 쿼리에는 두 가지 주요 유형이 있습니다.

* **상관 관계**: 외부 쿼리의 값을 참조하는 하위 쿼리입니다. 하위 쿼리는 외부 쿼리가 처리하는 각 행에 대해 한 번 평가됩니다.
* **상관 관계가 없는**: 외부 쿼리와 독립적인 하위 쿼리입니다. 외부 쿼리에 의존하지 않고 자체적으로 실행할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 상관 하위 쿼리만 지원합니다.

하위 쿼리는 반환하는 행 및 열 수에 따라 추가로 분류할 수 있습니다. 세 가지 유형이 있습니다.
* **표**: 여러 행과 여러 열을 반환합니다.
* **다중 값**: 여러 행과 단일 열을 반환합니다.
* **스칼라**: 단일 행과 단일 열을 반환합니다.

Azure Cosmos DB의 SQL 쿼리는 항상 단일 열(단순 값 또는 복잡한 문서)을 반환합니다. 따라서 Azure Cosmos DB에는 다중 값 및 스칼라 하위 쿼리만 적용할 수 있습니다. FROM 절에서만 다중 값 하위 쿼리를 관계식으로 사용할 수 있습니다. SELECT 또는 WHERE 절에서 스칼라 하위 쿼리를 스칼라 식으로 사용하거나 FROM 절의 관계식식으로 사용할 수 있습니다.

## <a name="multi-value-subqueries"></a>다중 값 하위 쿼리

다중 값 하위 쿼리는 문서 집합을 반환하며 항상 FROM 절 내에서 사용됩니다. 다음과 같은 경우 사용됩니다.

* JOIN 표현식 최적화. 
* 비용이 많이 드는 식을 한 번 평가하고 여러 번 참조합니다.

## <a name="optimize-join-expressions"></a>JOIN 표현식 최적화

다중 값 하위 쿼리는 WHERE 절의 모든 교차 조인 이 아니라 각 select-many 식 다음에 조건자 푸시를 통해 JOIN 식을 최적화할 수 있습니다.

다음과 같은 쿼리를 고려해 보세요.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

이 쿼리의 경우 인덱스는 태그가 있는 모든 문서와 "유아 식 수식"이라는 이름과 일치합니다. 0에서 10 사이의 값을 가진 영양 성분이며, 양이 1보다 큰 서빙 품목입니다. 여기서 JOIN 식은 필터를 적용하기 전에 각 일치 문서에 대한 태그, 영양소 및 서빙 배열의 모든 항목의 교차 곱을 수행합니다. 

WHERE 절은 각 <c, t, n, s> 튜플에 필터 조건어를 적용합니다. 예를 들어 일치하는 문서에 세 개의 배열 각각에 10개의 항목이 있는 경우 1 x 10 x 10 x 10(즉, 1,000) 투플으로 확장됩니다. 여기서 하위 쿼리를 사용하면 다음 식으로 조인하기 전에 조인된 배열 항목을 필터링하는 데 도움이 될 수 있습니다.

이 쿼리는 앞의 쿼리와 동일하지만 하위 쿼리를 사용합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

태그 배열의 항목이 필터와 일치하고 영양소 와 서빙 배열모두에 대해 다섯 가지 항목이 있다고 가정합니다. 그러면 JOIN 식은 첫 번째 쿼리에서 1,000개의 항목이 아닌 1 x 1 x 5 x 5 = 25항목으로 확장됩니다.

## <a name="evaluate-once-and-reference-many-times"></a>한 번 평가하고 여러 번 참조

하위 쿼리는 사용자 정의 함수(DF), 복잡한 문자열 또는 산술 식과 같은 값비싼 식으로 쿼리를 최적화하는 데 도움이 될 수 있습니다. JOIN 식과 함께 하위 쿼리를 사용하여 식을 한 번 평가하지만 여러 번 참조할 수 있습니다.

다음 쿼리는 UDF를 `GetMaxNutritionValue` 두 번 실행합니다.

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

UDF를 한 번만 실행하는 동등한 쿼리는 다음과 같습니다.

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> JOIN 식의 제품 간 동작을 염두에 두십시오. UDF 식이 정의되지 않은 것으로 평가할 수 있는 경우 JOIN 식은 항상 값이 아닌 하위 쿼리에서 개체를 반환하여 단일 행을 생성하도록 해야 합니다.
>

다음은 값이 아닌 개체를 반환하는 유사한 예입니다.

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

이 접근 방식은 UdF에 만국한되지 않습니다. 잠재적으로 비용이 많이 드는 식에 적용됩니다. 예를 들어 수학 함수와 `avg`동일한 접근 방식을 취할 수 있습니다.

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>외부 참조 데이터와 조인 모방

측정 단위 나 국가 코드와 같이 거의 변경되지 않는 정적 데이터를 참조해야 하는 경우가 많습니다. 각 문서에 대해 이러한 데이터를 복제하지 않는 것이 좋습니다. 이러한 중복을 방지하면 문서 크기를 작게 유지하여 저장소를 절약하고 쓰기 성능을 향상시킬 수 있습니다. 하위 쿼리를 사용하여 참조 데이터 컬렉션을 사용하여 내부 조인 의미 체계를 모방할 수 있습니다.

예를 들어 이 참조 데이터 집합을 고려하십시오.

| **단위** | **이름**            | **승수** | **베이스 유닛** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | 나노 그램            | 1.00E-09       | 그램          |
| ㎍       | 마이크로 그램           | 1.00E-06       | 그램          |
| mg       | 밀리 그램           | 1.00E-03       | 그램          |
| g        | 그램                | 1.00E+00       | 그램          |
| Kg       | 킬로그램            | 1.00E+03       | 그램          |
| Mg       | 메가그램            | 1.00E+06       | 그램          |
| Gg       | 기가그램            | 1.00E+09       | 그램          |
| 뉴저지       | 나노줄 (주)           | 1.00E-09       | 줄         |
| μJ       | 마이크로 줄          | 1.00E-06       | 줄         |
| Mj       | 밀리줄 (주)          | 1.00E-03       | 줄         |
| J        | 줄               | 1.00E+00       | 줄         |
| Kj       | 킬로줄           | 1.00E+03       | 줄         |
| Mj       | 메가줄 (주)(주)(주)(주)           | 1.00E+06       | 줄         |
| Gj       | 기가줄           | 1.00E+09       | 줄         |
| cal      | 칼로리             | 1.00E+00       | 칼로리       |
| Kcal     | 칼로리             | 1.00E+03       | 칼로리       |
| Iu       | 국제 단위 |                |               |


다음 쿼리는 이 데이터와 의 조인을 모방하여 단위 이름을 출력에 추가합니다.

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>스칼라 하위 쿼리

스칼라 하위 쿼리 식은 단일 값으로 평가하는 하위 쿼리입니다. 스칼라 하위 쿼리 식의 값은 하위 쿼리의 프로젝션(SELECT 절)의 값입니다.  스칼라 식이 유효한 여러 위치에서 스칼라 하위 쿼리 식을 사용할 수 있습니다. 예를 들어 SELECT 및 WHERE 절의 모든 식에서 스칼라 하위 쿼리를 사용할 수 있습니다.

스칼라 하위 쿼리를 사용 한다고 항상 최적화 하는 데 도움이 되지 않습니다. 예를 들어 scalar 하위 쿼리를 시스템 또는 사용자 정의 함수에 인수로 전달하면 RU(리소스 단위) 소비 또는 대기 시간에 아무런 이점이 없습니다.

스칼라 하위 쿼리는 다음과 같이 추가로 분류할 수 있습니다.
* 단순 식 스칼라 하위 쿼리
* 집계 스칼라 하위 쿼리

## <a name="simple-expression-scalar-subqueries"></a>단순 식 스칼라 하위 쿼리

단순 식 스칼라 하위 쿼리는 집계 식을 포함하지 않는 SELECT 절이 있는 상관 하위 쿼리입니다. 컴파일러는 하나의 더 큰 간단한 식으로 변환하기 때문에 이러한 하위 쿼리는 최적화 이점을 제공하지 않습니다. 내부 쿼리와 외부 쿼리 간에는 상관 관계가 없습니다.

다음은 몇 가지 예입니다.

**실시예 1**

```sql
SELECT 1 AS a, 2 AS b
```

간단한 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음으로 다시 작성할 수 있습니다.

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

두 쿼리 모두 다음 을 생성합니다.

```json
[
  { "a": 1, "b": 2 }
]
```

**실시예 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

간단한 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음으로 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

쿼리 출력:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**실시예 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

간단한 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음으로 다시 작성할 수 있습니다.

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>집계 스칼라 하위 쿼리

집계 스칼라 하위 쿼리는 단일 값으로 평가하는 프로젝션 또는 필터에 집계 함수가 있는 하위 쿼리입니다.

**예 1:**

다음은 프로젝션에 단일 집계 함수 식이 있는 하위 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**실시예 2**

다음은 여러 집계 함수 식이 있는 하위 쿼리입니다.

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**실시예 3**

다음은 프로젝션과 필터 모두에서 집계 하위 쿼리가 있는 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

쿼리 출력:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

이 쿼리를 작성하는 가장 좋은 방법은 하위 쿼리에 조인하고 SELECT 및 WHERE 절 모두에서 하위 쿼리 별칭을 참조하는 것입니다. 이 쿼리는 프로젝션과 필터가 아닌 조인 문 내에서만 하위 쿼리를 실행해야 하기 때문에 더 효율적입니다.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Exists 식

Azure 코스모스 DB는 EXISTS 식을 지원합니다. Azure Cosmos DB SQL API에 내장된 집계 스칼라 하위 쿼리입니다. EXISTS하위쿼리 식을 취하고 하위 쿼리가 행을 반환하는 경우 true를 반환하는 부울 식입니다. 그렇지 않으면 false를 반환합니다.

Azure Cosmos DB SQL API는 부울 식과 다른 스칼라 식을 구분하지 않으므로 SELECT 및 WHERE 절모두에서 EXISTS를 사용할 수 있습니다. 이는 부울 식(예: EXISTS, BETWEEN 및 IN)이 필터로 제한되는 T-SQL과는 다릅니다.

EXISTS 하위 쿼리가 정의되지 않은 단일 값을 반환하면 EXISTS가 false로 평가됩니다. 예를 들어 false로 평가하는 다음 쿼리를 고려하십시오.
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


이전 하위 쿼리의 VALUE 키워드가 생략된 경우 쿼리는 true로 평가됩니다.
```sql
SELECT EXISTS (SELECT undefined) 
```

하위 쿼리는 개체에서 선택한 목록의 값 목록을 포함합니다. 선택한 목록에 값이 없는 경우 하위 쿼리는 단일{}값 ''을 반환합니다. 이 값은 정의되므로 EXISTS는 true로 평가합니다.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>예: ARRAY_CONTAINS 다시 작성하고 현재로 조인

ARRAY_CONTAINS 일반적인 사용 사례는 배열에 항목이 있는 것으로 문서를 필터링하는 것입니다. 이 경우 태그 배열에 "orange"라는 항목이 포함되어 있는지 확인합니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS를 사용하도록 동일한 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

또한 ARRAY_CONTAINS 값이 배열 내의 모든 요소와 동일한지 만 확인할 수 있습니다. 배열 속성에 더 복잡한 필터가 필요한 경우 JOIN을 사용합니다.

배열의 단위 및 `nutritionValue` 속성을 기반으로 필터링하는 다음 쿼리를 고려하십시오. 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

컬렉션의 각 문서에 대해 배열 요소로 교차 제품이 수행됩니다. 이 JOIN 작업을 사용하면 배열 내의 속성을 필터링할 수 있습니다. 그러나 이 쿼리의 RU 소비는 중요합니다. 예를 들어 각 배열에 100개의 문서가 있는 경우 1,000 x 100(즉, 100,000) 투플으로 확장됩니다.

EXISTS를 사용하면 이 값비싼 교차 제품을 방지하는 데 도움이 될 수 있습니다.

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

이 경우 EXISTS 하위 쿼리 내의 배열 요소를 필터링합니다. 배열 요소가 필터와 일치하면 이를 투영하고 EXISTS는 true로 평가합니다.

또한 EXISTS별별과 프로젝션에서 참조할 수도 있습니다.

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

쿼리 출력:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>배열 식

ARRAY 식을 사용하여 쿼리 결과를 배열로 프로젝사 만들 수 있습니다. 이 식은 쿼리의 SELECT 절 내에서만 사용할 수 있습니다.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

다른 하위 쿼리와 마찬가지로 ARRAY 식이 있는 필터가 가능합니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

쿼리 출력:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

배열 식은 하위 쿼리에서 FROM 절 다음으로 올 수도 있습니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
