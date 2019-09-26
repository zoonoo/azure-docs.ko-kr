---
title: TinkerPop 기능과 Azure Cosmos DB Gremlin 호환성
description: 참조 설명서 그래프 엔진 호환성 문제
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910879"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 호환성
Azure Cosmos DB Graph 엔진은 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 트래버스 단계 사양을 매우 따르므로 차이점이 있습니다.

## <a name="behavior-differences"></a>동작 차이점

* Azure Cosmos DB Graph 엔진은 ***너비 우선*** 순회를 실행 하는 반면, TinkerPop Gremlin는 깊이 우선 합니다. 이 동작은 Cosmos DB 같이 수평 확장 가능한 시스템에서 더 나은 성능을 얻을 것입니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

* ***[Gremlin 바이트 코드](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 는 프로그래밍 언어의 제약을 받지 않는 그래프 조회에 대한 사양입니다. Cosmos DB Graph는 아직 지원 하지 않습니다. 를 ```GremlinClient.SubmitAsync()``` 사용 하 고 순회를 텍스트 문자열로 전달 합니다.

* ***```property(set, 'xyz', 1)```*** 설정 카디널리티는 현재 지원 되지 않습니다. 대신 ```property(list, 'xyz', 1)```를 사용하세요.

* ***```match()```*** 선언적 패턴 일치를 사용 하 여 그래프를 쿼리할 수 있습니다. 이 기능은 사용할 수 없습니다.

* 꼭 짓 점 또는 가장자리의 ***속성인 개체는*** 지원 되지 않습니다. 속성은 기본 형식 또는 배열이어야 합니다.

* ***배열 속성으로 정렬*** ```.order().by(<array property>)``` 지원 되지 않습니다. 정렬은 기본 형식만 지원됩니다.

* ***기본이 아닌 JSON 형식은*** 지원 되지 않습니다. , ```string``` ```number```또는 형식을사용/ 합니다. ```true``` ```false``` ```null```값은 지원 되지 않습니다. 

* ***GraphSONv3*** serializer는 현재 사용할 수 없습니다.

* 시스템의 분산 특성으로 인해 ***트랜잭션이*** 지원 되지 않습니다.  Gremlin 계정에서 적절 한 일관성 모델을 "고유한 쓰기 읽기"로 구성 하 고 낙관적 동시성을 사용 하 여 충돌 하는 쓰기를 해결 합니다.

## <a name="next-steps"></a>다음 단계
* 사용자 의견을 공유 하려면 [Cosmos DB 사용자](https://feedback.azure.com/forums/263030-azure-cosmos-db) 의견 페이지를 방문 하 고 중요 한 기능에 대해 팀에 집중할 수 있도록 합니다.
