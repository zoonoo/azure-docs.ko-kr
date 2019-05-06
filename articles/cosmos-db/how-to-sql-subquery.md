---
title: Azure Cosmos DB에 대 한 SQL 하위 쿼리
description: SQL 하위 쿼리 및 Azure Cosmos db에서의 일반적인 사용 사례에 대해 알아봅니다
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514801"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 SQL 하위 쿼리 예제

하위 쿼리는 다른 쿼리 내에 중첩 된 쿼리입니다. 내부 쿼리 또는 내부 select 하위 쿼리는 라고도 하며 하위 쿼리를 포함 하는 문에서 일반적으로 외부 쿼리를 호출 합니다.

하위 쿼리는 다음과 같은 두 종류가 있습니다.

* 상호 관련 된-상호 관련 된 하위 쿼리는 외부 쿼리에서 값을 참조 하는 하위 쿼리입니다. 하위 쿼리는 외부 쿼리에 의해 처리 되는 각 행에 대해 한 번 평가 됩니다.

* 비-상관 하위 쿼리는 비-상관 관계가 지정 된 하위 쿼리는 외부 쿼리의 독립적인 이며 외부 쿼리에 의존 하지 않고 자체에서 실행할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 상관된 하위 쿼리를 지원합니다.

## <a name="types-of-subqueries"></a>하위 쿼리 유형

행을 반환 하는 열 번호를 기준으로 하위 쿼리를 추가로 분류할 수 있습니다. 세 가지 종류가 있습니다.
1.  **테이블**: 여러 행 및 여러 열을 반환합니다.
2.  **다중 값**: 여러 행 및 단일 열을 반환합니다.
3.  **스칼라**: 단일 행 및 단일 열을 반환합니다.

> [!NOTE]
> Azure Cosmos DB 다중 값 및 스칼라 하위 쿼리를 지원합니다.

Azure Cosmos DB SQL 쿼리는 항상 단일 열 (간단한 값 또는 복잡 한 문서)를 반환합니다. 따라서 위의 다중값 및 스칼라 하위만 Azure Cosmos DB에 적용 됩니다. 다중 값 하위 쿼리에 사용할 수 있습니다만 FROM 절에서 관계형 식으로 스칼라 하위 쿼리 select에서 스칼라 식 또는 WHERE 절 또는 FROM 절에서 관계형 식으로 사용할 수 있습니다.


## <a name="multi-value-subqueries"></a>다중 값 하위 쿼리

다중 값 하위 쿼리는 문서 집합을 반환할 및 FROM 절 내에서 항상 사용 됩니다. 에 사용 됩니다.

* 조인 식 최적화 
* 비용이 많이 드는 식을 한 번 평가 하 고 여러 번 참조

### <a name="optimize-join-expressions"></a>조인 식 최적화

다중 값 하위 쿼리는 WHERE 절의 모든 크로스 조인 후가 아니라 각 선택 다 식 후 조건자를 푸시하여 조인 식을 최적화할 수 있습니다.

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

이 쿼리에 대 한 인덱스 이름 '병과 수식' 값이 1 보다 큰 용량을 사용 하 여 서비스 항목을 0과 10 사이 영양분이 항목을 포함 된 태그가 있는 모든 문서를 일치 합니다. 그러나 모든 필터를 적용 하기 전에 조인 식 여기는 일치 하는 각 문서에 대 한 태그, nutrients, 및 급 식 배열의 모든 항목의 교차곱 수행 합니다. 그런 다음 WHERE 절 필터 조건자에 각 < c, t, n s > 튜플을 적용 합니다. 예를 들어, 일치 하는 문서 세 배열의 각 항목이 10 개 있으면 확장 1 x 10 x 10 x 10 (예: 1,000) 튜플. 여기서 하위 쿼리를 사용 하 여, 다음 식을 사용 하 여 합류 하기 전에 조인 된 배열의 항목을 필터링 하는 데 도움이 됩니다.

이 쿼리 위와 동일 하지만 하위 쿼리를 활용 합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

첫 번째 쿼리에서 1,000 개 항목 달리 항목 하나만 배열의 항목에 태그를 필터에 대응 하 고 nutrients와 급 식 배열, 조인 식에 대 한 5 개 항목 1 x 1 x 5 x 5 = 25로 확장 됩니다.

### <a name="evaluate-once-and-reference-many-times"></a>한 번 및 참조 횟수 계산

하위 쿼리는 산술 식 또는 사용자 정의 함수 (UDF) 또는 복잡 한 문자열 같은 비용이 많이 드는 식을 사용 하 여 쿼리를 최적화 하는 데 도움이 됩니다. 식을 한 번만 계산 하지만 여러 번 참조 하는 하위 쿼리는 조인 식과 함께 사용할 수 있습니다.

