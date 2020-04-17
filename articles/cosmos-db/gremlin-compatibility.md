---
title: Azure 코스모스 DB 그렘린 TinkerPop 기능과의 호환성
description: 참조 문서 그래프 엔진 호환성 문제
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449887"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure 코스모스 DB 그렘린 호환성
Azure 코스모스 DB 그래프 엔진은 [아파치 TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 통과 단계 사양을 밀접하게 따르지만 차이점이 있습니다.

## <a name="behavior-differences"></a>행동 차이

* Azure 코스모스 DB 그래프 엔진은 ***폭넓은*** 첫 번째 통과를 실행하고 TinkerPop 그렘린은 깊이 우선입니다. 이 동작은 Cosmos DB와 같이 수평으로 확장 가능한 시스템에서 더 나은 성능을 달성합니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

* ***[Gremlin 바이트 코드](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 는 프로그래밍 언어의 제약을 받지 않는 그래프 조회에 대한 사양입니다. 코스모스 DB 그래프는 아직 지원하지 않습니다. 통과를 텍스트 문자열로 사용하고 `GremlinClient.SubmitAsync()` 전달합니다.

* ***`property(set, 'xyz', 1)`*** 설정된 카디널리티는 현재 지원되지 않습니다. 대신 `property(list, 'xyz', 1)`를 사용하세요. 자세한 내용은 [TinkerPop을 사용하여 정점 속성을](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)참조하십시오.

* ***`atch()`*** 을 사용하면 선언적 패턴 일치를 사용하여 그래프를 쿼리할 수 있습니다. 이 기능을 사용할 수 없습니다.

* 정점 이나 모서리에 ***속성으로 개체는*** 지원 되지 않습니다. 속성은 기본 형식 또는 배열이어야 합니다.

* ***배열 속성별로*** `order().by(<array property>)` 정렬하는 것은 지원되지 않습니다. 정렬은 기본 형식만 지원됩니다.

* ***기본이 아닌 JSON 형식은*** 지원되지 않습니다. 을 `string` `number`사용하거나 `true` / `false` 형식을 사용합니다. `null`값은 지원되지 않습니다. 

* ***GraphSONv3*** 직렬화기는 현재 지원되지 않습니다. 연결 `GraphSONv2` 구성에서 직렬화기, 판독기 및 기록기 클래스를 사용합니다. Azure 코스모스 DB 그렘린 API에서 반환된 결과는 GraphSON 형식과 동일한 형식을 갖지 않습니다. 

* **Lambda 식 및 함수는** 현재 지원되지 않습니다. 여기에는 `.map{<expression>}`에서 `.by{<expression>}`및 함수가 `.filter{<expression>}` 포함됩니다. 자세한 내용을 알아보고 그렘린 단계를 사용하여 다시 작성하는 방법을 알아보려면 [Lambdas의 참고 를](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)참조하십시오.

* ***트랜잭션은*** 시스템의 분산 특성으로 인해 지원되지 않습니다.  Gremlin 계정에서 적절한 일관성 모델을 구성하여 "사용자 고유의 쓰기 읽기"를 선택하고 낙관적 동시성을 사용하여 충돌하는 쓰기를 해결합니다.

## <a name="next-steps"></a>다음 단계
* [Cosmos DB 사용자 음성](https://feedback.azure.com/forums/263030-azure-cosmos-db) 페이지를 방문하여 피드백을 공유하고 팀이 중요한 기능에 집중할 수 있도록 도와줍니다.
