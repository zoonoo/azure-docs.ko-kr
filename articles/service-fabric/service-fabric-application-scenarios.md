---
title: 애플리케이션 시나리오 및 설계 | Microsoft Docs
description: 서비스 패브릭의 클라우드 애플리케이션 범주 개요 상태 저장 및 상태 비저장 서비스를 사용하는 애플리케이션 설계에 대해 논의합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228489"
---
# <a name="service-fabric-application-scenarios"></a>서비스 패브릭 애플리케이션 시나리오
Azure 서비스 패브릭은 다양한 유형의 비즈니스 애플리케이션 및 서비스를 쓰고 실행할 수 있도록 하는 안정적이고 유연한 플랫폼을 제공합니다. 이러한 애플리케이션 및 마이크로 서비스는 상태를 저장하지 않을 수도 있고 상태를 저장할 수도 있으며, 가상 머신 간에 리소스를 분산하여 효율성을 극대화합니다. Service Fabric의 고유한 아키텍처는 애플리케이션에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 리소스 요구 사항의 변화에 따라 애플리케이션을 간단하게 확장 또는 축소(실제 내부 또는 외부)할 수 있습니다.

응용 프로그램 빌드에 대 한 디자인 지침을 참조 하세요 [Azure Service Fabric에서 마이크로 서비스 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) 고 [Service Fabric을 사용 하 여 응용 프로그램 디자인의 모범 사례](service-fabric-best-practices-applications.md)

서비스 패브릭 플랫폼의 다음과 같은 유형의 응용 프로그램에 적합합니다.

* **데이터 수집, 처리 및 IoT**: Service Fabric 상태 저장 서비스를 통해 낮은 대기 시간에 큰 규모를 처리 하 고 이므로 수백만 대의 장치에서 데이터 처리를 위한 이상적인 장치와 계산의 데이터를 함께 배치 됩니다.

    IoT 서비스를 Service Fabric을 사용 하 여 구축한 고객 포함 [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)를 [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)합니다 [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), 및 [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)합니다.