다음 쿼리에서 UDF GetMaxNutritionValue를 두 번 실행합니다.

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

UDF를 한 번만 실행 하는 해당 쿼리는 다음과 같습니다.

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> UDF 수 식은 정의 되지 않은 경우 조인 식의 제품 간 동작을 지정 하면 항상 조인 식 값이 아닌 하위에서 직접 개체를 반환 하 여 단일 행을 생성 하는 확인 해야 합니다.
>

값이 아닌 개체를 반환 하는 유사한 예는 다음과 같습니다.

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

접근 방식은 모든 잠재적으로 비용이 많이 드는 식 아니라, Udf에 제한 되지 않습니다. 예를 들어, 수치 연산 함수 avg와 함께 동일한 접근 방식을 가져올 수 있습니다.

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>외부 참조 데이터를 사용 하 여 연결을 모방 합니다.

종종 단위의 측정값 또는 국가 코드와 같은 자주 변경 되는 정적 데이터를 참조 해야 합니다. 이러한 데이터에 대 한 각 문서에 대 한 복제를 하지는 것이 좋습니다. 이 중복을 방지 저장소에 저장 하 고 문서 크기를 작게 유지 하 여 쓰기 성능이 향상 됩니다. 참조 데이터의 컬렉션을 사용 하 여 내부 조인 의미 체계를 모방 하기 위해 여기서는 하위 쿼리를 사용할 수 있습니다.
예를 들어이 참조 데이터이 집합을 고려해 야 합니다.

| **단위** | **Name**            | **승수** | **기본 단위** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | 그램          |
| µg       | Microgram           | 1.00E-06       | 그램          |
| mg       | Milligram           | 1.00E-03       | 그램          |
| G        | 그램                | 1.00E+00       | 그램          |
| kg       | Kilogram            | 1.00E+03       | 그램          |
| Mg       | Megagram            | 1.00E+06       | 그램          |
| Gg       | Gigagram            | 1.00E+09       | 그램          |
| nJ       | Nanojoule           | 1.00E-09       | 줄         |
| µJ       | Microjoule          | 1.00E-06       | 줄         |
| mJ       | Millijoule          | 1.00E-03       | 줄         |
| J        | 줄               | 1.00E+00       | 줄         |
| kJ       | Kilojoule           | 1.00E+03       | 줄         |
| MJ       | Megajoule           | 1.00E+06       | 줄         |
| GJ       | Gigajoule           | 1.00E+09       | 줄         |
| cal      | 칼로리             | 1.00E+00       | 칼로리       |
| kcal     | 칼로리             | 1.00E+03       | 칼로리       |
| IU       | International 단위 |                |               |


다음 쿼리 단위의 이름을 출력에 추가 되도록이 데이터를 사용 하 여 조인 모방 합니다.

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

스칼라 하위 쿼리 식은 단일 값으로 계산 되는 하위 쿼리입니다. 스칼라 하위 쿼리 식의 값에는 하위 쿼리 프로젝션 (SELECT 절)의 값이입니다.  스칼라 하위 쿼리 식을 사용할 수 있습니다 여러 위치에서 스칼라 식이 유효 합니다. 예를 들어 모든 두 SELECT 식과 WHERE 절에 스칼라 하위 쿼리를 사용할 수 있습니다.
그러나 스칼라 하위 쿼리를 사용 하 여 도움이 되지 않습니다 항상 최적화. 예를 들어, 스칼라 하위 쿼리를 시스템 또는 사용자 정의 함수에 인수로 전달 이점이 없습니다 RU 소비 또는 대기 시간입니다.

스칼라 하위 쿼리를 추가로 분류할 수 있습니다.
* 간단한 식은 스칼라 하위 쿼리
* 집계의 스칼라 하위 쿼리

### <a name="simple-expression-scalar-subqueries"></a>간단한 식은 스칼라 하위 쿼리

간단한 식 스칼라 하위 쿼리는 모든 집계 식을 포함 하지 않는 SELECT 절에 있는 상관된 하위 쿼리 합니다. 이러한 하위 쿼리 컴파일러 큰 하나의 간단한 식으로 변환 하기 때문에 경우 이점은 없고 최적화를 제공 합니다. 내부 및 외부 쿼리 간에 상관 관계가 지정 된 컨텍스트가 없습니다.

다음은 몇 가지 예입니다.

**예 1**

```sql
SELECT 1 AS a, 2 AS b
```

이 쿼리 다시 작성할 수 있습니다, 하는 간단한 식 스칼라 하위 쿼리를 사용 하 여:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

두 쿼리 모두이 출력을 생성 합니다.

```json
[
  { "a": 1, "b": 2 }
]
```

**예 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

이 쿼리 다시 작성할 수 있습니다, 하는 간단한 식 스칼라 하위 쿼리를 사용 하 여:

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

