---
title: Azure Cosmos DB Gremlin 지원
description: Apache TinkerPop에서 Gremlin 언어를 알아봅니다. Azure Cosmos DB에서 어떤 기능과 단계를 사용할 수 있는지 알아보기
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 0c1ca054f9d28bb81c6f8acf6c0f43b134a596ed
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293772"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Azure Cosmos DB Gremlin 그래프 지원
Azure Cosmos DB는 [Apache Tinkerpop의](https://tinkerpop.apache.org) 그래프 순회 언어인 [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)을 지원합니다. Gremlin 언어를 사용하여 그래프 엔터티(예: 꼭짓점 및 에지)를 만들고, 해당 엔터티 내에서 속성을 수정하고, 쿼리 및 순회를 수행하고, 엔터티를 삭제할 수 있습니다. 

이 문서에서는 Gremlin을 간략히 연습해 볼 수 있는 과정을 제공하고 Gremlin API가 지원하는 Gremlin 기능을 나열합니다.

## <a name="compatible-client-libraries"></a>호환 가능한 클라이언트 라이브러리

다음 표에서는 Azure Cosmos DB에 대해 사용할 수 있는 인기 있는 Gremlin 드라이버를 보여 줍니다.

| 다운로드 | 원본 | 시작하기 | 지원되는 커넥터 버전 |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [GitHub의 Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [.NET을 사용하여 그래프 만들기](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Java를 사용하여 그래프 만들기](create-graph-java.md) | 3.2.0+ |
| [Node.JS](https://www.npmjs.com/package/gremlin) | [Github의 Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Node.js를 사용하여 그래프 만들기](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Github의 Gremlin Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Python을 사용하여 그래프 만들기](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [GitHub의 Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [PHP를 사용하여 그래프 만들기](create-graph-php.md) | 3.1.0 |
| [Gremlin 콘솔](https://tinkerpop.apache.org/downloads.html) | [TinkerPop 문서](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gremlin 콘솔을 사용하여 그래프 만들기](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>지원되는 그래프 개체
TinkerPop은 광범위한 그래프 기술을 지원하는 표준입니다. 따라서 그래프 공급자가 제공하는 기능을 설명하기 위한 표준 용어를 포함합니다. Azure Cosmos DB는 여러 서버 또는 클러스터에서 분할될 수 있는 지속적이고 높은 동시성을 갖는 쓰기 가능한 그래프 데이터베이스를 제공합니다. 

다음 표에는 Azure Cosmos DB를 통해 구현되는 TinkerPop 기능이 나와 있습니다. 

| Category | Azure Cosmos DB 구현 |  메모 | 
| --- | --- | --- |
| 그래프 기능 | Persistence 및 ConcurrentAccess를 제공합니다. 트랜잭션을 지원하도록 설계되었습니다. | 컴퓨터 메서드를 Spark 커넥터를 통해 구현할 수 있습니다. |
| 변수 기능 | 부울, 정수, 바이트, Double, Float, Long, 문자열을 지원합니다. | 기본 형식을 지원하고, 데이터 모델을 통해 복잡한 형식과 호환됩니다. |
| 꼭짓점 기능 | RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty를 지원합니다.  | 꼭짓점 만들기, 수정 및 삭제를 지원합니다. |
| 꼭짓점 속성 기능 | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | 꼭짓점 속성 만들기, 수정 및 삭제를 지원합니다. |
| 에지 기능 | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | 에지 만들기, 수정 및 삭제를 지원합니다. |
| 에지 속성 기능 | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | 에지 속성 만들기, 수정 및 삭제를 지원합니다. |

## <a name="gremlin-wire-format-graphson"></a>Gremlin 통신 형식: GraphSON

Azure Cosmos DB는 Gremlin 작업의 결과를 반환할 때 [GraphSON 형식](http://tinkerpop.apache.org/docs/current/reference/#graphson)을 사용합니다. Azure Cosmos DB는 현재 "GraphSONv2" 버전을 지원합니다. GraphSON은 JSON을 사용하여 꼭짓점, 에지 및 속성(단일 및 다중 값 속성)을 나타내기 위한 Gremlin 표준 형식입니다.

예를 들어 다음 코드 조각은 Azure Cosmos DB에서 *클라이언트로 반환되는* 꼭짓점의 GraphSON 표현을 보여 줍니다. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

꼭짓점에 대해 GraphSON에서 사용되는 속성은 다음과 같습니다.

| 자산 | 설명 | 
| --- | --- | --- |
| `id` | 꼭짓점의 ID입니다. 고유해야 합니다(해당되는 경우 `_partition` 값과 조합). 값이 제공되지 않으면 GUID가 자동으로 제공됩니다. | 
| `label` | 꼭짓점의 레이블입니다. 이 속성은 엔터티 형식을 설명하는 데 사용됩니다. |
| `type` | 비그래프 문서의 꼭짓점을 구별하는 데 사용됩니다. |
| `properties` | 꼭짓점과 연결된 사용자 정의 속성의 모음입니다. 각 속성에는 값이 여러 개 있을 수 있습니다. |
| `_partition` | 꼭짓점의 파티션 키입니다. [그래프 분할](graph-partitioning.md)에 사용됩니다. |
| `outE` | 이 속성에는 꼭짓점의 바깥 에지 목록이 포함됩니다. 꼭짓점과 함께 인접 정보를 저장하므로 순회를 빠르게 실행할 수 있습니다. 에지는 해당 레이블을 기준으로 그룹화됩니다. |

또한 에지에는 그래프의 다른 부분으로 탐색하는 데 도움이 되는 다음 정보가 포함됩니다.

| 자산 | 설명 |
| --- | --- |
| `id` | 에지의 ID입니다. 고유해야 합니다(해당되는 경우 `_partition` 값과 조합). |
| `label` | 에지의 레이블입니다. 이 속성은 선택 사항이며, 관계 유형을 설명하는 데 사용됩니다. |
| `inV` | 이 속성에는 에지의 모든 꼭지점 목록이 포함됩니다. 에지와 함께 인접 정보를 저장하므로 순회를 빠르게 실행할 수 있습니다. 꼭짓점은 해당 레이블을 기준으로 그룹화됩니다. |
| `properties` | 에지와 연결된 사용자 정의 속성의 모음입니다. 각 속성에는 값이 여러 개 있을 수 있습니다. |

각 속성은 배열 내에 여러 값을 저장할 수 있습니다. 

| 자산 | 설명 |
| --- | --- |
| `value` | 속성의 값입니다.

## <a name="gremlin-steps"></a>Gremlin 단계
이제 Azure Cosmos DB에서 지원되는 Gremlin 단계를 살펴보겠습니다. Gremlin에 대한 전체 참조는 [TinkerPop 참조](https://tinkerpop.apache.org/docs/3.3.2/reference)를 참조하세요.

| 단계 | 설명 | TinkerPop 3.2 설명서 |
| --- | --- | --- |
| `addE` | 두 꼭짓점 사이에 에지를 추가합니다. | [addE 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | 그래프에 꼭짓점을 추가합니다. | [addV 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | 모든 순회가 값을 반환하는지 확인합니다. | [and 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | 단계의 출력에 변수를 할당하는 단계 변조기 | [as 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | `group` 및 `order`에서 사용되는 단계 변조기 | [by 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 결과를 반환하는 첫 번째 순회를 반환합니다. | [coalesce 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 상수 값을 반환합니다. `coalesce`에 사용됩니다.| [constant 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | 순회에서 해당 개수를 반환합니다. | [count 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 중복 항목을 제거하고 값을 반환합니다. | [dedup 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 값(꼭짓점/에지)을 삭제합니다. | [drop 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 실 된 Gremlin 단계에서 생성된 모든 작업에 대한 설명을 작성합니다. | [executionProfile 단계](graph-execution-profile.md) |
| `fold` | 결과의 집계를 계산하는 장벽으로 작동합니다.| [fold 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 지정된 레이블을 기준으로 값을 그룹화합니다.| [group 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | 속성, 꼭짓점, 에지를 필터링하는 데 사용됩니다. `hasLabel`, `hasId`, `hasNot` 및 `has` 변형을 지원합니다. | [has 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 스트림에 값을 삽입합니다.| [inject 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | 부울 식을 사용하여 필터를 수행하는 데 사용됩니다. | [is 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | 순회의 항목 수를 제한하는 데 사용됩니다.| [limit 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | local은 하위 쿼리와 비슷하게 순회 섹션을 래핑합니다. | [local 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | 필터의 부정을 생성하는 데 사용됩니다. | [not 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 결과를 생성하는 경우 지정된 순회의 결과를 반환하고, 그렇지 않으면 호출하는 요소를 반환합니다. | [optional 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 순회 중 하나 이상이 값을 반환하도록 합니다. | [or 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 결과를 지정된 정렬 순서로 반환합니다. | [order 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | 순회의 전체 경로를 반환합니다. | [path 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | 속성을 맵으로 투영합니다. | [project 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 지정된 레이블에 대한 속성을 반환합니다. | [properties 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 지정된 값 범위로 필터링합니다.| [range 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | 지정된 횟수 동안 단계를 반복합니다. 반복에 사용됩니다. | [repeat 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | 순회의 결과를 샘플링하는 데 사용됩니다. | [sample 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | 순회의 결과를 투영하는 데 사용됩니다. |  [select 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | 순회의 비차단 집계에 사용됩니다. | [store 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열의 시작 부분이 포함된 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열의 끝 부분이 포함된 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | 문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열의 내용이 포함된 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | 문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열로 시작하지 않는 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | 문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열로 끝나지 않는 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | 문자열 필터링 함수. 이 함수는 `has()` 단계에서 특정 문자열이 포함되지 않은 속성을 검색하는 조건자로 사용됩니다. | [TextP 조건자](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | 꼭짓점에서의 경로를 트리로 집계합니다. | [tree 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 반복기를 단계로 언롤합니다.| [unfold 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 여러 순회의 결과를 병합합니다.| [union 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 꼭짓점 및 에지 `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, `otherV` 간 순회에 필요한 단계가 포함되어 있습니다. | [vertex 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | 순회의 결과를 필터링하는 데 사용. `eq`, `neq`, `lt`, `lte`, `gt`, `gte` 및 `between` 연산자 지원  | [where 단계](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB에서 제공하는 쓰기 최적화 엔진은 기본적으로 꼭짓점 및 에지 내의 모든 속성에 대한 자동 인덱싱을 지원합니다. 따라서 필터가 있는 쿼리, 범위 쿼리, 속성에 대한 정렬 또는 집계가 인덱스에서 처리되고 효율적으로 제공됩니다. Azure Cosmos DB에서 인덱싱이 작동하는 방식에 대한 자세한 내용은 [스키마 독립적 인덱싱](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [SDK를 사용하여](create-graph-dotnet.md) 그래프 애플리케이션 빌드 시작 
* Azure Cosmos DB에서 [그래프 지원](graph-introduction.md)에 대해 알아보기
