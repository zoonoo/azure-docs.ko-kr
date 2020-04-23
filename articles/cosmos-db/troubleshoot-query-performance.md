---
title: Azure Cosmos DB를 사용할 때 쿼리 문제 해결
description: Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870448"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용할 때 쿼리 문제 해결

이 문서에서는 Azure Cosmos DB에서 쿼리 문제를 해결하는 데 권장되는 일반적인 방법을 안내합니다. 이 문서에서 설명하는 단계를 잠재적인 쿼리 문제에 대한 완전한 방어책으로 간주해서는 안 되지만 여기에 가장 일반적인 성능 팁이 포함되어 있습니다. 이 문서를 Azure Cosmos DB 코어(SQL) API에서 느리거나 비용이 많이 드는 쿼리 문제를 해결하는 시작 장소로 사용해야 합니다. [진단 로그를](cosmosdb-monitor-resource-logs.md) 사용하여 느리거나 상당한 양의 처리량을 소비하는 쿼리를 식별할 수도 있습니다.

Azure Cosmos DB에서 쿼리 최적화를 광범위하게 분류할 수 있습니다.

- 쿼리의 RU(요청 단위) 요금을 줄이는 최적화
- 대기 시간을 줄이는 최적화

쿼리의 RU 요금을 줄이면 대기 시간도 거의 확실하게 줄어듭니다.

