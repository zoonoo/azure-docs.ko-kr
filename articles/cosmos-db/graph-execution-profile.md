---
title: 실행 프로필을 사용하여 Azure 코스모스 DB 그렘린 API에서 쿼리를 평가합니다.
description: 실행 프로필 단계를 사용하여 Gremlin 쿼리문제를 해결하고 개선하는 방법을 알아봅니다.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441845"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>실행 프로필 단계를 사용하여 Gremlin 쿼리를 평가하는 방법

이 문서에서는 Azure Cosmos DB Gremlin API 그래프 데이터베이스에 대해 실행 프로필 단계를 사용하는 방법에 대한 개요를 제공합니다. 이 단계에서는 문제 해결 및 쿼리 최적화에 대한 관련 정보를 제공하며, 이는 Cosmos DB Gremlin API 계정에 대해 실행할 수 있는 모든 Gremlin 쿼리와 호환됩니다.

이 단계를 사용하려면 Gremlin 쿼리 끝에 `executionProfile()` 함수 호출을 부가하기만 하면 됩니다. **Gremlin 쿼리가 실행되고** 작업의 결과가 쿼리 실행 프로필을 가진 JSON 응답 개체를 반환합니다.

예를 들어:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

단계를 호출한 `executionProfile()` 후 응답은 실행된 Gremlin 단계, 걸린 총 시간 및 명령문이 초래한 Cosmos DB 런타임 연산자의 배열을 포함하는 JSON 개체가 됩니다.

> [!NOTE]
> 실행 프로필에 대한 이 구현은 아파치 Tinkerpop 사양에 정의되어 있지 않습니다. Azure 코스모스 DB 그렘린 API의 구현에 만전을 기하고 있습니다.


## <a name="response-example"></a>응답 예제

다음은 반환될 출력의 추가된 예제입니다.

> [!NOTE]
> 이 예제에는 응답의 일반적인 구조를 설명하는 주석이 주석으로 추가됩니다. 실제 executionProfile 응답에는 주석이 포함되지 않습니다.

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
> executionProfile 단계는 그렘린 쿼리를 실행합니다. 여기에는 `addV` 또는 `addE`단계가 포함되며, 이 단계는 생성되고 쿼리에 지정된 변경 내용을 커밋합니다. 따라서 Gremlin 쿼리에서 생성된 요청 단위도 요금이 부과됩니다.

## <a name="execution-profile-response-objects"></a>실행 프로필 응답 개체

executionProfile() 함수의 응답은 다음과 같은 구조를 가진 JSON 개체의 계층 구조를 생성합니다.
  - **그렘린 작업 개체**: 실행된 전체 그렘린 작업을 나타냅니다. 다음 속성을 포함합니다.
    - `gremlin`: 실행된 명시적 그렘린 문입니다.
    - `totalTime`: 단계의 실행이 발생하는 시간(밀리초)입니다. 
    - `metrics`: 쿼리를 수행하기 위해 실행된 Cosmos DB 런타임 연산자 각각을 포함하는 배열입니다. 이 목록은 실행 순서대로 정렬됩니다.
    
  - **Cosmos DB 런타임 연산자**: 전체 그렘린 연산의 각 구성 요소를 나타냅니다. 이 목록은 실행 순서대로 정렬됩니다. 각 개체에는 다음 속성이 포함됩니다.
    - `name`: 연산자의 이름입니다. 이 단계는 평가되고 실행된 단계유형입니다. 자세한 내용은 아래 표를 참조하십시오.
    - `time`: 지정된 연산자가 소요한 시간(밀리초)입니다.
    - `annotations`: 실행된 연산자와 관련된 추가 정보를 포함합니다.
    - `annotations.percentTime`: 특정 연산자 실행에 걸린 총 시간의 백분율입니다.
    - `counts`: 이 연산자가 저장소 계층에서 반환한 개체 수입니다. 이 값은 `counts.resultCount` 내의 스칼라 값에 포함됩니다.
    - `storeOps`: 하나 또는 여러 개의 파티션에 걸쳐 있을 수 있는 저장소 작업을 나타냅니다.
    - `storeOps.fanoutFactor`: 이 특정 저장소 작업에 액세스한 파티션 수를 나타냅니다.
    - `storeOps.count`: 이 저장소 작업이 반환된 결과 수를 나타냅니다.
    - `storeOps.size`: 지정된 저장소 작업의 결과의 바이트 크기를 나타냅니다.