* **게임 및 세션 기반 대화형 응용 프로그램**: Service Fabric 응용 프로그램에 온라인 게임 또는 인스턴트 메시징 같은 대기 시간이 짧은 읽기 및 쓰기에 필요한 경우에 적합 합니다. Service Fabric을 사용 하면 별도 저장소나 캐시를 만들 필요 없이 이러한 대화형 상태 저장 응용 프로그램을 빌드할 수 있습니다. 방문 [Azure gaming 솔루션](https://azure.microsoft.com/solutions/gaming/) 디자인에 대 한 지침은 [Service Fabric을 사용 하 여 게임 서비스](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    게임 서비스를 구축한 고객 포함 [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) 하 고 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)합니다. 대화형 세션을 구축한 고객 포함 [Honeywell Hololens 사용 하 여](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **데이터 분석 및 워크플로 처리**: 처리 해야 하는 안정적으로 이벤트 또는 데이터 스트림을 혜택 최적화 된 읽기 및 쓰기가 Service fabric에서에서 응용 프로그램입니다. 또한 Service Fabric 지원 응용 프로그램 처리 파이프라인을 결과 안정적이 고 손실 없이 다음 처리 단계에 전달 해야 합니다. 데이터 일관성 및 계산 보장이 필수적인 트랜잭션 및 재무 시스템을 포함합니다.

    비즈니스 워크플로 서비스를 구축한 고객 포함 [칼 자이 그룹](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) 고 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **데이터에서 계산**: Service Fabric을 사용 하면 데이터 계산 집약적인 상태 저장 응용 프로그램을 빌드할 수 있습니다. Service Fabric 응용 프로그램에서 처리 (계산) 및 데이터를 배치할 수 있습니다. 일반적으로 응용 프로그램 데이터에 대 한 액세스를 해야 하는 경우는 외부 데이터 캐시 또는 저장소 계층과 연관 된 네트워크 대기 시간이 계산 시간을 제한 합니다. 상태 저장 서비스 패브릭 서비스를 사용 하 여 해당 대기 시간이 제거, 읽기 및 쓰기 더 최적화를 사용 하도록 설정 합니다. 예를 들어, 응용 프로그램을 보다 작거나 100 밀리초의 왕복 시간을 요구를 사용 하 여 고객에 게 실시간 권장 사항 선택 거의 수행 하는 것이 좋습니다. (권장 사항 선택의 계산이 인 데이터와 규칙을 사용 하 여 공동 배치) 하는 서비스 패브릭 서비스의 대기 시간 및 성능 특징의 표준 구현 모델을 비교할 사용자에 게 응답성이 뛰어난 환경을 제공합니다 원격 저장소에서 필요한 데이터를 인출 하지 않아도 됩니다.

    계산 서비스를 구축한 고객 포함 [Solidsoft 회신](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) 고 [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **가용성이 높은 서비스**: Service Fabric은 여러 보조 서비스 복제본을 만들어 신속한 장애 조치를 제공 합니다. 노드, 프로세스 또는 개별 서비스가 하드웨어 또는 다른 오류로 인해 중단되면 최소한의 서비스 손실로 보조 복제본 중 하나가 주 복제본으로 승격됩니다.

* **확장성 있는 서비스**: 개별 서비스를 분할하여 클러스터 전체로 상태를 확장할 수 있습니다. 또한 개별 서비스를 즉시 생성하고 제거할 수 있습니다. 서비스 쉽고 빠르게 수 인스턴스 많은 노드의 수천에서 적은 인스턴스에서 몇 개 노드를 확장 하 고 리소스 요구 사항에 따라 다음 다시 감축 합니다. 이러한 서비스를 빌드하고 전체 수명 주기를 관리 하려면 Service Fabric을 사용할 수 있습니다.

## <a name="application-design-case-studies"></a>애플리케이션 디자인 사례 연구
다양 한 응용 프로그램을 디자인 하려면 Service Fabric을 사용 하는 방법을 보여 주는 사례 연구에 게시 되어 [고객 스토리](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) 하며 [마이크로 서비스 솔루션 사이트](https://azure.microsoft.com/solutions/microservice-applications/)합니다.

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>상태 비저장 및 상태 저장 마이크로 서비스로 구성된 애플리케이션 설계
Azure Cloud Service 작업자 역할이 있는 애플리케이션을 구축하는 것은 상태 비저장 서비스의 예입니다. 이와는 반대로 상태 저장 마이크로 서비스는 요청 및 응답 이후에도 믿을 수 있는 상태를 유지합니다. 이 기능은 고가용성 및 복제에서 지 원하는 트랜잭션 보장을 제공 하는 간단한 Api 통해 상태의 일관성을 제공 합니다. 서비스 패브릭의 상태 저장 서비스는 고가용성을 보편화하여, 데이터베이스 및 기타 데이터 저장소가 아닌 모든 유형의 애플리케이션에 가져옵니다. 자연적 진행 방향입니다. 애플리케이션은 이미 NoSQL 데이터베이스에 대한 고가용성을 위해 순수하게 관계형 데이터베이스를 사용하지 않습니다. 이제 애플리케이션 자체에서는 안정성, 일관성, 또는 가용성을 유지하면서 추가 성능 향상을 위해 자체 내에서 관리되는 "핫" 상태 및 데이터를 보유할 수 있습니다.

마이크로 서비스로 구성된 애플리케이션을 구축할 경우 일반적으로 상태 비저장 및 상태 저장 비즈니스 중간 계층 서비스를 호출하는 상태 비저장 웹앱(ASP.NET, Node.js 등) 조합이 있으며 모두 서비스 패브릭 명령을 사용하여 동일한 서비스 패브릭 클러스터에 배포됩니다. 이러한 각 서비스는 확장성, 안정성 및 리소스 사용, 민첩성 및 개발 및 수명 주기 관리에 유연성을 크게 향상 시킵니다 관련 하 여 무관 합니다.

상태 저장 마이크로 서비스는 순수한 상태 비저장 애플리케이션의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요했던 추가 큐 및 캐시에 대한 필요성을 제거하기 때문에 애플리케이션 설계를 간소화합니다. 상태 저장 서비스 자연스럽 게가 높은 가용성 및 짧은 대기 시간을 전체적으로 응용 프로그램에서 관리 하는 이동 부분이 적습니다 있습니다. 아래 다이어그램에는 상태 비저장 애플리케이션과 상태 저장 애플리케이션 간의 설계 차이점을 보여줍니다. [Reliable Services](service-fabric-reliable-services-introduction.md) 및 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 프로그래밍 모델을 활용하여 상태 저장 서비스에서 애플리케이션 복잡성을 줄이고 높은 처리량과 낮은 대기 시간을 달성합니다.

## <a name="an-application-built-using-stateless-services"></a>상태 비저장 서비스를 사용하여 빌드된 애플리케이션
![상태 비저장 서비스를 사용하는 애플리케이션][Image1]

## <a name="an-application-built-using-stateful-services"></a>상태 저장 서비스를 사용하여 빌드된 애플리케이션
![상태 비저장 서비스를 사용하는 애플리케이션][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계

* [고객 사례 연구](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)에 대해 알아보세요.
* [패턴 및 시나리오](service-fabric-patterns-and-scenarios.md)에 대해 자세히 알아보세요.

* Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 및 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 상태 비저장 및 상태 저장 서비스 구축을 시작합니다.
* 지침에 대 한 Azure 아키텍처 센터를 방문 [Azure에서 마이크로 서비스 구축](https://docs.microsoft.com/azure/architecture/microservices/)
* 로 이동 [Azure Service Fabric 응용 프로그램 및 클러스터에 대 한 모범 사례](service-fabric-best-practices-overview.md) 응용 프로그램 디자인 지침에 대 한 합니다.

* 또한 다음 항목을 참조하세요.
  * [마이크로 서비스 정보](service-fabric-overview-microservices.md)
  * [서비스 상태 정의 및 관리](service-fabric-concepts-state.md)
  * [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
  * [서비스 패브릭 서비스 크기 조정](service-fabric-concepts-scalability.md)
  * [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