이 문서에서는 [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합을 사용하여 다시 만들 수 있는 예제를 제공합니다.

## <a name="common-sdk-issues"></a>일반적인 SDK 문제

- 최상의 성능을 위해 [성능 팁을](performance-tips.md)따르십시오.
    > [!NOTE]
    > 성능 향상을 위해 Windows 64비트 호스트 처리를 권장합니다. SQL SDK에는 로컬에서 쿼리를 구문 분석하고 최적화하는 네이티브 ServiceInterop.dll이 포함되어 있습니다. ServiceInterop.dll은 Windows x64 플랫폼에서만 지원됩니다. ServiceInterop.dll을 사용할 수 없는 Linux 및 기타 지원되지 않는 플랫폼의 경우 게이트웨이에 추가 네트워크 호출을 통해 최적화된 쿼리를 가져옵니다.
- 쿼리에 대한 `MaxItemCount` a를 설정할 수 있지만 최소 항목 수를 지정할 수는 없습니다.
    - 코드는 0에서 에 이르는 모든 `MaxItemCount`페이지 크기를 처리해야 합니다.
    - 페이지의 항목 수는 항상 지정된 `MaxItemCount`항목보다 적습니다. 그러나, `MaxItemCount` 엄격 하 게 최대 이며이 금액 보다 더 적은 결과 있을 수 있습니다.
- 이후 페이지에 결과가 있는 경우에도 쿼리에 빈 페이지가 있을 수 있습니다. 그 이유는 다음과 같은 것일 수 있습니다.
    - SDK에서 여러 네트워크 호출을 수행할 수 있습니다.
    - 쿼리가 문서를 검색하는 데 시간이 오래 걸릴 수 있습니다.
- 모든 쿼리에는 쿼리를 계속할 수 있는 연속 토큰이 있습니다. 쿼리를 완전히 드레인해야 합니다. SDK 샘플을 살펴보고 루프를 `while` `FeedIterator.HasMoreResults` 사용하여 전체 쿼리를 드레인합니다.

## <a name="get-query-metrics"></a>쿼리 메트릭 받기

Azure Cosmos DB에서 쿼리를 최적화하는 경우 첫 번째 단계는 항상 쿼리에 대한 [쿼리 메트릭을 얻는](profile-sql-api-query.md) 것입니다. 이러한 메트릭은 Azure 포털에서도 사용할 수 있습니다.

[![쿼리 메트릭](./media/troubleshoot-query-performance/obtain-query-metrics.png) 얻기](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

쿼리 메트릭을 가져온 후 검색된 문서 수와 쿼리의 출력 문서 수를 비교합니다. 이 비교를 사용하여 이 문서에서 검토할 관련 섹션을 식별합니다.

검색된 문서 수는 쿼리가 로드하는 데 필요한 문서 수입니다. 출력 문서 수는 쿼리 결과에 필요한 문서 수입니다. 검색된 문서 수가 출력 문서 수보다 훨씬 높으면 인덱스를 사용할 수 없고 검사를 수행해야 하는 쿼리 부분이 하나 이상 있었습니다.

시나리오에 대한 관련 쿼리 최적화를 이해하려면 다음 섹션을 참조하십시오.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 RU 요금이 너무 높습니다.

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>검색된 문서 수는 출력 문서 수보다 훨씬 높습니다.

- [인덱싱 정책에 필요한 경로를 포함합니다.](#include-necessary-paths-in-the-indexing-policy)

- [인덱스를 사용하는 시스템 함수를 이해합니다.](#understand-which-system-functions-use-the-index)

- [인덱스를 사용하는 집계 쿼리를 이해합니다.](#understand-which-aggregate-queries-use-the-index)

- [필터와 ORDER BY 절이 모두 있는 쿼리를 수정합니다.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [하위 쿼리를 사용하여 JOIN 식을 최적화합니다.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색된 문서 수는 출력 문서 수와 거의 같습니다.

- [교차 파티션 쿼리를 피하십시오.](#avoid-cross-partition-queries)

- [여러 속성에 필터가 있는 쿼리를 최적화합니다.](#optimize-queries-that-have-filters-on-multiple-properties)

- [필터와 ORDER BY 절이 모두 있는 쿼리를 수정합니다.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 RU 요금은 허용되지만 대기 시간이 여전히 너무 높습니다.

- [근접성을 향상시킵니다.](#improve-proximity)

- [프로비저닝된 처리량을 늘립니다.](#increase-provisioned-throughput)

- [최대 동시성을 늘립니다.](#increase-maxconcurrency)

- [최대 버퍼드항목 수를 늘립니다.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색된 문서 수가 출력 문서 수를 초과하는 쿼리

 검색된 문서 수는 쿼리가 로드하는 데 필요한 문서 수입니다. 출력 문서 수는 쿼리 결과에 필요한 문서 수입니다. 검색된 문서 수가 출력 문서 수보다 훨씬 높으면 인덱스를 사용할 수 없고 검사를 수행해야 하는 쿼리 부분이 하나 이상 있었습니다.

다음은 인덱스에서 완전히 제공되지 않은 검색 쿼리의 예입니다.

쿼리:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

쿼리 메트릭:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

검색된 문서 수(60,951)는 출력 문서 수(7)보다 훨씬 높기 때문에 이 쿼리는 검사를 수행해야 합니다. 이 경우 시스템 함수 [UPPER()는](sql-query-upper.md) 인덱스를 사용하지 않습니다.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>인덱싱 정책에 필요한 경로 포함

인덱싱 정책은 절, `WHERE` `ORDER BY` 절 `JOIN`및 대부분의 시스템 함수에 포함된 모든 속성을 포함해야 합니다. 인덱스 정책에 지정된 경로는 JSON 문서의 속성과 일치해야 합니다(대/소문자 구분).

[영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대한 간단한 쿼리를 실행하는 경우 `WHERE` 절의 속성이 인덱싱될 때 훨씬 낮은 RU 요금이 표시됩니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 충전:** 409.51 RU

#### <a name="optimized"></a>최적화됨

업데이트된 인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 요금:** 2.98 RU

언제든지 인덱싱 정책에 속성을 추가할 수 있으며 쓰기 가용성이나 성능에는 영향을 주지 않습니다. 인덱스에 새 속성을 추가 하는 경우 속성을 사용 하는 쿼리는 즉시 사용 가능한 새 인덱스를 사용 합니다. 쿼리는 빌드되는 동안 새 인덱스를 사용합니다. 따라서 인덱스 재생성 진행 중에는 쿼리 결과가 일치하지 않을 수 있습니다. 새 속성이 인덱싱된 경우 인덱스 를 다시 작성하는 동안 기존 인덱스만 사용하는 쿼리는 영향을 받지 않습니다. 인덱스 [변환 진행률을 추적할](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)수 있습니다.

### <a name="understand-which-system-functions-use-the-index"></a>인덱스를 사용하는 시스템 기능 이해

식을 문자열 값범위로 변환할 수 있는 경우 인덱스를 사용할 수 있습니다. 그렇지 않으면 할 수 없습니다.

다음은 인덱스를 사용할 수 있는 몇 가지 일반적인 문자열 함수 목록입니다.

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING (str_expr, num_expr, num_expr) = str_expr,하지만 첫 번째 num_expr 0인 경우에만

다음은 인덱스를 사용하지 않고 각 문서를 로드해야 하는 몇 가지 일반적인 시스템 함수입니다.

| **시스템 기능**                     | **최적화를 위한 아이디어**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 전체 텍스트 검색에 Azure 검색을 사용합니다.                        |
| 상부/하부                             | 비교를 위해 데이터를 정규화하기 위해 시스템 함수를 사용하는 대신 삽입 시 케이싱을 정규화합니다. 와 같은 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'```쿼리가 됩니다. |
| 수학 함수(비집계) | 쿼리에서 값을 자주 계산해야 하는 경우 JSON 문서에 값을 속성으로 저장하는 것이 좋습니다. |

------

시스템 기능이 작동하지 않더라도 쿼리의 다른 부분에서도 인덱스를 사용할 수 있습니다.

### <a name="understand-which-aggregate-queries-use-the-index"></a>인덱스를 사용하는 집계 쿼리 이해

대부분의 경우 Azure Cosmos DB의 집계 시스템 함수는 인덱스를 사용합니다. 그러나 집계 쿼리의 필터 또는 추가 절에 따라 많은 수의 문서를 로드해야 할 수 있습니다. 일반적으로 쿼리 엔진은 먼저 같음 및 범위 필터를 적용합니다. 이러한 필터를 적용한 후 쿼리 엔진은 추가 필터를 평가하고 필요한 경우 나머지 문서를 로드하여 집계를 계산할 수 있습니다.

예를 들어 이러한 두 샘플 쿼리를 감안할 때 `CONTAINS` 같음 및 시스템 함수 필터가 모두 `CONTAINS` 있는 쿼리는 일반적으로 시스템 함수 필터만 있는 쿼리보다 효율적입니다. 이는 같음 필터가 먼저 적용되고 더 비싼 `CONTAINS` 필터에 대해 문서를 로드하기 전에 인덱스를 사용하기 때문입니다.

필터만 `CONTAINS` 있는 쿼리 - RU 요금이 더 높습니다.

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

같음 필터와 `CONTAINS` 필터를 모두 사용할 수 있는 쿼리 - RU 요금 절감:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

다음은 인덱스를 완전히 사용하지 않는 집계 쿼리의 추가 예입니다.

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>인덱스를 사용하지 않는 시스템 함수가 있는 쿼리

인덱스를 사용하는지 확인하려면 관련 [시스템 함수의 페이지를](sql-query-system-functions.md) 참조해야 합니다.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>사용자 정의 함수(UDF)로 쿼리 집계

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>그룹 별 쿼리

`GROUP BY` 절의 속성의 `GROUP BY` 카디널리티가 증가함에 따라 RU 요금이 증가합니다. 이 예제에서는 RU 요금이 높을 것으로 예상되므로 쿼리 엔진은 필터와 `c.foodGroup = "Sausages and Luncheon Meats"` 일치하는 모든 문서를 로드해야 합니다.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

동일한 집계 쿼리를 자주 실행하려는 경우 개별 쿼리를 실행하는 것보다 [Azure Cosmos DB 변경 피드를](change-feed.md) 사용하여 실시간 구체화된 보기를 작성하는 것이 더 효율적일 수 있습니다.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>필터와 ORDER BY 절이 모두 있는 쿼리 수정

필터와 `ORDER BY` 절이 있는 쿼리는 일반적으로 범위 인덱스를 사용하지만 복합 인덱스에서 제공될 수 있는 경우 더 효율적입니다. 인덱싱 정책을 수정하는 것 외에도 복합 인덱스의 모든 속성을 `ORDER BY` 절에 추가해야 합니다. 이렇게 쿼리를 변경하면 복합 인덱스를 사용할 수 있습니다.  [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대한 쿼리를 실행하여 영향을 관찰할 수 있습니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

인덱싱 정책:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 충전:** 44.28 RU

#### <a name="optimized"></a>최적화됨

업데이트된 쿼리(절에 `ORDER BY` 두 속성을 모두 포함) :

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

업데이트된 인덱싱 정책:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 요금:** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>하위 쿼리를 사용하여 JOIN 식 최적화
다중 값 하위 쿼리는 절에서 모든 교차 조인이 아닌 선택-다수 식 다음에 조건자 푸시를 통해 식을 최적화할 `JOIN` 수 있습니다. `WHERE`

다음 쿼리를 살펴보십시오.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 충전:** 167.62 RU

이 쿼리의 경우 인덱스는 태그가 있는 문서와 "유아식", 영양값이 0보다 크고 서빙 량이 1보다 큰 모든 문서와 일치합니다. 여기서 `JOIN` 식은 필터를 적용하기 전에 각 일치 문서에 대한 태그, 영양소 및 서빙 배열의 모든 항목의 교차 곱을 수행합니다. 그런 `WHERE` 다음 절은 각 `<c, t, n, s>` 튜플에 필터 조건어를 적용합니다.

예를 들어 일치하는 문서에 세 개의 배열 각각에 10개의 항목이 있는 경우 1 x 10 x 10 x 10(즉, 1,000) 투플으로 확장됩니다. 여기서 하위 쿼리를 사용하면 다음 식으로 조인하기 전에 조인된 배열 항목을 필터링하는 데 도움이 될 수 있습니다.

이 쿼리는 앞의 쿼리와 동일하지만 하위 쿼리를 사용합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 요금:** 22.17 RU

태그 배열의 항목 하나만 필터와 일치하고 영양소와 서빙 배열모두에 대해 5개의 항목이 있다고 가정합니다. 식은 `JOIN` 첫 번째 쿼리에서 1,000개의 항목이 아닌 1 x 1 x 5 x 5 = 25항목으로 확장됩니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색된 문서 수가 출력 문서 수와 동일한 쿼리

검색된 문서 수가 출력 문서 수와 거의 같으면 쿼리에서 불필요한 문서를 많이 검색할 필요가 없습니다. TOP 키워드를 사용하는 쿼리와 같은 많은 쿼리의 경우 검색된 문서 수가 출력 문서 수를 1로 초과할 수 있습니다. 당신은 이것에 대해 걱정할 필요가 없습니다.

### <a name="avoid-cross-partition-queries"></a>교차 파티션 쿼리 방지

Azure Cosmos DB는 [분할을](partitioning-overview.md) 사용하여 요청 단위 및 데이터 저장소 요구가 증가함에 따라 개별 컨테이너를 확장합니다. 각 물리적 파티션에는 별도의 독립 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인해야 합니다. 이 최적화는 쿼리에 필요한 총 루수를 줄입니다.

프로비저닝된 RU(30,000개 이상) 또는 많은 양의 데이터가 저장된 경우(약 100GB 이상) 쿼리 RU 요금이 크게 감소할 수 있는 충분한 컨테이너가 있을 수 있습니다.

예를 들어 파티션 키 foodGroup을 사용하여 컨테이너를 만드는 경우 다음 쿼리는 단일 물리적 파티션만 확인해야 합니다.

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

이러한 쿼리는 쿼리에 파티션 키를 추가하여 최적화됩니다.

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

파티션 키에 범위 필터가 있거나 파티션 키에 필터가 없는 쿼리는 모든 실제 파티션의 인덱스에 결과를 확인해야 합니다.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>여러 속성에 필터가 있는 쿼리 최적화

여러 속성에 필터가 있는 쿼리는 일반적으로 범위 인덱스를 사용하지만 복합 인덱스에서 제공될 수 있는 경우 더 효율적입니다. 소량의 데이터의 경우 이 최적화는 큰 영향을 미치지 않습니다. 그러나 많은 양의 데이터에 유용할 수 있습니다. 복합 인덱스당 하나의 비같음 필터만 최적화할 수 있습니다. 쿼리에 같지 않은 필터가 여러 개 있는 경우 복합 인덱스를 사용할 필터 중 하나를 선택합니다. 나머지는 범위 인덱스를 계속 사용합니다. 비같음 필터는 복합 인덱스에서 마지막으로 정의되어야 합니다. [복합 인덱스에 대해 자세히 알아봅니다.](index-policy.md#composite-indexes)

다음은 복합 인덱스로 최적화할 수 있는 쿼리의 몇 가지 예입니다.

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

관련 복합 인덱스는 다음과 같습니다.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>쿼리 대기 시간을 줄이는 최적화

대부분의 경우 쿼리 대기 시간이 여전히 너무 높으면 RU 요금이 허용될 수 있습니다. 다음 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁에 대한 개요를 제공합니다. 동일한 데이터 집합에서 동일한 쿼리를 여러 번 실행하는 경우 매번 동일한 RU 요금이 청구됩니다. 그러나 쿼리 대기 시간은 쿼리 실행마다 다를 수 있습니다.

### <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정과 다른 지역에서 실행되는 쿼리는 동일한 리전 내에서 실행된 경우보다 대기 시간이 더 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내의 가상 컴퓨터에서 온 경우보다 대기 시간이 수십 또는 수백 밀리초 이상일 것으로 예상해야 합니다. [Azure Cosmos DB에서 데이터를 전역적으로 배포하여](distribute-data-globally.md) 데이터를 앱에 더 가깝게 가져올 수 있도록 하는 것은 간단합니다.

### <a name="increase-provisioned-throughput"></a>프로비저닝된 처리량 증가

Azure Cosmos DB에서 프로비저닝된 처리량은 요청 단위(R)로 측정됩니다. 처리량의 5 R을 소비하는 쿼리가 있다고 가정해 보냅니다. 예를 들어 1,000개의 R을 프로비전하는 경우 해당 쿼리를 초당 200회 실행할 수 있습니다. 사용 가능한 처리량이 충분하지 않을 때 쿼리를 실행하려고 하면 Azure Cosmos DB에서 HTTP 429 오류를 반환합니다. 현재 코어(SQL) API SDK는 짧은 시간 동안 기다린 후 이 쿼리를 자동으로 다시 시도합니다. 제한된 요청은 시간이 오래 걸리므로 프로비저닝된 처리량을 늘리면 쿼리 대기 시간이 향상될 수 있습니다. Azure 포털의 메트릭 블레이드에서 [제한된 요청의 총 수를](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) 관찰할 수 **있습니다.**

### <a name="increase-maxconcurrency"></a>최대 동시성 증가

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 직렬로 가져옵니다. 따라서 MaxConcurrency를 파티션 수로 설정하면 다른 모든 시스템 조건이 동일하게 유지되는 경우 가장 성능이 뛰어난 쿼리를 얻을 수 있습니다. 파티션 수를 모르는 경우 MaxConcurrency(또는 이전 SDK 버전의 MaxDegreesOfParallelism)를 높은 숫자로 설정할 수 있습니다. 시스템은 최소(파티션 수, 사용자가 제공한 입력)를 최대 병렬 처리 수준으로 선택합니다.

### <a name="increase-maxbuffereditemcount"></a>최대 버퍼드항목 수 증가

쿼리는 현재 결과 일괄 처리가 클라이언트에서 처리되는 동안 결과를 미리 가져오도록 설계되었습니다. 사전 페칭은 쿼리의 전체 대기 시간을 개선하는 데 도움이 됩니다. MaxBufferedItemCount 설정은 미리 가져온 결과 수를 제한합니다. 이 값을 반환된 예상 결과 수(또는 더 높은 수)로 설정하면 쿼리가 사전 페칭을 통해 가장 많은 이점을 얻을 수 있습니다. 이 값을 -1로 설정하면 시스템이 버퍼링할 항목 수를 자동으로 결정합니다.

## <a name="next-steps"></a>다음 단계
쿼리당 R을 측정하는 방법, 쿼리를 조정하는 실행 통계 를 얻는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [.NET SDK를 사용하여 SQL 쿼리 실행 메트릭 받기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](sql-api-sql-query-metrics.md)
* [.NET SDK용 성능 팁](performance-tips.md)
