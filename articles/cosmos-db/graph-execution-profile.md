---
title: 실행 프로필을 사용하여 Azure Cosmos DB Gremlin API에서 쿼리 평가
description: 실행 프로필 단계를 사용하여 Gremlin 쿼리 문제를 해결하고 개선하는 방법을 알아봅니다.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087488"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>실행 프로필 단계를 사용하여 Gremlin 쿼리를 평가하는 방법
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

이 문서에서는 Azure Cosmos DB Gremlin API 그래프 데이터베이스에 대해 실행 프로필 단계를 사용하는 방법에 대한 개요를 제공합니다. 이 단계에서는 문제 해결 및 쿼리 최적화에 대한 관련 정보를 제공하며, 이는 Cosmos DB Gremlin API 계정에 대해 실행할 수 있는 모든 Gremlin 쿼리와 호환됩니다.

이 단계를 사용하려면 Gremlin 쿼리 끝에 `executionProfile()` 함수 호출을 추가하기만 하면 됩니다. **Gremlin 쿼리가 실행되고** 작업 결과에서 쿼리 실행 프로필이 있는 JSON 응답 개체를 반환합니다.

예를 들면 다음과 같습니다.

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

`executionProfile()` 단계를 호출한 후 응답은 실행된 Gremlin 단계, 소요된 총 시간, 문이 발생한 Cosmos DB 런타임 연산자의 배열을 포함하는 JSON 개체가 됩니다.

> [!NOTE]
> 이 실행 프로필 구현은 Apache Tinkerpop 사양에 정의되어 있지 않습니다. 이는 Azure Cosmos DB Gremlin API 구현과 관련 있습니다.


## <a name="response-example"></a>응답 예제

다음은 반환될 출력의 주석이 추가된 예제입니다.

> [!NOTE]
> 이 예제는 응답의 일반적인 구조를 설명하는 내용이 주석으로 추가됩니다. 실제 executionProfile 응답에는 주석이 포함되지 않습니다.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> executionProfile 단계에서 Gremlin 쿼리를 실행합니다. 여기에는 `addV` 또는 `addE` 단계가 포함되며, 이로 인해 만들어지고 쿼리에 지정된 변경 내용이 커밋됩니다. 그 결과 Gremlin 쿼리에서 생성된 요청 단위에도 요금이 청구됩니다.

## <a name="execution-profile-response-objects"></a>실행 프로필 응답 개체

executionProfile() 함수의 응답은 구조가 다음과 같은 JSON 개체의 계층 구조를 생성합니다.
  - **Gremlin 작업 개체**: 실행된 전체 Gremlin 작업을 나타냅니다. 다음 속성을 포함합니다.
    - `gremlin`: 실행된 명시적 Gremlin 문입니다.
    - `totalTime`: 단계 실행이 발생한 시간(밀리초)입니다. 
    - `metrics`: 쿼리를 수행하기 위해 실행된 각 Cosmos DB 런타임 연산자를 포함하는 배열입니다. 이 목록은 실행 순서대로 정렬됩니다.
    
  - **Cosmos DB 런타임 연산자**: 전체 Gremlin 작업의 각 구성 요소를 나타냅니다. 이 목록은 실행 순서대로 정렬됩니다. 각 개체에는 다음 속성이 포함됩니다.
    - `name`: 연산자의 이름입니다. 이는 평가 및 실행된 단계의 유형입니다. 자세한 내용은 아래의 표를 참조하세요.
    - `time`: 지정된 연산자에서 소요된 시간(밀리초)입니다.
    - `annotations`: 실행된 연산자와 관련된 추가 정보를 포함합니다.
    - `annotations.percentTime`: 특정 연산자를 실행하는 데 걸린 총 시간의 백분율입니다.
    - `counts`: 이 연산자에 의해 스토리지 계층에서 반환된 개체 수입니다. 이는 `counts.resultCount` 스칼라 값에 포함됩니다.
    - `storeOps`: 하나 이상의 파티션에 걸쳐 있을 수 있는 스토리지 작업을 나타냅니다.
    - `storeOps.fanoutFactor`: 이 특정 스토리지 작업에 액세스한 파티션의 수를 나타냅니다.
    - `storeOps.count`: 이 스토리지 작업에서 반환한 결과 수를 나타냅니다.
    - `storeOps.size`: 지정된 스토리지 작업의 결과 크기(바이트)를 나타냅니다.

