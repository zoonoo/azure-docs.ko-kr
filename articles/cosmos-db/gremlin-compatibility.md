---
title: TinkerPop 기능과 Azure Cosmos DB Gremlin 호환성
description: 참조 설명서 그래프 엔진 호환성 문제
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327024"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 호환성
Azure Cosmos DB Graph 엔진은 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 트래버스 단계 사양을 매우 따르므로 차이점이 있습니다.

## <a name="behavior-differences"></a>동작 차이점

* Azure Cosmos DB Graph 엔진은 ***너비 우선*** 순회를 실행 하는 반면, TinkerPop Gremlin는 깊이 우선 합니다. 이 동작은 Cosmos DB 같이 수평 확장 가능한 시스템에서 더 나은 성능을 얻을 것입니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

* ***[Gremlin 바이트 코드](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 는 프로그래밍 언어의 제약을 받지 않는 그래프 조회에 대한 사양입니다. Cosmos DB Graph는 아직 지원 하지 않습니다. @No__t-0을 사용 하 고 순회를 텍스트 문자열로 전달 합니다.

* ***```property(set, 'xyz', 1)```*** 집합 카디널리티는 현재 지원 되지 않습니다. 대신 ```property(list, 'xyz', 1)```를 사용하세요.

* ***```match()```은*** 선언적 패턴 일치를 사용 하 여 그래프를 쿼리할 수 있습니다. 이 기능은 사용할 수 없습니다.

* 꼭 짓 점 또는 가장자리의 ***속성인 개체는*** 지원 되지 않습니다. 속성은 기본 형식 또는 배열이어야 합니다.

* ***배열 속성 ```.order().by(<array property>)```로 정렬 하는*** 것은 지원 되지 않습니다. 정렬은 기본 형식만 지원됩니다.

* ***기본이 아닌 JSON 형식은*** 지원 되지 않습니다. @No__t-0, ```number``` 또는 ```true``` @ no__t @ no__t-4 유형을 사용 합니다. ```null``` 값은 지원 되지 않습니다. 

* ***GraphSONv3*** serializer는 현재 사용할 수 없습니다.

* 시스템의 분산 특성으로 인해 ***트랜잭션이*** 지원 되지 않습니다.  Gremlin 계정에서 적절 한 일관성 모델을 "고유한 쓰기 읽기"로 구성 하 고 낙관적 동시성을 사용 하 여 충돌 하는 쓰기를 해결 합니다.

## <a name="next-steps"></a>다음 단계
* 사용자 의견을 공유 하려면 [Cosmos DB 사용자](https://feedback.azure.com/forums/263030-azure-cosmos-db) 의견 페이지를 방문 하 고 중요 한 기능에 대해 팀에 집중할 수 있도록 합니다.