**예 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

이 쿼리 다시 작성할 수 있습니다, 하는 간단한 식 스칼라 하위 쿼리를 사용 하 여:

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

## <a name="aggregate-scalar-subqueries"></a>집계의 스칼라 하위 쿼리

집계는 스칼라 하위 쿼리는 프로젝션 또는 단일 값으로 계산 되는 필터는 집계 함수가 포함 된 하위 쿼리 합니다.

**예제 1:**

해당 프로젝션에 단일 집계 함수 식 사용 하 여 하위 쿼리는 다음과 같습니다.

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

**예 2**

여러 집계 함수 식 사용 하 여 하위 쿼리:

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

**예 3**

프로젝션 및 필터를 모두 집계는 하위 쿼리를 사용 하 여 쿼리 합니다.

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

이 쿼리를 작성 하는 좀 더 최적의 방법은 하위에 참여 하 고 모두 선택의 별칭 하위 쿼리 및 WHERE 절을 참조할 것입니다. 조인 문 내 에서만 및 프로젝션 및 필터에 없는 하위 쿼리를 실행 해야 하기 때문에이 쿼리를 더 효율적입니다.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>EXISTS 식

Azure Cosmos DB는 EXISTS 식을 지원 합니다. Azure Cosmos DB SQL API에 기본 제공 집계 스칼라 하위 쿼리는입니다. EXISTS는 하위 쿼리 식을 사용 하는 하위 쿼리의 모든 행을 반환 하는 경우 true를 반환 하는 부울 식에는 그렇지 않으면 false 반환합니다.
EXISTS를 모두 선택에서 사용할 수 있으므로 부울 식 및 다른 스칼라 식 사이 Azure Cosmos DB SQL API를 구분 하지 않습니다 및 WHERE 절. T-SQL, 부울 식 (예: EXISTS, BETWEEN 및 IN) 필터에 제한 되는 달리입니다.

정의 되지 않은 단일 값을 반환 하는 EXISTS 하위 쿼리를 EXISTS는 false로 계산 됩니다. 예를 들어, false로 계산 되는 다음 쿼리를 고려 합니다.
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


그러나 위의 하위 쿼리에서 값 키워드를 생략 하면 쿼리에서 true로 평가 됩니다.
```sql
SELECT EXISTS (SELECT undefined) 
```

하위 쿼리는 개체의 선택 목록에 값 목록을 묶습니다. 선택한 목록에 값이 없는 경우 하위 쿼리에서 단일 값을 반환 합니다 '{}' true로 평가 되는 정의 되 고 따라서 존재 합니다.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>예제: 재작성 ARRAY_CONTAINS 및와 조인

ARRAY_CONTAINS의 일반적인 사용 사례는 배열에 있는 항목의 존재 여부에 따라 문서를 필터링 하는 것입니다. 이 경우 tags 배열을 라는 항목이 주황색으로 포함 되는지 확인 합니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS를 사용 하도록 동일한 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

또한 ARRAY_CONTAINS은 값 배열 내의 요소와 같은지 여부를 확인할 수만 있습니다. 더 복잡 한 필터 배열 속성에 필요한 경우에 조인이 수행 해야 합니다.

필터 기반 배열의 속성에 nutritionValue 단위에 다음 쿼리를 고려 합니다. 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

각 컬렉션의 문서에 대 한 교차곱을 해당 배열 요소를 사용 하 여 수행 됩니다. 이 조인 작업 수 있도록 배열 내의 속성에 대 한 필터입니다. 그러나이 쿼리의 RU 소비는 중요 한 됩니다. 예를 들어 1,000 개의 문서에서 각 배열 항목 100 개 있으면 확장 됩니다 (예: 100,000) 1,000 x 100 튜플.

사용 하 여 `EXISTS` 이 비용이 많이 드는 교차곱 되지 않도록 합니다.

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

이 경우에서는 EXISTS 하위 쿼리 내에서 배열 요소를 필터링합니다. 배열 요소에는 필터를 일치 경우 프로젝트에서는 및 `EXISTS` true로 평가 합니다.

별칭 EXISTS 수 또한 프로젝션에 참조 해야 합니다.

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

### <a name="array-expression"></a>배열 식

`ARRAY` 식은 배열 쿼리의 결과 프로젝트에 사용할 수 있습니다. 이 식은 쿼리의 SELECT 절 내 에서만 사용할 수 있습니다.

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

다른 하위 쿼리와 마찬가지로 사용 하 여 필터를 `ARRAY` 식 수 있습니다.

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

배열 식 하위 쿼리의 FROM 절 뒤에 가져올 수도 있습니다.

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

- [SQL 쿼리 예제](how-to-sql-query.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [문서 데이터 모델](modeling-data.md)
