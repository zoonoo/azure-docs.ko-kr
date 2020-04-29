---
title: TinkerPop 기능과 Azure Cosmos DB Gremlin 호환성
description: 참조 설명서 그래프 엔진 호환성 문제
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449887"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 호환성
Azure Cosmos DB Graph 엔진은 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 트래버스 단계 사양을 매우 따르므로 차이점이 있습니다.

## <a name="behavior-differences"></a>동작 차이점

* Azure Cosmos DB Graph 엔진은 ***너비 우선*** 순회를 실행 하는 반면, TinkerPop Gremlin는 깊이 우선 합니다. 이 동작은 Cosmos DB 같이 수평 확장 가능한 시스템에서 더 나은 성능을 얻을 것입니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

* ***[Gremlin 바이트 코드](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 는 프로그래밍 언어의 제약을 받지 않는 그래프 조회에 대한 사양입니다. Cosmos DB Graph는 아직 지원 하지 않습니다. 를 `GremlinClient.SubmitAsync()` 사용 하 고 순회를 텍스트 문자열로 전달 합니다.

* ***`property(set, 'xyz', 1)`*** 설정 카디널리티는 현재 지원 되지 않습니다. 대신 `property(list, 'xyz', 1)`를 사용하세요. 자세한 내용은 [TinkerPop를 사용 하 여 꼭 짓 점 속성](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)을 참조 하세요.

* ***`atch()`*** 선언적 패턴 일치를 사용 하 여 그래프를 쿼리할 수 있습니다. 이 기능은 사용할 수 없습니다.

* 꼭 짓 점 또는 가장자리의 ***속성인 개체는*** 지원 되지 않습니다. 속성은 기본 형식 또는 배열이어야 합니다.

* `order().by(<array property>)` ***배열 속성을 기준으로 정렬 하는 것은*** 지원 되지 않습니다. 정렬은 기본 형식만 지원됩니다.

* ***기본이 아닌 JSON 형식은*** 지원 되지 않습니다. , `string` `number` `true` 또는 / 형식을 `false` 사용 합니다. `null`값은 지원 되지 않습니다. 

* ***GraphSONv3*** serializer는 현재 지원 되지 않습니다. 연결 `GraphSONv2` 구성에서 Serializer, 판독기 및 작성기 클래스를 사용 합니다. Azure Cosmos DB Gremlin API에서 반환 된 결과는 GraphSON 형식과 형식이 다릅니다. 

* **람다 식과 함수** 는 현재 지원 되지 않습니다. 여기에는 `.map{<expression>}`, `.by{<expression>}`및 `.filter{<expression>}` 함수가 포함 됩니다. 자세히 알아보고 Gremlin 단계를 사용 하 여 다시 작성 하는 방법에 대 한 자세한 내용은 [람다에 대 한 참고](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)를 참조 하세요.

* 시스템의 분산 특성으로 인해 ***트랜잭션이*** 지원 되지 않습니다.  Gremlin 계정에서 적절 한 일관성 모델을 "고유한 쓰기 읽기"로 구성 하 고 낙관적 동시성을 사용 하 여 충돌 하는 쓰기를 해결 합니다.

## <a name="next-steps"></a>다음 단계
* 사용자 의견을 공유 하려면 [Cosmos DB 사용자](https://feedback.azure.com/forums/263030-azure-cosmos-db) 의견 페이지를 방문 하 고 중요 한 기능에 대해 팀에 집중할 수 있도록 합니다.
