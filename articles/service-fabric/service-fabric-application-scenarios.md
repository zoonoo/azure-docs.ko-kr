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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052607"
---
# <a name="service-fabric-application-scenarios"></a>서비스 패브릭 애플리케이션 시나리오
Azure Service Fabric 작성 하 고 다양 한 유형의 비즈니스 응용 프로그램 및 서비스를 실행할 수 있는 안정적이 고 유연한 플랫폼을 제공 합니다. 이러한 응용 프로그램 및 마이크로 서비스는 상태 비저장 또는 상태 저장 될 수 있습니다 하 고 효율성을 최대화 하기 위해 가상 머신 간에 리소스를 분산 하 합니다. 

Service Fabric의 고유한 아키텍처는 애플리케이션에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 리소스 요구 사항의 변화에 따라 애플리케이션을 간단하게 확장 또는 축소(실제 내부 또는 외부)할 수 있습니다.

응용 프로그램 빌드에 대 한 디자인 지침을 참조 하세요 [Azure Service Fabric에서 마이크로 서비스 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) 하 고 [Service Fabric을 사용 하 여 응용 프로그램 디자인에 대 한 유용한](service-fabric-best-practices-applications.md)합니다.

다음 유형의 응용 프로그램에 대 한 Service Fabric 플랫폼을 사용 하는 것이 좋습니다.

* **데이터 수집, 처리 및 IoT**: Service Fabric 큰 규모를 처리 하 고 해당 상태 저장 서비스를 통해 단위의 짧은 대기 시간. 수백만 개의 장치 및 계산에 대 한 데이터 공동 배치 된 장치에서 데이터를 처리 도움이 됩니다.

    Service Fabric을 사용 하 여 IoT 서비스를 구축한 고객 포함 [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)를 [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)합니다 [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), 및 [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)합니다.

