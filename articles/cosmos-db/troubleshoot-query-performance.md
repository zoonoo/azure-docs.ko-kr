---
title: Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결
description: Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b3c6926f17e8378fd3b53bfd59a7c5ea8141adb4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097237"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결

이 문서에서는 Azure Cosmos DB의 쿼리 문제를 해결 하기 위한 일반적인 권장 방법을 안내 합니다. 이 문서에 설명 된 단계는 잠재적 쿼리 문제에 대 한 완전 한 방어를 고려 하지 않지만 여기에는 가장 일반적인 성능 팁이 포함 되어 있습니다. 이 문서를 사용 하 여 Azure Cosmos DB core (SQL) API에서 속도가 느리거나 비용이 많이 드는 쿼리 문제를 해결할 수 있습니다. [진단 로그](cosmosdb-monitor-resource-logs.md) 를 사용 하 여 속도가 느리거나 처리량을 많이 소비 하는 쿼리를 식별할 수도 있습니다.

Azure Cosmos DB에서 쿼리 최적화를 광범위 하 게 분류 할 수 있습니다.

- 쿼리의 요청 단위 () 비용을 줄이는 최적화
- 대기 시간을 단축 하는 최적화

쿼리 비용을 줄이는 경우에도 대기 시간이 거의 줄어듭니다.

