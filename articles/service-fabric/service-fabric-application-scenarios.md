---
title: 응용 프로그램 시나리오 및 설계
description: 서비스 패브릭의 클라우드 애플리케이션 범주 개요 상태 저장 및 상태 비저장 서비스를 사용하는 애플리케이션 설계에 대해 논의합니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024746"
---
# <a name="service-fabric-application-scenarios"></a>서비스 패브릭 애플리케이션 시나리오

Azure Service Fabric은 다양한 유형의 비즈니스 응용 프로그램 및 서비스를 작성하고 실행할 수 있는 안정적이고 유연한 플랫폼을 제공합니다. 이러한 응용 프로그램 및 마이크로 서비스는 상태 비수기 또는 상태 보호가 될 수 있으며 효율성을 최대화하기 위해 가상 시스템 간에 리소스 균형을 유지합니다.

Service Fabric의 고유한 아키텍처는 애플리케이션에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 변화하는 리소스 요구 사항에 따라 응용 프로그램을 쉽게 확장하거나 확장할 수 있습니다.

응용 프로그램 빌드에 대한 디자인 지침은 [Azure 서비스 패브릭의 마이크로 서비스 아키텍처및](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) [서비스 패브릭을 사용하는 응용 프로그램 디자인에 대한 모범 사례를](service-fabric-best-practices-applications.md)참조하십시오.

다음과 같은 유형의 응용 프로그램에 서비스 패브릭 플랫폼을 사용하는 것이 좋습니다.

