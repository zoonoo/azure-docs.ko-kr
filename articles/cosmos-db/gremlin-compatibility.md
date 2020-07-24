---
title: TinkerPop 기능과 Azure Cosmos DB Gremlin 호환성
description: 참조 설명서 그래프 엔진 호환성 문제
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092510"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 호환성
Azure Cosmos DB Graph 엔진은 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 트래버스 단계 사양을 매우 따르므로 Azure Cosmos DB에 한정 된 구현의 차이점이 있습니다. 지원 되는 Gremlin 단계 목록을 알아보려면 [GREMLIN API 유선 프로토콜 지원](gremlin-support.md) 문서를 참조 하세요.

## <a name="behavior-differences"></a>동작 차이점

* Azure Cosmos DB Graph 엔진은 ***너비 우선*** 순회를 실행 하는 반면, TinkerPop Gremlin는 깊이 우선 합니다. 이 동작은 Cosmos DB 같이 수평 확장 가능한 시스템에서 더 나은 성능을 얻을 것입니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

* ***[Gremlin 바이트 코드](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 는 프로그래밍 언어의 제약을 받지 않는 그래프 조회에 대한 사양입니다. Cosmos DB Graph는 아직 지원 하지 않습니다. `GremlinClient.SubmitAsync()`를 사용 하 고 순회를 텍스트 문자열로 전달 합니다.

* ***`property(set, 'xyz', 1)`*** 설정 카디널리티는 현재 지원 되지 않습니다. 대신 `property(list, 'xyz', 1)`를 사용하세요. 자세한 내용은 [TinkerPop를 사용 하 여 꼭 짓 점 속성](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)을 참조 하세요.

* *** `match()` 단계*** 를 현재 사용할 수 없습니다. 이 단계에서는 선언적 쿼리 기능을 제공 합니다.

* 꼭 짓 점 또는 가장자리의 ***속성인 개체는*** 지원 되지 않습니다. 속성은 기본 형식 또는 배열이어야 합니다.

* ***배열 속성*** `order().by(<array property>)` 으로 정렬 지원 되지 않습니다. 정렬은 기본 형식만 지원됩니다.

* ***기본이 아닌 JSON 형식은*** 지원 되지 않습니다. `string`, `number` 또는 형식을 사용 `true` / `false` 합니다. `null`값은 지원 되지 않습니다. 

* ***GraphSONv3*** serializer는 현재 지원 되지 않습니다. `GraphSONv2`연결 구성에서 Serializer, 판독기 및 작성기 클래스를 사용 합니다. Azure Cosmos DB Gremlin API에서 반환 된 결과는 GraphSON 형식과 형식이 다릅니다. 

* **람다 식과 함수** 는 현재 지원 되지 않습니다. 여기에는 `.map{<expression>}` , `.by{<expression>}` 및 함수가 포함 됩니다 `.filter{<expression>}` . 자세히 알아보고 Gremlin 단계를 사용 하 여 다시 작성 하는 방법에 대 한 자세한 내용은 [람다에 대 한 참고](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)를 참조 하세요.

* 시스템의 분산 특성으로 인해 ***트랜잭션이*** 지원 되지 않습니다.  Gremlin 계정에서 적절 한 일관성 모델을 "고유한 쓰기 읽기"로 구성 하 고 낙관적 동시성을 사용 하 여 충돌 하는 쓰기를 해결 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

* **중간 트래버스 `.V()` 단계를 포함 하는 Gremlin 쿼리의 인덱스 사용률**: 현재 `.V()` 순회의 첫 번째 호출만 인덱스를 사용 하 여 연결 된 필터 또는 조건자를 확인 합니다. 후속 호출에서는 인덱스를 참조 하지 않으므로 쿼리의 대기 시간 및 비용이 늘어날 수 있습니다.
    
    기본 인덱싱을 사용 하는 경우 단계에서 시작 하는 일반적인 read Gremlin 쿼리는 `.V()` 또는와 같은 연결 된 필터링 단계에서 매개 변수를 사용 `.has()` `.where()` 하 여 쿼리의 비용과 성능을 최적화 합니다. 예를 들면 다음과 같습니다.

    ```java
    g.V().has('category', 'A')
    ```

    그러나 `.V()` Gremlin 쿼리에 둘 이상의 단계가 포함 된 경우에는 쿼리에 대 한 데이터의 해상도가 최적이 아닐 수 있습니다. 다음 쿼리를 예로 들어 보겠습니다.

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    이 쿼리는 라는 속성에 따라 꼭 짓 점 그룹 두 개를 반환 `category` 합니다. 이 경우 첫 번째 호출만 `g.V().has('category', 'A')` 인덱스를 사용 하 여 해당 속성의 값을 기반으로 꼭 짓 점을 확인 합니다.

    이 쿼리에 대 한 해결 방법은 및와 같은 하위 트래버스 단계를 사용 하는 것입니다 `.map()` `union()` . 이는 아래 예시.

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    [Gremlin `executionProfile()` 단계](graph-execution-profile.md)를 사용 하 여 쿼리의 성능을 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 사용자 의견을 공유 하려면 [Cosmos DB 사용자](https://feedback.azure.com/forums/263030-azure-cosmos-db) 의견 페이지를 방문 하 고 중요 한 기능에 대해 팀에 집중할 수 있도록 합니다.