코스모스 DB 그렘린 런타임 연산자|설명
---|---
`GetVertices`| 이 단계에서는 지속성 계층에서 미리 설정된 개체 집합을 가져옵니다. 
`GetEdges`| 이 단계에서는 정점 세트에 인접한 모서리를 가져옵니다. 이 단계를 통해 하나 또는 여러 개의 저장소 작업이 발생할 수 있습니다.
`GetNeighborVertices`| 이 단계에서는 모서리 세트에 연결된 정점을 가져옵니다. 가장자리에는 파티션 키와 소스 및 대상 정점의 ID가 포함됩니다.
`Coalesce`| 이 단계는 `coalesce()` Gremlin 단계가 실행될 때마다 두 작업의 평가를 설명합니다.
`CartesianProductOperator`| 이 단계에서는 두 데이터 집합 간에 카르테시안 제품을 계산합니다. 일반적으로 술어를 사용할 `to()` 때마다 `from()` 실행됩니다.
`ConstantSourceOperator`| 이 단계에서는 식을 계산하여 상수 값을 생성합니다.
`ProjectOperator`| 이 단계에서는 이전 작업의 결과를 사용하여 응답을 준비하고 직렬화합니다.
`ProjectAggregation`| 이 단계에서는 집계 작업에 대한 응답을 준비하고 직렬화합니다.

> [!NOTE]
> 이 목록은 새 연산자가 추가될 때 계속 업데이트됩니다.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>실행 프로필 응답을 분석하는 방법에 대한 예

다음은 실행 프로필 응답을 사용하여 발견할 수 있는 일반적인 최적화의 예입니다.
  - 블라인드 팬아웃 쿼리.
  - 필터링되지 않은 쿼리입니다.

### <a name="blind-fan-out-query-patterns"></a>블라인드 팬아웃 쿼리 패턴

**분할된 그래프에서**다음 실행 프로필 응답을 가정합니다.

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

다음과 같은 결론을 내릴 수 있습니다.
- Gremlin 문은 패턴을 `g.V('id')`따르기 때문에 쿼리는 단일 ID 조회입니다.
- 메트릭을 `time` 보면 [단일 포인트 읽기 작업에 대해 10ms 이상이기](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)때문에 이 쿼리의 대기 시간이 높은 것 같습니다.
- 개체를 `storeOps` 살펴보면 `fanoutFactor` 이 `5`작업에서 [5개의 파티션에](https://docs.microsoft.com/azure/cosmos-db/partition-data) 액세스했다는 것을 알 수 있습니다.

이 분석의 결론으로 첫 번째 쿼리가 필요 이상으로 많은 파티션에 액세스하고 있는지 확인할 수 있습니다. 이 문제는 쿼리에서 분할 키를 조건자로 지정하여 해결할 수 있습니다. 이렇게 하면 대기 시간이 줄어들고 쿼리당 비용이 줄어듭니다. [그래프 분할](graph-partitioning.md)에 대해 자세히 알아보세요. 더 최적의 쿼리는 `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>필터링되지 않은 쿼리 패턴

다음 두 실행 프로필 응답을 비교합니다. 간단히 하기 위해 이러한 예제에서는 단일 분할된 그래프를 사용합니다.

이 첫 번째 쿼리는 레이블을 `tweet` 사용 하 고 모든 정점을 검색 하 고 그 인접 한 정점을 가져옵니다.

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

인접한 정점을 탐색하기 전에 동일한 쿼리의 `has('lang', 'en')`프로파일을 확인하지만 이제 추가 필터를 사용할 수 있습니다.

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

이 두 쿼리는 동일한 결과에 도달했지만 인접 항목을 쿼리하기 전에 더 큰 초기 데이터 집합을 반복해야 하므로 첫 번째 쿼리에는 더 많은 요청 단위가 필요합니다. 두 응답에서 다음 매개 변수를 비교할 때 이 동작의 지표를 볼 수 있습니다.
- `metrics[0].time` 첫 번째 응답에서 값이 더 높으며, 이는 이 단일 단계가 해결하는 데 더 오래 걸렸음을 나타냅니다.
- `metrics[0].counts.resultsCount` 첫 번째 응답에서 값이 더 높으며 이는 초기 작업 데이터 집합이 더 컸음을 나타냅니다.

## <a name="next-steps"></a>다음 단계
* Azure 코스모스 DB에서 [지원되는 그렘린 기능에](gremlin-support.md) 대해 알아봅니다. 
* [Azure 코스모스 DB의 그렘린 API에](graph-introduction.md)대해 자세히 알아보십시오.
