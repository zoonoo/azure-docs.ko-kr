---
title: "Azure Cosmos DB Graph API 소개 | Microsoft Docs"
description: "Azure Cosmos DB를 사용하여 Gremlin 쿼리 언어로 대기 시간을 단축하면서 대량의 그래프를 저장하고 쿼리하고 트래버스하는 방법을 알아봅니다."
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Azure Cosmos DB: Graph API 소개

[Azure Cosmos DB](introduction.md)는 업무에 중요한 응용 프로그램에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)로 지원되는 [턴키 전역 분산](../documentdb/documentdb-distribute-data-globally.md), 전 세계적으로 [처리량 및 저장소의 탄력적인 확장](partition-data.md), 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, [잘 정의된 5개 일관성 수준](../documentdb/documentdb-consistency-levels.md), 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 및 인덱스 관리를 처리하지 않아도 되도록 [데이터를 자동으로 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다. 또한 다중 모델 방식이며, 문서, 키-값, 그래프 및 열 형식 데이터 모델을 지원합니다. 

![Gremlin, 그래프 및 Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB는 턴키 전역 분산, 처리량 및 저장소의 탄력적인 확장, 10ms 미만의 읽기 대기 시간 및 p99로 15ms 미만, 자동 인덱싱 및 쿼리, 튜닝 가능한 일관성 수준, 99.99% 가용성을 포함하는 포괄적인 SLA와 함께 그래프 모델링 및 트래버스 API를 제공합니다. Azure Cosmos DB[Gremlin 표준](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)을 사용하여 쿼리할 수 있고 다른 Gremlin 호환 그래프 시스템과 통합됩니다.

이 문서에서는 Azure Cosmos DB Graph API이 개요, 이 API를 사용하여 수십억 개의 꼭짓점 및 에지가 있는 방대한 그래프를 저장하고, 밀리초 크기 대기 시간 내에서 쿼리하고, 그래프 구조 및 스키마를 쉽게 발전시키는 방법을 제공합니다. 

## <a name="graph-databases"></a>그래프 데이터베이스
실제 환경의 경우처럼 데이터는 자연스럽게 연결되어 있습니다. 일반적인 데이터 모델링은 엔터티에 중점을 둡니다. 그렇지만 대부분의 응용 프로그램에서는 엔터티 간의 풍부한 관계를 모델링할 필요도 있습니다. 그래프를 사용하면 엔터티 및 관계를 자연스럽게 모델링할 수 있습니다. 

[그래프](http://mathworld.wolfram.com/Graph.html)는 [꼭짓점](http://mathworld.wolfram.com/GraphVertex.html) 및 [에지](http://mathworld.wolfram.com/GraphEdge.html)로 구성된 구조입니다. 꼭짓점 및 에지는 둘 다 임의 개수의 속성을 포함할 수 있습니다. 꼭짓점은 사람, 장소, 이벤트 등의 개별 개체를 나타냅니다. 에지는 꼭짓점 간의 관계를 나타냅니다. 예를 들어 한 사람이 다른 사람을 알 수 있거나, 이벤트와 관련되거나, 최근에 특정 위치에 있었을 수 있습니다. 속성은 꼭짓점 및 에지에 대한 정보를 나타냅니다. 예제 속성으로는 이름, 나이를 갖는 꼭짓점과 타임스탬프 및/또는 가중치를 갖는 에지가 있습니다. 공식적으로 이 모델을 [속성 그래프](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model)라고 합니다. Azure Cosmos DB는 속성 그래프 모델을 지원합니다. 

예를 들어 다음 다이어그램 샘플 그래프는 사람, 모바일 장치, 관심 분야 및 운영 체제 간 관계를 보여 줍니다. 

![사람, 장치 및 관심 분야를 보여 주는 샘플 데이터베이스](./media/graph-introduction/sample-graph.png) 

그래프는 과학, 기술 및 비즈니스의 다양한 데이터 집합을 이해하는 데 유용합니다. 그래프 데이터베이스를 사용하면 자연스럽고 효율적으로 그래프를 모델링하고 저장하여 많은 시나리오에서 관심을 이끌 수 있습니다. 그래프 데이터베이스는 일반적으로 NoSQL 데이터베이스입니다. 사용 사례에 주로 스키마 유연성과 신속한 반복이 필요하기 때문입니다. 

그래프는 새롭고 강력한 데이터 모델링 기법을 제공합니다. 그렇지만 이것만으로 그래프 데이터베이스를 사용하는 충분한 이유가 되는 것은 아닙니다. 그래프 통과와 관련된 여러 사용 사례 및 패턴에서 그래프는 전형적인 SQL 및 NoSQL 데이터베이스에 비해 훨씬 높은 성능을 발휘합니다. 이러한 성능 차이는 친구의 친구 같은 둘 이상의 관계를 통과할 때 더욱 증폭됩니다. 

그래프 데이터베이스가 제공하는 빠른 통과 기능을 깊이 우선 검색, 너비 우선 검색, Dijkstra 알고리즘 등의 그래프 알고리즘에 조합하여 소셜 네트워킹, 지리 공간적, 콘텐츠 관리, 권장 사항 등 다양한 도메인의 문제를 해결할 수 있습니다. 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용한 광범위한 규모의 그래프
Azure Cosmos DB는 전역 배포, 저장소 및 처리량의 탄력적인 확장, 자동 인덱싱 및 쿼리, 튜닝 가능한 일관성 수준을 제공하고 TinkerPop 표준을 지원하는 완전히 관리되는 그래프 데이터베이스입니다.  

![Azure DB Cosmos 그래프 아키텍처](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB는 시중에 판매되는 다른 그래프 데이터베이스에 비해 다음과 같은 차별화된 기능을 제공합니다. 

* **탄력적으로 확장 가능한 처리량 및 저장소**: 실제 사용되는 그래프는 단일 서버의 용량을 초과하여 확장되어야 합니다. Azure Cosmos DB를 사용하면 여러 서버에 걸쳐 원활하게 그래프를 확장할 수 있습니다. 또한 액세스 패턴에 따라 그래프의 처리량을 독립적으로 확장할 수 있습니다. Azure Cosmos DB는 거의 무제한으로 저장소 크기 및 프로비전된 처리량을 확장할 수 있는 그래프 데이터베이스를 지원합니다. 

* **다중 지역 복제:** Azure Cosmos DB에서는 계정에 연결된 모든 지역에 투명하게 그래프 데이터를 복제하여 해당 보증을 포함한 일관성, 가용성과 성능 사이의 균형을 제공하는 한편 데이터에 대한 전역 액세스를 필요로 하는 응용 프로그램을 개발할 수 있도록 합니다. Azure Cosmos DB에서는 멀티 호밍 API를 사용하는 투명한 지역 장애 조치 및 전 세계에 걸쳐 처리량 및 저장소 크기를 탄력적으로 조정하는 기능을 제공합니다.

* **친숙한 Gremlin 구문을 사용한 빠른 쿼리 및 통과**: 다른 유형의 꼭짓점 및 에지를 저장하고 친숙한 Gremlin 구문을 통해 이러한 문서를 쿼리합니다(향상된 SQL도 곧 제공될 예정임). Azure Cosmos DB는 동시성이 높고 잠금이 없으며 로그 구조화된 인덱싱 기술을 활용하여 모든 콘텐츠를 자동으로 인덱싱합니다. 따라서 스키마 힌트, 보조 인덱스 또는 뷰를 지정할 필요 없이 풍부한 실시간 쿼리 및 통과를 사용할 수 있습니다. [Gremlin을 사용한 그래프 쿼리](gremlin-support.md)에서 자세한 내용을 참조하세요.

* **완전히 관리:** 데이터베이스 및 컴퓨터 리소스를 관리할 필요가 없습니다. 완전히 관리되는 Microsoft Azure 서비스의 경우 가상 컴퓨터를 관리하거나 소프트웨어를 배포 및 구성하거나 크기 조정을 관리하거나 복잡한 데이터 계층 업그레이드를 처리할 필요가 없습니다. 모든 그래프가 자동으로 백업되고 지역적 실패로부터 보호됩니다. 필요 시 쉽게 Azure Cosmos DB 계정을 추가하고 용량을 프로비전할 수 있으므로 데이터베이스 작동 및 관리 대신 응용 프로그램에 집중할 수 있습니다.

* **자동 인덱싱:** Azure Cosmos DB는 기본적으로 그래프에서 노드 및 에지 내의 모든 속성을 자동으로 인덱싱하고 스키마 또는 보조 인덱스 생성을 예상하거나 요구하지 않습니다. 

* **Gremlin과의 호환성**: Azure Cosmos DB는 기본적으로 오픈 소스 Gremlin 표준을 지원하고 다른 Gremlin 지원 그래프 시스템에 통합될 수 있습니다. 따라서 Titan 또는 Neo4j 등의 다른 그래프 데이터베이스에서 쉽게 마이그레이션하거나, Apache Spark GraphX 같은 그래프 분석 프레임워크에서 Azure Cosmos DB를 사용할 수 있습니다.

* **튜닝 가능한 일관성 수준:** 잘 정의된 5가지 일관성 수준에서 선택하여 일관성과 성능 간의 최적 절충을 실현합니다. 쿼리 및 읽기 작업에 대해 Azure Cosmos DB는 강력, 제한된 부실, 세션, 일관된 접두사, 최종의 5가지 일관성 수준을 제공합니다. 이러한 잘 정의된 세부적인 일관성 수준을 통해 일관성, 가용성 및 대기 시간 간에 타당한 절충을 수행할 수 있습니다. [일관성 수준을 사용하여 DocumentDB에서 가용성 및 성능 최대화](../documentdb/documentdb-consistency-levels.md)에서 자세히 알아보세요.

또한 Azure Cosmos DB는 같은 컨테이너/데이터베이스 내에서 문서 및 그래프와 같은 여러 모델을 사용하는 기능을 제공합니다. 문서 컬렉션을 사용하여 그래프 데이터를 문서와 나란히 저장하고, JSON에 대한 SQL 쿼리와 Gremlin 쿼리를 둘 다 사용하여 그래프와 동일한 데이터를 쿼리할 수 있습니다. 

## <a name="getting-started"></a>시작
Graph API에 대한 지원을 사용해서 Azure CLI, Azure Powershell 또는 Azure Portal을 통해 Azure DB Cosmos 계정을 만들 수 있습니다. 계정을 만들면 Azure Portal은 Gremlin에 대한 WebSocket 프런트 엔드를 제공하는 `https://<youraccount>.graphs.azure.com`과 같은 서비스 끝점을 제공합니다. [Gremin 콘솔](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console)과 같은 Gremlin 호환 도구가 이 끝점에 연결하고 Java, Node.js 또는 임의 Gremlin 클라이언트 드라이버에서 응용 프로그램을 빌드하도록 구성할 수 있습니다.

다음 표에서는 Azure Cosmos DB에 대해 사용할 수 있는 인기 있는 Gremlin 드라이버를 보여 줍니다.

| 다운로드 | 문서화 |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.JS](https://www.npmjs.com/package/gremlin) |[Github의 Gremlin-JavaScript](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin 콘솔](https://tinkerpop.apache.org/downloads.html) |[TinkerPop 문서](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

또한 Azure Cosmos DB는 NuGet을 통해 자사 .NET 라이브러리에 [Azure Cosmos DB SDK](../documentdb/documentdb-sdk-dotnet.md)에 대한 Gremlin 확장 메서드를 제공합니다. 이 라이브러리는 DocumenDB 데이터 파티션에 직접 연결하는 데 사용할 수 있는 "in-proc" Gremlin 서버를 제공합니다. 

| 다운로드 | 문서화 |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

[Azure Cosmos DB 에뮬레이터](../documentdb/documentdb-nosql-local-emulator.md)를 사용하여 Azure 구독을 구입하거나 비용을 발생시키지 않고도 Graph API를 통해 로컬에서 개발 및 테스트를 수행할 수 있습니다. 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Azure Cosmos DB 그래프 지원에 대한 시나리오
Azure Cosmos DB 그래프 지원을 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다. 

* **소셜 네트워크**: 고객 및 다른 사람과의 상호 작용에 대한 데이터를 조합하여 개인 설정된 환경을 개발하거나, 고객 행동을 예측하거나, 관심 영역이 비슷한 다른 사람과 연결해줄 수 있습니다. 소셜 네트워크를 관리하고 고객 기본 설정 및 데이터를 추적하는 데 Azure Cosmos DB를 사용할 수 있습니다. 

* **추천 엔진**: 소매 업계에서 일반적으로 사용됩니다. 제품, 사용자 및 사용자 상호 작용(예: 항목 구입, 검색, 등급 지정)에 대한 정보를 조합하여 사용자 지정된 추천을 작성할 수 있습니다. 대기 시간이 짧고, 탄력적으로 확장되고, 네이티브 그래프 지원을 제공하는 Azure Cosmos DB는 이러한 상호 작용을 모델링하는 데 이상적입니다.

* **지리 공간**: 원격 통신, 물류 및 여행 계획에서 사용되는 많은 응용 프로그램은 특정 영역 내에서 원하는 위치를 찾거나 두 위치 간에 가장 짧은/최적 경로를 찾아야 합니다. Azure Cosmos DB는 이러한 문제에 가장 적합합니다. 

* **사물 인터넷**: IoT 장치 간의 네트워크 및 연결을 그래프로 모델링하여 장치 및 자산의 상태를 보다 잘 이해하고 한 네트워크 부분의 변경이 다른 부분에 어떤 영향을 미칠 수 있는지 알 수 있습니다. 

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB의 그래프 지원에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Cosmos DB 그래프 자습서](create-graph-dotnet.md) 시작
* [Gremlin을 사용하여 Azure Cosmos DB에서 그래프를 쿼리](gremlin-support.md)하는 방법 알아보기

