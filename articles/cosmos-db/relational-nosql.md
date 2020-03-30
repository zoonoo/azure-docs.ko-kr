---
title: Azure 코스모스 DB NoSQL과 관계형 데이터베이스 간의 차이점 이해
description: 이 문서에서는 NoSQL 과 관계형 데이터베이스 간의 차이점을 참조합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896624"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>NoSQL과 관계형 데이터베이스간의 차이점 이해

이 문서에서는 관계형 데이터베이스에 대한 NoSQL 데이터베이스의 주요 이점 중 일부를 열거합니다. 또한 NoSQL과 함께 작업할 때 겪는 몇 가지 과제에 대해서도 논의할 것입니다. 존재하는 다양한 데이터 저장소를 자세히 살펴보시면 올바른 데이터 저장소 [선택에](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)대한 도움말을 살펴보십시오.

## <a name="high-throughput"></a>높은 처리량

관계형 데이터베이스 시스템을 유지 관리할 때 가장 명백한 문제 중 하나는 대부분의 관계형 엔진이 엄격한 ACID 의미 체계를 적용하기 위해 잠금 및 [래치를 적용한다는 것입니다.](https://en.wikipedia.org/wiki/ACID) 이 방법은 데이터베이스 내에서 일관된 데이터 상태를 보장하는 측면에서 이점이 있습니다. 그러나 동시성, 대기 시간 및 가용성과 관련하여 많은 장단점이 있습니다. 이러한 기본적인 아키텍처 제한으로 인해 트랜잭션 볼륨이 높으면 데이터를 수동으로 샤드해야 할 수 있습니다. 수동 샤딩을 구현하는 것은 시간이 많이 걸리고 고통스러운 운동이 될 수 있습니다.

이러한 시나리오에서 [분산 데이터베이스는](https://en.wikipedia.org/wiki/Distributed_database) 보다 확장성이 있는 솔루션을 제공할 수 있습니다. 그러나 유지 관리는 여전히 비용이 많이 들고 시간이 많이 소요되는 운동이 될 수 있습니다. 관리자는 시스템의 분산 특성이 투명하도록 추가 작업을 수행해야 할 수 있습니다. 또한 데이터베이스의 "연결이 끊긴" 특성을 고려해야 할 수도 있습니다.

[Azure Cosmos DB는](https://docs.microsoft.com/azure/cosmos-db/introduction) 모든 Azure 지역에 전 세계에 배포되어 이러한 문제를 간소화합니다. 파티션 범위는 동시에 고가용성을 유지하면서 응용 프로그램에 맞춰 데이터베이스를 원활하게 성장시키기 위해 동적으로 세분화될 수 있습니다. 세분화된 다중 테넌시와 엄격하게 제어되는 클라우드 네이티브 리소스 거버넌스는 [놀라운 지연 시간 보장과](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) 예측 가능한 성능을 용이하게 합니다. 분할은 완전히 관리되므로 관리자는 코드를 작성하거나 파티션을 관리할 필요가 없습니다.

트랜잭션 볼륨이 초당 수천 개의 트랜잭션과 같이 극단적인 수준에 도달하는 경우 분산 된 NoSQL 데이터베이스를 고려해야 합니다. Azure Cosmos DB를 고려하여 효율성 극대화, 유지 관리 용이성 및 총 소유 비용 절감을 고려하십시오.

![백 엔드](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>계층적 데이터

데이터베이스의 트랜잭션에 많은 부모-자식 관계를 포함할 수 있는 사용 사례는 상당수 있습니다. 이러한 관계는 시간이 지남에 따라 크게 증가할 수 있으며 관리하기가 어려울 수 있습니다. 계층 [적 데이터베이스의 형태는](https://en.wikipedia.org/wiki/Hierarchical_database_model) 1980 년대에 등장했지만 스토리지의 비효율성으로 인해 인기가 없었습니다. [또한 Ted Codd의 관계형 모델이](https://en.wikipedia.org/wiki/Relational_model) 거의 모든 주류 데이터베이스 관리 시스템에서 사용되는 사실상의 표준이 됨에 따라 견인력을 잃었습니다.

그러나 오늘날 문서 스타일 데이터베이스의 인기가 크게 증가했습니다. 이러한 데이터베이스는 디스크에 데이터를 저장하는 비용에 대한 우려로 인해 계층적 데이터베이스 패러다임을 재창조한 것으로 간주될 수 있습니다. 따라서 관계형 데이터베이스에서 많은 복잡한 부모-자식 엔터티 관계를 유지 관리하는 것은 이제 최신 문서 지향 접근 방식에 비해 패턴 이외 로 간주될 수 있습니다.

[객체 지향 디자인의](https://en.wikipedia.org/wiki/Object-oriented_design)출현과 관계형 모델과 결합할 때 발생하는 [임피던스 불일치는](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) 특정 사용 사례에 대한 관계형 데이터베이스의 안티 패턴을 강조 표시합니다. 숨겨져 있지만 종종 상당한 유지 보수 비용이 발생할 수 있습니다. [ORM 접근 방식은](https://en.wikipedia.org/wiki/Object-relational_mapping) 부분적으로 이를 완화하기 위해 진화했지만 문서 지향 데이터베이스는 객체 지향 접근 방식과 훨씬 더 잘 결합됩니다. 이 방법을 사용하면 개발자가 ORM 드라이버 또는 맞춤형 언어 특정 [OO Database 엔진에](https://en.wikipedia.org/wiki/Object_database)커밋할 필요가 없습니다. 데이터에 많은 부모-자식 관계 및 계층 구조의 깊은 수준이 포함되어 있는 경우 [Azure Cosmos DB SQL API와](https://docs.microsoft.com/azure/cosmos-db/introduction)같은 NoSQL 문서 데이터베이스를 사용하는 것이 좋습니다.

![Orderdetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>복잡한 네트워크 및 관계

아이러니하게도, 그들의 이름을 감안할 때 관계형 데이터베이스는 깊고 복잡한 관계를 모델링하기 위한 최적의 솔루션이 아닙니다. 그 이유는 엔터티 간의 관계가 관계형 데이터베이스에 실제로 존재하지 않기 때문입니다. 쿼리를 사용하여 매핑을 허용하려면 카르테시안 조인이 필요한 복잡한 관계와 함께 런타임에 계산해야 합니다. 결과적으로 관계가 증가함에 따라 계산 측면에서 운영 비용이 기하급수적으로 증가합니다. 경우에 따라 이러한 엔터티를 관리하려고 하는 관계형 데이터베이스를 사용할 수 없게 됩니다.

관계형 데이터베이스가 등장한 시기에 다양한 형태의 "네트워크" 데이터베이스가 등장했지만 계층적 데이터베이스와 마찬가지로 이러한 시스템은 인기를 얻기 위해 애를 썼습니다. 느린 채택은 당시의 사용 사례 부족과 스토리지 비효율성 때문이었습니다. 오늘날 그래프 데이터베이스 엔진은 네트워크 데이터베이스 패러다임의 재등장으로 간주될 수 있습니다. 이러한 시스템의 주요 이점은 관계가 데이터베이스 내에 "일류 시민"으로 저장된다는 것입니다. 따라서 교차 관계는 각각의 새 조인 또는 교차 제품과 함께 시간 복잡성을 증가시키는 대신 일정한 시간에 수행할 수 있습니다.

데이터베이스에서 복잡한 관계 네트워크를 유지 관리하는 경우 이 데이터를 관리하기 위해 [Azure Cosmos DB Gremlin API와](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) 같은 그래프 데이터베이스를 고려할 수 있습니다.

![그래프](./media/relational-or-nosql/graph.png)

Azure Cosmos DB는 모든 주요 NoSQL 모델 유형에 대한 API 프로젝션을 제공하는 다중 모델 데이터베이스 서비스입니다. 열 패밀리, 문서, 그래프 및 키 값입니다. [그렘린(그래프)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) 및 SQL(코어) 문서 API 계층은 완벽하게 상호 운용할 수 있습니다. 이렇게 하면 프로그래밍 가능성 수준에서 서로 다른 모델 간에 전환할 수 있는 이점이 있습니다. 그래프 저장소는 복잡한 네트워크 탐색과 동일한 저장소의 문서 레코드로 모델링된 트랜잭션 모두에서 쿼리할 수 있습니다.

## <a name="fluid-schema"></a>유체 스키마

관계형 데이터베이스의 또 다른 특징은 디자인 타임에 스키마를 정의해야 한다는 것입니다. 이는 참조 무결성 및 데이터의 적합성 측면에서 이점이 있습니다. 그러나 응용 프로그램이 증가함에 따라 제한적일 수도 있습니다. 동일한 테이블 또는 데이터베이스 정의를 공유하는 논리적으로 분리된 모델에서 스키마의 변경 사항에 응답하면 시간이 지남에 따라 복잡해질 수 있습니다. 이러한 사용 사례는 종종 레코드 단위로 관리하기 위해 응용 프로그램으로 전환되는 스키마의 이점을 누릴 수 있습니다. 이를 위해서는 데이터베이스가 "스키마에 구애받지 않는" 것이어야 하며 레코드가 데이터베이스에 포함된 데이터 측면에서 "자체 설명"되도록 허용해야 합니다.

구조가 지속적으로 높은 속도로 변경되는 데이터를 관리하는 경우, 특히 데이터베이스 전체에서 적합성을 적용하기 어려운 외부 소스에서 트랜잭션이 발생할 수 있는 경우 보다 스키마에 구애받지 않는 접근 방식을 고려할 수 있습니다. Azure 코스모스 DB와 같은 관리되는 NoSQL 데이터베이스 서비스를 사용합니다.

## <a name="microservices"></a>마이크로 서비스

[마이크로 서비스](https://en.wikipedia.org/wiki/Microservices) 패턴은 최근 몇 년 동안 크게 성장했습니다. 이 패턴은 [서비스 지향 아키텍처에](https://en.wikipedia.org/wiki/Service-oriented_architecture)뿌리를 두고 있습니다. 이러한 최신 마이크로 서비스 아키텍처에서 데이터 전송에 대한 사실상표준은 [JSON이며,](https://en.wikipedia.org/wiki/JSON)이는 대부분의 문서 지향 NoSQL 데이터베이스의 저장 매체이기도 합니다. 따라서 NoSQL 문서는 복잡한 Microservice 구현에서 지속성 및 [동기화(이벤트 소싱 패턴](https://en.wikipedia.org/wiki/Event-driven_architecture)사용)에 훨씬 더 원활하게 맞습니다. 보다 전통적인 관계형 데이터베이스는 이러한 아키텍처에서 유지 관리하는 것이 훨씬 더 복잡할 수 있습니다. 이는 API 전체의 상태 및 동기화에 필요한 변환량이 더 많아기 때문입니다. 특히 Azure Cosmos DB에는 많은 NoSQL 데이터베이스보다 JSON 기반 마이크로 서비스 아키텍처에 훨씬 더 완벽하게 맞는 여러 가지 기능이 있습니다.

* 순수 JSON 데이터 유형 선택
* 데이터베이스에 내장된 JavaScript 엔진 및 [쿼리 API입니다.](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api)
* 클라이언트가 컨테이너에 대한 수정 사항에 대한 알림을 받기 위해 구독할 수 있는 최신 [변경 피드입니다.](https://docs.microsoft.com/azure/cosmos-db/change-feed)

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 데이터베이스의 몇 가지 과제

NoSQL 데이터베이스를 구현할 때 몇 가지 분명한 장점이 있지만 고려해야 할 몇 가지 과제가 있습니다. 관계형 모델로 작업할 때 동일한 정도에 나타나지 않을 수 있습니다.

* 동일한 엔터티를 가리키는 많은 관계가 있는 트랜잭션입니다.
* 전체 데이터 집합에서 강력한 일관성이 필요한 트랜잭션을

첫 번째 과제를 살펴보면 NoSQL 데이터베이스의 엄지 손가락 규칙은 일반적으로 비정규화되어 앞에서 설명했듯이 분산 시스템에서 보다 효율적인 읽기를 생성합니다. 그러나 이러한 접근 방식에는 몇 가지 디자인 과제가 있습니다. 하나의 카테고리와 여러 태그와 관련된 제품을 예로 들어 보겠습니다.

![조인](./media/relational-or-nosql/many-joins.png)

NoSQL 문서 데이터베이스의 모범 사례 방법은 "제품 문서"에서 범주 이름과 태그 이름을 직접 비정규화하는 것입니다. 그러나 범주, 태그 및 제품을 동기화상태로 유지하기 위해 이를 용이하게 하기 위한 디자인 옵션은 "일대다"의 간단한 업데이트가 아니라 제품의 여러 레코드에 데이터가 중복되기 때문에 유지 관리의 복잡성을 추가했습니다. 및 조인을 통해 데이터를 검색할 수 있습니다. 

비정규화된 레코드에서 읽기가 더 효율적이며 개념적으로 결합된 엔터티의 수가 증가함에 따라 점점 더 효율적이라는 단점이 있습니다. 그러나 비정규화 레코드에서 결합된 엔터티의 수가 증가함에 따라 읽기 효율성이 증가하는 것처럼 엔터티를 동기화상태로 유지하는 유지 관리 의 복잡성도 증가합니다. 이러한 절충을 완화하는 한 가지 방법은 [하이브리드 데이터 모델을](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)만드는 것입니다.

NoSQL 데이터베이스에서 이러한 장단점을 처리하는 데 사용할 수 있는 유연성이 더 많지만 유연성이 향상되면 더 많은 설계 결정을 내릴 수 있습니다. 사용자가 다른 파티션에 앉아 있지만 다른 컨테이너에 앉아 있는 경우 [비정규화된 사용자 데이터를 동기화상태로](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) 유지하는 방법을 포함하는 [실제 예제를 사용하여 Azure Cosmos DB에서 데이터를 모델링하고 파티션하는 방법을](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)참조하십시오.

강력한 일관성과 관련하여 전체 데이터 집합에서 이 문제가 필요한 경우는 드뭅니다. 그러나 필요한 경우 분산 데이터베이스에서 문제가 될 수 있습니다. 강력한 일관성을 유지하려면 클라이언트가 데이터를 읽을 수 있도록 허용하기 전에 모든 복제본 및 리전에서 데이터를 동기화해야 합니다. 이렇게 하면 읽기 대기 시간이 늘어까요.

다시 말하지만 Azure Cosmos DB는 여기에 관련된 다양한 장단점에 대한 관계형 데이터베이스보다 더 많은 유연성을 제공하지만 소규모 구현의 경우 이 접근 방식은 더 많은 디자인 고려 사항을 추가할 수 있습니다. 이 항목에 대한 자세한 내용은 [일관성, 가용성 및 성능 절충에](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) 대한 도움말을 참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리하는 방법에 대해 알아봅니다.

* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 컨테이너와 항목으로 작업](databases-containers-items.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](firewall-support.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