* **게임 및 세션 기반 대화형 응용 프로그램**: Service Fabric 응용 프로그램에 온라인 게임 또는 인스턴트 메시징 같은 대기 시간이 짧은 읽기 및 쓰기에 필요한 경우에 유용 합니다. Service Fabric을 사용 하면 별도 저장소나 캐시를 만들 필요 없이 이러한 대화형 상태 저장 응용 프로그램을 빌드할 수 있습니다. 방문 [Azure gaming 솔루션](https://azure.microsoft.com/solutions/gaming/) 디자인에 대 한 지침은 [게임 서비스에서 Service Fabric을 사용 하 여](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)입니다.

    게임 서비스를 구축한 고객 포함 [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) 하 고 [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)합니다. 대화형 세션을 구축한 고객 포함 [Hololens 사용 하 여 Honeywell](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)합니다.

* **데이터 분석 및 워크플로 처리**: 이벤트 또는 최적화 된 읽기 및 쓰기가 Service Fabric에서의 혜택 데이터 스트림을 안정적으로 처리 해야 하는 응용 프로그램입니다. Service Fabric에는 또한 결과 안정적이 고 손실 없이 다음 처리 단계에 전달 해야 응용 프로그램 처리 파이프라인을 지원 합니다. 이러한 파이프라인에는 데이터 일관성 및 계산 보장이 필수적인 트랜잭션 및 재무 시스템을 포함 합니다.

    비즈니스 워크플로 서비스를 구축한 고객 포함 [칼 자이 그룹](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)를 [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), 및 [Société 일반](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)합니다.

* **데이터에서 계산**: Service Fabric을 사용 하면 많은 데이터 계산을 수행 하는 상태 저장 응용 프로그램을 빌드할 수 있습니다. Service Fabric 응용 프로그램에서 처리 (계산) 및 데이터의 공동 배치를 허용합니다. 

   일반적으로 응용 프로그램 데이터에 대 한 액세스를 해야 하는 경우는 외부 데이터 캐시 또는 저장소 계층과 연관 된 네트워크 대기 시간이 계산 시간을 제한 합니다. 해당 대기 시간을 제거 하는 상태 저장 서비스 패브릭 서비스, 읽기 및 쓰기 더 최적화를 사용 하도록 설정 합니다. 
   
   예를 들어, 응용 프로그램을 보다 작거나 100 밀리초의 왕복 시간을 요구를 사용 하 여 고객에 게 실시간 권장 사항 선택 거의 수행 하는 것이 좋습니다. Service Fabric 서비스의 대기 시간 및 성능 특징 비교 원격 저장소에서 필요한 데이터를 인출 하는 표준 구현 모델을 사용 하 여 사용자에 게 응답성이 뛰어난 환경을 제공 합니다. 시스템 응답성 이므로 권장 사항 선택의 계산이 데이터 및 규칙과 함께 배치 됩니다.

    계산 서비스를 구축한 고객 포함 [Solidsoft 회신](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) 하 고 [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)합니다.

* **가용성이 높은 서비스**: Service Fabric은 여러 보조 서비스 복제본을 만들어 신속한 장애 조치를 제공 합니다. 노드, 프로세스 또는 개별 서비스가 하드웨어 또는 다른 오류로 인해 중단되면 최소한의 서비스 손실로 보조 복제본 중 하나가 주 복제본으로 승격됩니다.

* **확장성 있는 서비스**: 개별 서비스를 분할하여 클러스터 전체로 상태를 확장할 수 있습니다. 개별 서비스 생성 및 수도 즉석 제거 합니다. 인스턴스에서 많은 노드의 수천 몇 노드에서 몇 가지 인스턴스에서 서비스를 확장할 수 있으며에 필요에 따라 다시 확장할 수 있습니다. 이러한 서비스를 빌드하고 전체 수명 주기를 관리 하려면 Service Fabric을 사용할 수 있습니다.

## <a name="application-design-case-studies"></a>애플리케이션 디자인 사례 연구
에 게시 된 응용 프로그램을 디자인 하려면 Service Fabric을 사용 하는 방법을 보여 주는 사례 연구를 [고객 스토리](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) 하 고 [Azure에서 마이크로 서비스](https://azure.microsoft.com/solutions/microservice-applications/) 사이트입니다.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>상태 비저장 및 상태 저장 마이크로 서비스로 구성 된 응용 프로그램 디자인
Azure Cloud Services 작업자 역할을 사용 하 여 응용 프로그램을 빌드하는 상태 비저장 서비스의 예시입니다. 이와는 반대로 상태 저장 마이크로 서비스는 요청 및 응답 이후에도 믿을 수 있는 상태를 유지합니다. 이 기능은 고가용성 및 복제에서 지 원하는 트랜잭션 보장을 제공 하는 간단한 Api 통해 상태의 일관성을 제공 합니다. 

Service Fabric에서 상태 저장 서비스는 모든 유형의 응용 프로그램, 뿐 아니라 데이터베이스 및 다른 데이터 저장소에 고가용성을 제공합니다. 자연적 진행 방향입니다. 애플리케이션은 이미 NoSQL 데이터베이스에 대한 고가용성을 위해 순수하게 관계형 데이터베이스를 사용하지 않습니다. 이제 애플리케이션 자체에서는 안정성, 일관성, 또는 가용성을 유지하면서 추가 성능 향상을 위해 자체 내에서 관리되는 "핫" 상태 및 데이터를 보유할 수 있습니다.

일반적으로 상태 비저장 웹 앱 (예: ASP.NET 및 Node.js)의 조합이 있는 마이크로 서비스로 구성 된 응용 프로그램을 작성할 때 상태 비저장 및 상태 저장 비즈니스 중간 계층 서비스를 호출 합니다. 앱 및 서비스는 모든 Service Fabric 배포 명령을 통해 동일한 Service Fabric 클러스터에 배포 됩니다. 이러한 각 서비스는 확장성, 안정성 및 리소스 사용 관련 하 여 무관 합니다. 이 독립성은 민첩성 및 개발 및 수명 주기 관리에 유연성을 개선합니다.

상태 저장 마이크로 서비스는 순수한 상태 비저장 애플리케이션의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요했던 추가 큐 및 캐시에 대한 필요성을 제거하기 때문에 애플리케이션 설계를 간소화합니다. 상태 저장 서비스의 높은 가용성 및 대기 시간이 짧은 있으므로 간단히 응용 프로그램에서 관리할 수 있습니다. 

다음 다이어그램은 응용 프로그램을 상태 비저장 및 상태 저장 디자인 간의 차이점을 보여 줍니다. [Reliable Services](service-fabric-reliable-services-introduction.md) 및 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 프로그래밍 모델을 활용하여 상태 저장 서비스에서 애플리케이션 복잡성을 줄이고 높은 처리량과 낮은 대기 시간을 달성합니다.

상태 비저장 서비스를 사용 하는 예제 응용 프로그램은 다음과 같습니다. ![상태 비저장 서비스를 사용 하는 응용 프로그램][Image1]

상태 저장 서비스를 사용 하는 예제 응용 프로그램은 다음과 같습니다. ![상태 비저장 서비스를 사용 하는 응용 프로그램][Image2]

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [패턴 및 시나리오](service-fabric-patterns-and-scenarios.md)합니다.

* Service Fabric을 사용 하 여 상태 비저장 및 상태 저장 서비스 구축을 시작 [Reliable Services](service-fabric-reliable-services-quick-start.md) 하 고 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델입니다.
* 지침에 대 한 Azure 아키텍처 센터를 방문 [Azure에서 마이크로 서비스 구축](https://docs.microsoft.com/azure/architecture/microservices/)합니다.
* 로 이동 [Azure Service Fabric 응용 프로그램 및 클러스터에 대 한 모범 사례](service-fabric-best-practices-overview.md) 응용 프로그램 디자인 지침에 대 한 합니다.

* 또한 다음 항목을 참조하세요.
  * [마이크로 서비스 정보](service-fabric-overview-microservices.md)
  * [서비스 상태 정의 및 관리](service-fabric-concepts-state.md)
  * [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
  * [서비스 패브릭 서비스 크기 조정](service-fabric-concepts-scalability.md)
  * [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