* **데이터 수집, 처리 및 IoT**: 서비스 패브릭은 대규모를 처리하고 상태 관리 서비스를 통해 대기 시간이 낮습니다. 장치 및 계산에 대한 데이터가 배치되는 수백만 대의 장치에서 데이터를 처리하는 데 도움이 될 수 있습니다.

    서비스 패브릭을 사용하여 IoT 서비스를 구축한 고객으로는 [하니웰,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [PCL 건설,](https://customers.microsoft.com/story/pcl-construction-professional-services-azure) [크레스트론,](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [슈나이더 일렉트릭,](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric) [메쉬 시스템](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)등이 있습니다.

* **게임 및 세션 기반 대화형 응용 프로그램**: 서비스 패브릭은 응용 프로그램에 온라인 게임 또는 인스턴트 메시징과 같이 대기 시간이 짧은 읽기 및 쓰기가 필요한 경우에 유용합니다. Service Fabric을 사용하면 별도의 저장소 나 캐시를 만들지 않고도 이러한 대화형 상태 저장 응용 프로그램을 빌드할 수 있습니다. 게임 서비스에서 서비스 패브릭 [사용에](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)대한 설계 지침을 보려면 Azure 게임 [솔루션을](https://azure.microsoft.com/solutions/gaming/) 방문하십시오.

    게임 서비스를 구축 한 고객은 [다음 게임과](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) [디가 모어를](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)포함한다. 대화형 세션을 구축한 고객은 [홀로렌즈가 있는 하니웰을](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)포함합니다.

* **데이터 분석 및 워크플로 처리**: 서비스 패브릭에서 최적화된 읽기 및 쓰기를 통해 이벤트 또는 데이터 스트림을 안정적으로 처리해야 하는 애플리케이션입니다. 또한 Service Fabric은 결과를 신뢰할 수 있어야 하며 손실 없이 다음 처리 단계로 전달되어야 하는 응용 프로그램 처리 파이프라인을 지원합니다. 이러한 파이프라인에는 데이터 일관성 및 계산 보장이 필수적인 트랜잭션 및 금융 시스템이 포함됩니다.

    비즈니스 워크플로 우두혈록 서비스를 구축한 고객으로는 [자이스 그룹,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [쿼럼 비즈니스 솔루션](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)및 [Société General이](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)있습니다.

* **데이터 계산**: 서비스 패브릭을 사용하면 집중적인 데이터 계산을 수행하는 상태 충실한 응용 프로그램을 빌드할 수 있습니다. 서비스 패브릭은 응용 프로그램의 처리(계산) 및 데이터의 코로케이션을 허용합니다. 

   일반적으로 응용 프로그램에서 데이터에 액세스해야 하는 경우 외부 데이터 캐시 또는 저장소 계층과 연결된 네트워크 대기 시간이 계산 시간을 제한합니다. 상태 충실 한 서비스 패브릭 서비스는 이러한 대기 시간을 제거하여 보다 최적화된 읽기 및 쓰기를 가능하게 합니다.

   예를 들어 왕복 시간 요구 사항이 100밀리초 미만인 고객을 위해 거의 실시간 추천 선택을 수행하는 응용 프로그램을 생각해 보십시오. Service Fabric 서비스의 대기 시간 및 성능 특성은 원격 저장소에서 필요한 데이터를 가져와야 하는 표준 구현 모델과 비교하여 사용자에게 응답 가능한 환경을 제공합니다. 권장 사항 선택 계산이 데이터 및 규칙과 공존하기 때문에 시스템이 더 반응합니다.

    계산 서비스를 구축한 고객은 [Solidsoft 회신](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) 및 [인포지원을](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)포함합니다.

* **가용성이 높은 서비스**: 서비스 패브릭은 여러 보조 서비스 복제본을 만들어 빠른 장애 조치(failover)를 제공합니다. 노드, 프로세스 또는 개별 서비스가 하드웨어 또는 다른 오류로 인해 중단되면 최소한의 서비스 손실로 보조 복제본 중 하나가 주 복제본으로 승격됩니다.

* **확장 가능한 서비스**: 개별 서비스를 분할하여 클러스터 전체로 상태를 확장할 수 있습니다. 개별 서비스를 즉시 생성하고 제거할 수도 있습니다. 몇 개의 노드에 있는 몇 개의 인스턴스에서 여러 노드의 수천 개의 인스턴스로 서비스를 확장한 다음 필요에 따라 다시 확장할 수 있습니다. 서비스 패브릭을 사용하여 이러한 서비스를 빌드하고 전체 수명 주기를 관리할 수 있습니다.

## <a name="application-design-case-studies"></a>애플리케이션 디자인 사례 연구

서비스 패브릭이 응용 프로그램을 디자인하는 데 사용되는 방법을 보여 주는 사례 연구는 Azure 사이트의 [고객 스토리](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) 및 [마이크로 서비스에](https://azure.microsoft.com/solutions/microservice-applications/) 게시됩니다.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>상태 비수기 및 상태 비수 마이크로 서비스로 구성된 응용 프로그램 디자인

Azure 클라우드 서비스 작업자 역할을 사용 하 고 응용 프로그램을 빌드 하는 것은 상태 비리스 서비스의 예입니다. 이와는 반대로 상태 저장 마이크로 서비스는 요청 및 응답 이후에도 믿을 수 있는 상태를 유지합니다. 이 기능은 복제에 의해 뒷받침되는 트랜잭션 보증을 제공하는 간단한 API를 통해 상태의 높은 가용성과 일관성을 제공합니다.

Service Fabric의 상태 저장 서비스는 데이터베이스 및 기타 데이터 저장소뿐만 아니라 모든 유형의 응용 프로그램에 고가용성을 제공합니다. 자연적 진행 방향입니다. 애플리케이션은 이미 NoSQL 데이터베이스에 대한 고가용성을 위해 순수하게 관계형 데이터베이스를 사용하지 않습니다. 이제 애플리케이션 자체에서는 안정성, 일관성, 또는 가용성을 유지하면서 추가 성능 향상을 위해 자체 내에서 관리되는 "핫" 상태 및 데이터를 보유할 수 있습니다.

마이크로 서비스로 구성된 응용 프로그램을 빌드할 때 일반적으로 상태 비수기 및 상태 보호 비즈니스 중간 계층 서비스에 호출하는 상태 ASP.NET 및 Node.js와 같은 상태 비관리 웹 앱의 조합이 있습니다. 앱과 서비스는 모두 Service Fabric 배포 명령을 통해 동일한 서비스 패브릭 클러스터에 배포됩니다. 이러한 각 서비스는 확장, 안정성 및 리소스 사용과 관련하여 독립적입니다. 이러한 독립성은 개발 및 수명 주기 관리의 민첩성과 유연성을 향상시킵니다.

상태 저장 마이크로 서비스는 순수한 상태 비저장 애플리케이션의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요했던 추가 큐 및 캐시에 대한 필요성을 제거하기 때문에 애플리케이션 설계를 간소화합니다. 상태 저장 서비스는 가용성이 높고 대기 시간이 적기 때문에 응용 프로그램에서 관리할 세부 정보가 줄어듭니다.

다음 다이어그램에서는 상태 비수기 응용 프로그램을 디자인하는 것과 상태 조정된 응용 프로그램을 디자인하는 것 의 차이점을 보여 줍니다. 상태 관리 서비스는 [신뢰할 수 있는 서비스와](service-fabric-reliable-services-introduction.md) 신뢰할 수 있는 [행위자](service-fabric-reliable-actors-introduction.md) 프로그래밍 모델을 활용하여 높은 처리량과 낮은 대기 시간을 달성하면서 응용 프로그램의 복잡성을 줄입니다.

다음은 상태 비수기 서비스를 사용하는 ![예제 응용 프로그램: 상태 비수기 서비스를 사용하는 응용 프로그램입니다.][Image1]

상태 관리 서비스를 사용하는 예제 응용 ![프로그램: 상태 정보 서비스를 사용하는 응용 프로그램][Image2]

## <a name="next-steps"></a>다음 단계

* 서비스 패브릭 [신뢰할 수 있는](service-fabric-reliable-services-quick-start.md) 서비스 및 신뢰할 [수 있는 행위자](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 통해 상태 비수기 및 상태 비관리 서비스 구축을 시작하십시오.
* Azure에서 마이크로 서비스 빌드에 대한 지침을 보려면 Azure 아키텍처 센터를 [방문하십시오.](https://docs.microsoft.com/azure/architecture/microservices/)
* 응용 프로그램 디자인 지침에 대한 [Azure 서비스 패브릭 응용 프로그램 및 클러스터 모범 사례로](service-fabric-best-practices-overview.md) 이동합니다.

* 참고 항목:
  * [마이크로서비스 이해](service-fabric-overview-microservices.md)
  * [서비스 상태 정의 및 관리](service-fabric-concepts-state.md)
  * [서비스 가용성](service-fabric-availability-services.md)
  * [서비스 크기 조정](service-fabric-concepts-scalability.md)
  * [파티션 서비스](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