이 문서에서는 [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합을 사용 하 여 다시 만들 수 있는 예제를 제공 합니다.

## <a name="common-sdk-issues"></a>일반적인 SDK 문제

이 가이드를 읽기 전에 쿼리 엔진과 관련이 없는 일반적인 SDK 문제를 고려 하는 것이 좋습니다.

- 최상의 성능을 위해 다음 [성능 팁](performance-tips.md)을 따르세요.
    > [!NOTE]
    > 성능을 향상 시키려면 Windows 64 비트 호스트를 처리 하는 것이 좋습니다. SQL SDK에는 로컬에서 쿼리를 구문 분석 하 고 최적화 하는 네이티브 ServiceInterop .dll이 포함 되어 있습니다. ServiceInterop .dll은 Windows x64 플랫폼 에서만 지원 됩니다. ServiceInterop .dll을 사용할 수 없는 Linux 및 기타 지원 되지 않는 플랫폼의 경우 추가 네트워크 호출을 수행 하 여 최적화 된 쿼리를 가져옵니다.
- SDK를 사용 하면 쿼리에 `MaxItemCount` 를 설정할 수 있지만 최소 항목 수를 지정할 수는 없습니다.
    - 코드는 0부터까지 페이지 크기를 처리 해야 합니다 `MaxItemCount`.
    - 페이지의 항목 수는 항상 지정 `MaxItemCount`된 보다 작거나 같습니다. 그러나은 `MaxItemCount` (는) 엄격히 말해서이 크기 보다 결과가 적을 수 있습니다.
- 이후 페이지에 결과가 있는 경우에도 쿼리에 빈 페이지가 있을 수 있습니다. 이에 대 한 이유는 다음과 같습니다.
    - SDK에서 여러 네트워크 호출을 수행할 수 있습니다.
    - 쿼리가 문서를 검색 하는 데 시간이 오래 걸릴 수 있습니다.
- 모든 쿼리에는 쿼리를 계속 하는 데 사용할 수 있는 연속 토큰이 있습니다. 쿼리를 완전히 드레이닝 해야 합니다. SDK 샘플을 살펴보고의 `while` `FeedIterator.HasMoreResults` 루프를 사용 하 여 전체 쿼리를 드레이닝 합니다.

## <a name="get-query-metrics"></a>쿼리 메트릭 가져오기

Azure Cosmos DB에서 쿼리를 최적화 하는 경우 첫 번째 단계는 항상 쿼리에 대 한 [쿼리 메트릭을 가져오는](profile-sql-api-query.md) 것입니다. 이러한 메트릭은 Azure Portal 에서도 사용할 수 있습니다. 데이터 탐색기에서 쿼리를 실행 하면 쿼리 메트릭이 **결과** 탭 옆에 표시 됩니다.

[![쿼리 메트릭](./media/troubleshoot-query-performance/obtain-query-metrics.png) 가져오기](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

쿼리 메트릭을 가져온 후에는 **검색 된 문서 수** 를 쿼리의 **출력 문서 수** 와 비교 합니다. 이 비교를 사용 하 여이 문서에서 검토할 관련 섹션을 확인할 수 있습니다.

**검색 된 문서** 수는 쿼리 엔진에서 로드 하는 데 필요한 문서 수입니다. **출력 문서 수** 는 쿼리 결과에 필요한 문서 수입니다. 검색 된 **문서 수가** **출력 문서**수보다 크게 높으면 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

시나리오에 대 한 관련 쿼리 최적화를 이해 하려면 다음 섹션을 참조 하십시오.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 과도 한 요금이 너무 높음

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>검색 된 문서 수가 출력 문서 수보다 크게 높습니다.

- [인덱싱 정책에 필요한 경로를 포함 합니다.](#include-necessary-paths-in-the-indexing-policy)

- [인덱스를 사용 하는 시스템 함수를 파악 합니다.](#understand-which-system-functions-use-the-index)

- [인덱스를 사용 하는 집계 쿼리를 파악 합니다.](#understand-which-aggregate-queries-use-the-index)

- [필터와 ORDER BY 절을 모두 포함 하는 쿼리를 최적화 합니다.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [하위 쿼리를 사용 하 여 조인 식을 최적화 합니다.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색 된 문서 수가 출력 문서 수와 거의 동일 합니다.

- [파티션 간 쿼리를 최소화 합니다.](#minimize-cross-partition-queries)

- [여러 속성에 대 한 필터가 있는 쿼리를 최적화 합니다.](#optimize-queries-that-have-filters-on-multiple-properties)

- [필터와 ORDER BY 절을 모두 포함 하는 쿼리를 최적화 합니다.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 과도 한 요금이 허용 되지만 대기 시간이 너무 높습니다.

- [근접성을 개선 합니다.](#improve-proximity)

- [프로 비전 된 처리량을 늘립니다.](#increase-provisioned-throughput)

- [MaxConcurrency를 늘립니다.](#increase-maxconcurrency)

- [MaxBufferedItemCount를 늘립니다.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색 된 문서 개수가 출력 문서 수를 초과 하는 쿼리

 **검색 된 문서** 수는 쿼리 엔진에서 로드 하는 데 필요한 문서 수입니다. **출력 문서 수** 는 쿼리에서 반환 된 문서 수입니다. 검색 된 **문서 수가** **출력 문서**수보다 크게 높으면 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

인덱스에 의해 완전히 처리 되지 않은 검색 쿼리의 예는 다음과 같습니다.

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

**검색 된 문서 수** (60951)는이 쿼리가 문서 스캔을 생성 하는 것을 의미 하는 **출력 문서 수** (7) 보다 훨씬 더 높습니다. 이 경우 시스템 함수 [UPPER ()](sql-query-upper.md) 는 인덱스를 사용 하지 않습니다.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>인덱싱 정책에 필요한 경로를 포함 합니다.

인덱싱 정책은 절, `WHERE` `ORDER BY` 절, `JOIN`및 대부분의 시스템 함수에 포함 된 모든 속성을 포함 해야 합니다. 인덱스 정책에 지정 된 원하는 경로는 JSON 문서의 속성과 일치 해야 합니다.

> [!NOTE]
> Azure Cosmos DB 인덱싱 정책의 속성은 대/소문자를 구분 합니다.

[영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대해 다음과 같은 간단한 쿼리를 실행 하는 경우 `WHERE` 절의 속성이 인덱싱되는 경우 매우 낮은 요금이 발생 합니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

**작업 요금:** 409.51 RUs

#### <a name="optimized"></a>최적화됨

업데이트 된 인덱싱 정책:

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

**작업 요금:** 2.98 RUs

쓰기 가용성 또는 성능에 영향을 주지 않고 언제 든 지 인덱싱 정책에 속성을 추가할 수 있습니다. 인덱스에 새 속성을 추가 하는 경우 속성을 사용 하는 쿼리는 즉시 사용 가능한 새 인덱스를 사용 합니다. 쿼리를 작성 하는 동안 새 인덱스를 사용 합니다. 따라서 인덱스를 다시 작성 하는 동안 쿼리 결과가 일치 하지 않을 수 있습니다. 새 속성이 인덱싱되는 경우 인덱스를 다시 작성 하는 동안 기존 인덱스만 사용 하는 쿼리는 영향을 받지 않습니다. [인덱스 변환 진행률을 추적할](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)수 있습니다.

### <a name="understand-which-system-functions-use-the-index"></a>인덱스를 사용 하는 시스템 함수 이해

식의 범위를 문자열 값으로 변환할 수 있는 경우 인덱스를 사용할 수 있습니다. 그렇지 않으면 사용할 수 없습니다.

인덱스를 사용할 수 있는 몇 가지 일반적인 문자열 함수 목록은 다음과 같습니다.

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING (str_expr, num_expr, num_expr) = str_expr 이지만 첫 번째 num_expr 0 인 경우에만

인덱스를 사용 하지 않으며 각 문서를 로드 해야 하는 몇 가지 일반적인 시스템 함수는 다음과 같습니다.

| **시스템 함수**                     | **최적화에 대 한 아이디어**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 전체 텍스트 검색에 Azure Search를 사용 합니다.                        |
| 위쪽/아래쪽                             | 비교를 위해 시스템 함수를 사용 하 여 데이터를 정규화 하는 대신 삽입 시 대/소문자를 정규화 합니다. 과 같은 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` 쿼리는 ```SELECT * FROM c WHERE c.name = 'BOB'```가 됩니다. |
| 수치 연산 함수 (비 집계) | 쿼리에서 값을 자주 계산 해야 하는 경우 JSON 문서에 값을 속성으로 저장 하는 것이 좋습니다. |

------

시스템 함수에서 사용 하지 않는 경우에도 쿼리의 다른 부분은 인덱스를 계속 사용할 수 있습니다.

### <a name="understand-which-aggregate-queries-use-the-index"></a>인덱스를 사용 하는 집계 쿼리 이해

대부분의 경우 Azure Cosmos DB 집계 시스템 함수는 인덱스를 사용 합니다. 그러나 집계 쿼리의 필터 또는 추가 절에 따라 쿼리 엔진은 많은 수의 문서를 로드 해야 할 수 있습니다. 일반적으로 쿼리 엔진은 같음 및 범위 필터를 먼저 적용 합니다. 이러한 필터를 적용 한 후 쿼리 엔진은 추가 필터를 평가 하 고 필요한 경우 집계를 계산 하기 위해 나머지 문서를 로드할 수 있습니다.

예를 들어 이러한 두 개의 샘플 쿼리가 있는 경우 같음 및 `CONTAINS` 시스템 함수 필터를 모두 포함 하는 쿼리는 일반적으로 `CONTAINS` 시스템 함수 필터를 사용 하는 쿼리보다 더 효율적입니다. 이는 같음 필터가 먼저 적용 되 고 더 비싼 `CONTAINS` 필터를 위해 문서를 로드 해야 하기 전에 인덱스를 사용 하기 때문입니다.

`CONTAINS` 필터만 사용 하 여 쿼리-높은 수준의 요금이 부과 됩니다.

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

같음 필터와 `CONTAINS` 필터를 모두 사용 하 여 쿼리-더 낮은 작업 요금:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

인덱스를 완전히 사용 하지 않을 집계 쿼리의 추가 예는 다음과 같습니다.

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>인덱스를 사용 하지 않는 시스템 함수를 사용 하 여 쿼리

관련 [시스템 함수의 페이지](sql-query-system-functions.md) 를 참조 하 여 인덱스를 사용 하는지 확인 해야 합니다.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>UDF (사용자 정의 함수)를 사용 하 여 쿼리 집계

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>GROUP BY를 사용 하는 쿼리

절에 있는 속성의 카디널리티가 `GROUP BY` 증가 함에 따라 쿼리를 사용 하는 것이 증가 합니다. `GROUP BY` 예를 들어 아래 쿼리에서는 고유 설명 수가 증가 함에 따라 쿼리의 배 요금이 증가 합니다.

`GROUP BY` 절을 사용 하 여 집계 함수를 사용 하는 것은 집계 함수에 대 한 유일한 요금 보다 높습니다. 이 예에서는 쿼리 엔진이 `c.foodGroup = "Sausages and Luncheon Meats"` 필터와 일치 하는 모든 문서를 로드 해야 하므로,

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

동일한 집계 쿼리를 자주 실행 하려는 경우 개별 쿼리를 실행 하는 것 보다 [Azure Cosmos DB 변경 피드](change-feed.md) 를 사용 하 여 실시간 구체화 뷰를 작성 하는 것이 더 효율적일 수 있습니다.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>필터 및 ORDER BY 절이 모두 있는 쿼리 최적화

필터 및 `ORDER BY` 절이 있는 쿼리는 일반적으로 범위 인덱스를 사용 하지만 복합 인덱스에서 제공 될 수 있는 경우 더 효율적입니다. 인덱싱 정책을 수정 하는 것 외에도 복합 인덱스의 모든 속성을 `ORDER BY` 절에 추가 해야 합니다. 이러한 쿼리에 대 한 변경 내용은 복합 인덱스를 사용 하도록 합니다.  [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 집합에 대해 쿼리를 실행 하 여 영향을 관찰할 수 있습니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

**작업 요금:** 44.28 RUs

#### <a name="optimized"></a>최적화됨

업데이트 된 쿼리 ( `ORDER BY` 절의 두 속성 포함):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

업데이트 된 인덱싱 정책:

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

**작업 요금:** 8.86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>하위 쿼리를 사용 하 여 조인 식 최적화

다중값 하위 쿼리는 `JOIN` `WHERE` 절의 모든 크로스 조인이 아니라 각 select-many 식 다음에 조건자를 푸시하여 식을 최적화할 수 있습니다.

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

**작업 요금:** 167.62 RUs

이 쿼리의 경우 인덱스는 이름이 `infant formula`0 보다 `nutritionValue` 크고 1 보다 `amount` 큰 태그가 있는 모든 문서와 일치 합니다. 여기 `JOIN` 에 있는 식은 필터를 적용 하기 전에 일치 하는 각 문서에 대 한 태그, nutrients 및 배열 항목의 모든 항목에 대 한 교차곱을 수행 합니다. 그러면 `WHERE` 절이 각 `<c, t, n, s>` 튜플에 필터 조건자를 적용 합니다.

예를 들어, 일치 하는 문서에 각각 3 개의 배열에 10 개의 항목이 있는 경우 1 x 10 x 10 x 10 (즉, 1000) 튜플로 확장 됩니다. 여기서 하위 쿼리를 사용 하 여 다음 식으로 조인 하기 전에 조인 된 배열 항목을 필터링 할 수 있습니다.

이 쿼리는 위의 쿼리와 동일 하지만 하위 쿼리를 사용 합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**작업 요금:** 22.17 RUs

태그 배열의 한 항목만 필터와 일치 하 고 nutrients 및 servings 배열 모두에 대해 5 개의 항목이 있다고 가정 합니다. 첫 `JOIN` 번째 쿼리의 1000 항목과 달리 식은 1 x 1 x 5 x 5 항목으로 확장 됩니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색 된 문서 수가 출력 문서 수와 같은 쿼리

검색 된 **문서 수가** **출력 문서 수**와 거의 같으면 쿼리 엔진은 불필요 한 많은 문서를 검색할 필요가 없습니다. 키워드를 사용 하는 것과 같은 많은 쿼리에서는 **검색 된 문서 수가** 1로 **출력 문서 수** 를 초과할 수 있습니다. `TOP` 이에 대해 걱정 하지 않아도 됩니다.

### <a name="minimize-cross-partition-queries"></a>파티션 간 쿼리 최소화

Azure Cosmos DB에서는 [분할](partitioning-overview.md) 을 사용 하 여 개별 컨테이너를 요청 단위로 확장 하 고 데이터 저장소를 증가 시켜야 합니다. 각 실제 파티션에는 별도의 독립 된 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치 하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인 해야 합니다. 이러한 최적화를 통해 쿼리에 필요한 RUs의 총 수가 줄어듭니다.

프로 비전 된 RUs (3만 이상) 또는 대용량의 데이터가 저장 되어 있는 경우 (약 100 GB 초과) 쿼리를 크게 줄일 수 있을 만큼 충분 한 컨테이너가 있을 수 있습니다.

예를 들어 파티션 키가 foodGroup 인 컨테이너를 만드는 경우 다음 쿼리는 단일 실제 파티션만 확인 해야 합니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

파티션 키가 있는 `IN` 필터를 포함 하는 쿼리는 관련 실제 파티션만 확인 하 고 "팬 아웃" 하지 않습니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

파티션 키에 대해 범위 필터를 포함 하거나 파티션 키에 필터가 없는 쿼리는 "팬 아웃" 하 고 결과에 대 한 모든 물리적 파티션의 인덱스를 확인 해야 합니다.

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>여러 속성에 대 한 필터가 있는 쿼리 최적화

여러 속성에 대 한 필터를 포함 하는 쿼리는 일반적으로 범위 인덱스를 사용 하지만 복합 인덱스에서 제공 될 수 있는 경우 더 효율적입니다. 적은 양의 데이터에 대해 이러한 최적화는 상당한 영향을 주지 않습니다. 그러나 많은 양의 데이터에 대해 유용 합니다. 복합 인덱스 당 최대 하나의 일치 하지 않는 필터만 최적화할 수 있습니다. 쿼리에 일치 하지 않는 필터가 여러 개 있는 경우 복합 인덱스를 사용할 하나를 선택 합니다. 나머지는 범위 인덱스를 계속 사용 합니다. 같지 않음 필터는 복합 인덱스에서 마지막으로 정의 되어야 합니다. [복합 인덱스에 대해 자세히 알아보세요](index-policy.md#composite-indexes).

다음은 복합 인덱스를 사용 하 여 최적화할 수 있는 쿼리의 몇 가지 예입니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

다음은 관련 복합 인덱스입니다.

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

대부분의 경우 쿼리 대기 시간이 너무 높은 경우에는 항상 사용할 수 있는 요금이 부과 될 수 있습니다. 다음 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁의 개요를 제공 합니다. 동일한 쿼리를 동일한 데이터 집합에서 여러 번 실행 하는 경우에는 매번 동일한 실행 요금이 발생 합니다. 하지만 쿼리 대기 시간은 쿼리 실행 마다 다를 수 있습니다.

### <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정이 아닌 다른 지역에서 실행 되는 쿼리는 동일한 지역 내에서 실행 된 경우 보다 대기 시간이 더 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행 하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내에서 가상 컴퓨터의 가상 컴퓨터에서 발생 한 것 보다 수십 또는 수백 밀리초 (또는 그 이상)로 표시 되어야 합니다. 데이터를 앱에 더 가깝게 가져올 수 있도록 [Azure Cosmos DB 데이터를 전역적으로 배포](distribute-data-globally.md) 하는 것이 간단 합니다.

### <a name="increase-provisioned-throughput"></a>프로 비전 된 처리량 증가

Azure Cosmos DB에서 프로 비전 된 처리량은 RUs (요청 단위)로 측정 됩니다. 5 개의 처리량 RUs를 사용 하는 쿼리가 있다고 가정 합니다. 예를 들어 1000 RUs를 프로 비전 하는 경우 초당 해당 200 쿼리를 실행할 수 있습니다. 사용 가능한 처리량이 충분 하지 않은 경우 쿼리를 실행 하려고 하면 Azure Cosmos DB HTTP 429 오류가 반환 됩니다. 잠시 기다린 후 현재 코어 (SQL) API Sdk가이 쿼리를 자동으로 다시 시도 합니다. 제한 된 요청은 시간이 오래 걸리므로 프로 비전 된 처리량이 증가 하면 쿼리 대기 시간이 향상 될 수 있습니다. Azure Portal의 **메트릭** 블레이드에서 [제한 된 요청의 총 수](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) 를 확인할 수 있습니다.

### <a name="increase-maxconcurrency"></a>MaxConcurrency 늘리기

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동 합니다. 하지만 개별 분할 된 컬렉션의 데이터는 쿼리와 관련 하 여 순차적으로 인출 됩니다. 따라서 MaxConcurrency를 파티션 수로 설정 하면 다른 모든 시스템 조건을 동일 하 게 유지 하는 경우 가장 성능이 뛰어난 쿼리를 달성할 수 있습니다. 파티션 수를 모르는 경우 MaxConcurrency (또는 이전 SDK 버전의 MaxDegreesOfParallelism)를 높은 수로 설정할 수 있습니다. 시스템은 최대 병렬 처리 수준으로 최소 (파티션 수, 사용자 제공 입력)를 선택 합니다.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount 증가

쿼리는 결과의 현재 배치가 클라이언트에서 처리 되는 동안 결과를 미리 인출 하도록 디자인 되었습니다. 미리 페치를 사용 하면 쿼리의 전체 대기 시간을 향상 시킬 수 있습니다. MaxBufferedItemCount를 설정 하면 프리페치 된 결과의 수가 제한 됩니다. 이 값을 반환 된 예상 결과 수 (또는 더 높은 수)로 설정 하면 쿼리는 미리 페치를 통해 가장 많은 이점을 얻을 수 있습니다. 이 값을-1로 설정 하면 시스템에서 버퍼링 할 항목 수가 자동으로 결정 됩니다.

## <a name="next-steps"></a>다음 단계
쿼리당 RUs를 측정 하 고 쿼리를 튜닝 하기 위한 실행 통계를 가져오는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [.NET SDK를 사용 하 여 SQL 쿼리 실행 메트릭 가져오기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](sql-api-sql-query-metrics.md)
* [.NET SDK용 성능 팁](performance-tips.md)
