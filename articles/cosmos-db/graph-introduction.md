---
title: Azure Cosmos DB Gremlin API 소개
description: Azure Cosmos DB를 사용하여 Apache TinkerPop의 Gremlin 그래프 쿼리 언어로 대기 시간을 단축하면서 대량의 그래프를 저장하고 쿼리하고 트래버스하는 방법을 알아봅니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502803"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB 소개: Gremlin API

[Azure Cosmos DB](introduction.md) 는 중요 업무용 애플리케이션에 사용할 수 있도록 Microsoft에서 제공하는 전역 분산 다중 모델 데이터베이스 서비스입니다. 또한 다중 모델 데이터베이스 방식이며 문서, 키-값, 그래프 및 열 형식 데이터 모델을 지원합니다. Azure Cosmos DB Gremlin API는 규모에 상관없이 완전 관리형 데이터베이스 환경에서 그래프 데이터를 저장하고 운영하는 데 사용됩니다.  

![Azure Cosmos DB 그래프 아키텍처](./media/graph-introduction/cosmosdb-graph-architecture.png)

이 문서에서는 Azure Cosmos DB Gremlin API의 개요와 이 API를 사용하여 수십억 개의 꼭짓점 및 에지가 포함된 대량의 그래프를 저장하는 방법을 설명합니다. 밀리초 단위 대기 시간 내에 그래프를 쿼리하고 그래프 구조를 쉽게 발전시킬 수 있습니다. Azure Cosmos DB의 Gremlin API는 [Apache TinkerPop](https://tinkerpop.apache.org)  그래프 데이터베이스 표준을 기반으로 하며 Gremlin 쿼리 언어를 사용합니다. 

Azure Cosmos DB의 Gremlin API는 그래프 데이터베이스 알고리즘의 기능을 확장성 높은 관리형 인프라와 결합하여 유연성과 관계형 접근 방식의 부족으로 인한 대부분의 일반적인 데이터 문제에 대한 고유하고 유연한 솔루션을 제공합니다. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Azure Cosmos DB 그래프 데이터베이스의 기능
 
Azure Cosmos DB는 전역 배포, 저장소 및 처리량의 탄력적인 확장, 자동 인덱싱 및 쿼리, 튜닝 가능한 일관성 수준을 제공하고 TinkerPop 표준을 지원하는 완전히 관리되는 그래프 데이터베이스입니다. 

Azure Cosmos DB Gremlin API에서 제공하는 차별화된 기능은 다음과 같습니다.

* **탄력적으로 확장성 있는 처리량 및 스토리지**

  실제 사용되는 그래프는 단일 서버의 용량을 초과하여 확장되어야 합니다. Azure Cosmos DB는 거의 무제한인 규모와 프로비저닝된 처리량을 포함할 수 있는 수평적으로 확장성 있는 그래프 데이터베이스를 지원합니다. 그래프 데이터베이스 규모가 증가하면 데이터는 [그래프 분할](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)을 사용하여 자동으로 분산됩니다.

* **다중 지역 복제**

  Azure Cosmos DB는 그래프 데이터를 Azure 지역에 자동으로 복제할 수 있습니다. 복제는 데이터에 대한 전역 액세스가 필요한 애플리케이션의 개발을 간소화합니다. Azure Cosmos DB는 읽기 대기 시간을 최소화하는 것 외에도, 드물지만 한 지역에서 서비스 중단이 발생하는 경우 애플리케이션의 연속성을 보장할 수 있는 지역별 장애 조치(failover) 메커니즘을 제공합니다. 

* **가장 널리 채택된 그래프 쿼리 표준을 통한 빠른 쿼리 및 이동**

  다른 유형의 꼭짓점 및 에지를 저장하고 익숙한 Gremlin 구문을 통해 이러한 문서를 쿼리합니다. Gremlin은 일반적인 그래프 알고리즘을 구현하기 위한 풍부한 인터페이스를 제공하는 명령형 기능 쿼리 언어입니다. 
  
  Azure Cosmos DB를 통해 스키마 힌트, 보조 인덱스 또는 뷰를 지정할 필요 없이 풍부한 실시간 쿼리 및 이동을 사용할 수 있습니다. [Gremlin을 사용하여 그래프 쿼리](gremlin-support.md)에서 자세한 내용을 참조하세요.

* **완전 관리형 그래프 데이터베이스**

  Azure Cosmos DB는 데이터베이스 및 컴퓨터 리소스를 관리할 필요가 없습니다. 대부분의 기존 그래프 데이터베이스 플랫폼은 인프라의 제한 사항이 적용되며 운영을 보장하기 위해 높은 수준의 유지 관리가 필요합니다. 
  
  완전 관리형 Microsoft Azure 서비스의 경우 가상 머신을 관리하거나, 런타임 소프트웨어를 업데이트하거나, 분할 또는 복제를 관리하거나, 복잡한 데이터 계층 업그레이드를 처리할 필요가 없습니다. 모든 그래프가 자동으로 백업되고 지역적 실패로부터 보호됩니다. 이와 같은 보장을 통해 개발자는 데이터베이스를 운영하고 관리하는 대신 애플리케이션의 가치를 실현하는 데 집중할 수 있습니다. 

* **자동 인덱싱**:

  Azure Cosmos DB는 기본적으로 그래프에서 노드 및 에지 내의 모든 속성을 자동으로 인덱싱하고 스키마 또는 보조 인덱스 생성을 예상하거나 요구하지 않습니다. [Azure Cosmos DB의 인덱싱](https://docs.microsoft.com/azure/cosmos-db/index-overview)에 대해 자세히 알아봅니다. 

* **Apache TinkerPop과의 호환성**

  Azure Cosmos DB는 [오픈 소스 Apache TinkerPop 표준](http://tinkerpop.apache.org/)을 지원합니다. Tinkerpop 표준에는 Azure Cosmos DB의 Gremlin API와 쉽게 통합할 수 있는 애플리케이션 및 라이브러리의 충분한 에코시스템이 있습니다. 

* **튜닝 가능한 일관성 수준**

  잘 정의된 5가지 일관성 수준에서 선택하여 일관성과 성능 간의 최적 절충을 실현합니다. 쿼리 및 읽기 작업에 대해 Azure Cosmos DB는 강력, 제한된 부실, 세션, 일관된 접두사, 최종의 5가지 일관성 수준을 제공합니다. 이러한 잘 정의된 세부적인 일관성 수준을 통해 일관성, 가용성 및 대기 시간 간에 적절한 절충을 이룰 수 있습니다. [Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](consistency-levels.md)에서 자세히 알아봅니다.

## <a name="scenarios-that-can-use-gremlin-api"></a>Gremlin API를 사용할 수 있는 시나리오
Azure Cosmos DB 그래프 지원을 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* 소셜 네트워크

  고객 및 다른 사람과의 상호 작용에 대한 데이터를 조합하여 개인 설정된 환경을 개발하거나, 고객 행동을 예측하거나, 관심 영역이 비슷한 다른 사람과 연결해줄 수 있습니다. Azure Cosmos DB를 사용하여 소셜 네트워크를 관리하고 고객 기본 설정 및 데이터를 추적할 수 있습니다.

* 추천 엔진

  이 시나리오는 소매 업계에서 일반적으로 사용됩니다. 제품, 사용자 및 사용자 상호 작용(예: 항목 구입, 검색, 등급 지정)에 대한 정보를 조합하여 사용자 지정된 추천을 작성할 수 있습니다. 대기 시간이 짧고, 탄력적으로 확장되고, 네이티브 그래프 지원을 제공하는 Azure Cosmos DB는 이러한 상호 작용을 모델링하는 데 이상적입니다.

* 지리 공간적

  원격 통신, 물류 및 여행 계획에서 사용되는 많은 애플리케이션은 하나의 영역 내에서 원하는 위치를 찾거나 두 위치 간에 가장 짧은/최적 경로를 찾아야 합니다. Azure Cosmos DB는 이러한 문제를 자연스럽게 해결합니다.

* 사물 인터넷

  IoT 장치 간의 네트워크 및 연결을 그래프로 모델링하여 장치 및 자산의 상태를 보다 잘 이해할 수 있습니다. 또한 한 네트워크 부분의 변경이 다른 부분에 어떤 영향을 미칠 수 있는지도 알 수 있습니다.

## <a name="introduction-to-graph-databases"></a>그래프 데이터베이스 소개
실제 환경의 경우처럼 데이터는 자연스럽게 연결되어 있습니다. 기존 데이터 모델링은 엔터티를 개별적으로 정의하고 런타임에 해당 관계를 컴퓨팅하는 데 중점을 둡니다. 이 모델에도 장점이 있지만 복잡하게 연결된 데이터는 해당 제약 조건에 따라 관리하기 어려울 수 있습니다.  

그래프 데이터베이스 접근 방식은 스토리지 계층에서 관계를 지속하므로 매우 효율적인 그래프 검색 작업이 가능합니다. Azure Cosmos DB의 Gremlin API는 [속성 그래프 모델](https://tinkerpop.apache.org/docs/current/reference/#intro)을 지원합니다.

### <a name="property-graph-objects"></a>속성 그래프 개체

속성 [그래프](http://mathworld.wolfram.com/Graph.html)는 [꼭짓점](http://mathworld.wolfram.com/GraphVertex.html) 및 [가장자리](http://mathworld.wolfram.com/GraphEdge.html)로 구성된 구조입니다. 두 개체는 개수 제한 없이 키-값 쌍을 속성으로 사용할 수 있습니다. 

* **꼭짓점** - 꼭짓점은 사람, 장소, 이벤트 등의 개별 엔터티를 나타냅니다.

* **에지** - 에지는 꼭짓점 간의 관계를 나타냅니다. 예를 들어, 한 사용자가 다른 사용자를 알고 있으며, 이벤트에 관계되어 있고, 최근 한 위치에 있었을 수 있습니다. 

* **속성** -  속성은 꼭짓점 및 에지에 대한 정보를 나타냅니다. 꼭짓점이든, 가장자리든 제한 없이 속성을 포함할 수 있으며 쿼리에서 개체를 설명하고 필터링하는 데 사용될 수 있습니다. 예제 속성에는 이름 및 사용 기간이 포함된 꼭짓점이나 타임스탬프 및/또는 가중치가 포함될 수 있는 가장자리가 있습니다. 

그래프 데이터베이스는 스키마 또는 제한된 데이터 모델에 대한 종속성이 없으므로 일반적으로 NoSQL 또는 비관계형 데이터베이스 범주에 포함됩니다. 이처럼 스키마가 없으므로 연결된 구조를 자연스럽고 효율적으로 모델링 및 저장할 수 있습니다. 

### <a name="gremlin-by-example"></a>Gremlin 예제
샘플 그래프를 사용하여 Gremlin에서 쿼리를 표현할 수 있는 방법을 살펴봅니다. 다음 그림에서는 사용자, 관심 영역 및 디바이스에 대한 데이터를 그래프 형태로 관리하는 비즈니스 응용 프로그램을 보여줍니다.  

![사람, 디바이스 및 관심 분야를 보여주는 샘플 데이터베이스](./media/gremlin-support/sample-graph.png) 

이 그래프에는 다음과 같은 꼭짓점 유형(Gremlin의 "레이블")이 있습니다.

- 사람: 그래프에는 Robin, Thomas 및 Ben 세 사람이 있음
- 관심 분야: 관심 있는 분야로 이 예제에서는 축구 게임
- 디바이스 사람들이 사용하는 디바이스
- 운영 체제: 디바이스가 실행되는 운영 체제

위 엔터티 간 관계는 다음 에지 유형/레이블을 통해 나타냅니다.

- Knows: 예: “Thomas knows Robin”
- Interested: 그래프에서 사람의 관심 분야를 나타내는 데 사용합니다(예: “Ben is interested in Football”).
- RunsOS: 노트북에서 Windows OS가 실행됩니다.
- Uses: 사람이 사용하는 디바이스를 나타냅니다. 예를 들어 Robin은 일련 번호가 77인 Motorola 휴대폰을 사용합니다.

[Gremlin 콘솔](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console)을 사용하여 이 그래프에 대해 일부 작업을 실행해 보겠습니다. 또한 원하는 플랫폼(Java, Node.js, Python 또는 .NET)에서 Gremlin 드라이버를 사용하여 이러한 작업을 수행할 수도 있습니다.  Azure Cosmos DB에서 지원되는 기능을 살펴보기 전에 구문에 익숙해질 수 있는 몇 가지 예제를 확인해 보겠습니다.

먼저 CRUD를 살펴보겠습니다. 다음 Gremlin 문은 "Thomas" 꼭짓점을 그래프에 삽입합니다.

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

그런 후 다음 Gremlin 문은 Thomas와 Robin 사이에 "knows" 에지를 삽입합니다.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

다음 쿼리는 이름의 내림차순으로 "person" 꼭짓점을 반환합니다.
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

그래프의 강점은 "Thomas의 친구들은 어떤 운영 체제를 사용하는가?"와 같은 질문에 답변해야 할 경우에 잘 나타납니다. 다음과 같은 Gremlin 순회를 실행하여 그래프에서 해당 정보를 가져올 수 있습니다.

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
이제 Azure Cosmos DB에서 Gremlin 개발자에게 제공하는 기능을 살펴보겠습니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB의 그래프 지원에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Cosmos DB 그래프 자습서](create-graph-dotnet.md) 시작
* [Gremlin을 사용하여 Azure Cosmos DB에서 그래프를 쿼리](gremlin-support.md)하는 방법 알아보기