Cosmos DB Gremlin 런타임 연산자|설명
---|---
`GetVertices`| 이 단계에서는 지속성 계층에서 예측된 개체 집합을 가져옵니다. 
`GetEdges`| 이 단계는 꼭짓점 집합에 인접한 에지를 가져옵니다. 이 단계를 수행하면 하나 이상의 스토리지 작업이 발생할 수 있습니다.
`GetNeighborVertices`| 이 단계에서는 에지 집합에 연결된 꼭짓점을 가져옵니다. 에지에는 원본 및 대상 꼭짓점의 파티션 키와 ID가 포함됩니다.
`Coalesce`| 이 단계에서는 `coalesce()` Gremlin 단계가 실행될 때마다 두 작업을 평가하기 위한 작업을 수행합니다.
`CartesianProductOperator`| 이 단계에서는 두 데이터 세트 간에 카티전 곱을 계산합니다. 일반적으로 `to()` 또는 `from()`을 사용할 때마다 실행됩니다.
`ConstantSourceOperator`| 이 단계에서는 결과적으로 상수 값을 생성하는 식을 계산합니다.
`ProjectOperator`| 이 단계에서는 이전 작업의 결과를 사용하여 응답을 준비하고 직렬화합니다.
`ProjectAggregation`| 이 단계에서는 집계 작업에 대한 응답을 준비하고 직렬화합니다.

> [!NOTE]
> 이 목록은 새 연산자가 추가되면 계속 업데이트됩니다.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>실행 프로필 응답을 분석하는 방법에 대한 예제

다음은 실행 프로필 응답을 사용하여 발견할 수 있는 일반적인 최적화의 예제입니다.
  - 블라인드 팬아웃 쿼리
  - 필터링되지 않은 쿼리

### <a name="blind-fan-out-query-patterns"></a>블라인드 팬아웃 쿼리 패턴

**분할된 그래프** 에서 다음과 같이 실행 프로필 응답을 가정합니다.

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

이를 통해 다음의 결론을 내릴 수 있습니다.
- Gremlin 문은 `g.V('id')` 패턴을 따르므로, 쿼리는 단일 ID 조회입니다.
- `time` 메트릭을 보면 이 쿼리의 대기 시간이 [단일 지점 읽기 작업에 대해 10ms를 초과](./introduction.md#guaranteed-speed-at-any-scale)하므로, 높은 것으로 판단됩니다.
- `storeOps` 개체를 살펴보면 `fanoutFactor`가 `5`임을 알 수 있습니다. 즉, 이 작업에서 [5개의 파티션](./partitioning-overview.md)에 액세스했습니다.

이 분석의 결론으로, 첫 번째 쿼리가 필요한 것보다 많은 파티션에 액세스하고 있음을 확인할 수 있습니다. 이 문제는 쿼리에서 분할 키를 조건자로 지정하여 해결할 수 있습니다. 이로 인해 대기 시간이 줄어들고 쿼리당 비용이 감소합니다. [그래프 분할](graph-partitioning.md)에 대해 자세히 알아보세요. 좀 더 최적화된 쿼리는 `g.V('tt0093640').has('partitionKey', 't1001')`입니다.

### <a name="unfiltered-query-patterns"></a>필터링되지 않은 쿼리 패턴

다음의 두 실행 프로필 응답을 비교합니다. 간단히 설명하기 위해 이 예제에서는 분할된 단일 그래프를 사용합니다.

이 첫 번째 쿼리는 `tweet` 레이블이 있는 모든 꼭짓점을 검색한 후 인접한 꼭짓점을 가져옵니다.

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

인접한 꼭짓점을 살펴보기 전에 동일한 쿼리의 프로필을 확인하지만, 이제 `has('lang', 'en')` 추가 필터를 사용합니다.

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

이러한 두 쿼리는 동일한 결과에 도달했지만, 첫 번째 쿼리는 인접한 항목을 쿼리하기 전에 더 큰 초기 데이터 세트를 반복해야 하므로 더 많은 요청 단위가 필요합니다. 두 응답에서 다음 매개 변수를 비교할 때 이 동작의 지표를 볼 수 있습니다.
- `metrics[0].time` 값은 첫 번째 응답에서 더 높은데, 이는 이 단일 단계를 해결하는 데 시간이 더 많이 걸렸음을 나타냅니다.
- `metrics[0].counts.resultsCount` 값은 첫 번째 응답에서도 더 높은데, 이는 초기 작업 데이터 세트가 더 컸음을 나타냅니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB에서 [지원되는 Gremlin 기능](gremlin-support.md)에 대해 알아봅니다. 
* [Azure Cosmos DB의 Gremlin API](graph-introduction.md)에 대해 자세히 알아봅니다.