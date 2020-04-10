---
title: Azure 서비스 패브릭 응용 프로그램 디자인 모범 사례
description: Azure 서비스 패브릭을 사용하여 응용 프로그램 및 서비스를 개발하기 위한 모범 사례 및 디자인 고려 사항
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991657"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure 서비스 패브릭 응용 프로그램 디자인 모범 사례

이 문서에서는 Azure 서비스 패브릭에서 응용 프로그램 및 서비스를 빌드하기 위한 모범 사례 지침을 제공합니다.
 
## <a name="get-familiar-with-service-fabric"></a>서비스 패브릭에 익숙해지기
* 서비스 [패브릭에 대해 자세히 알아보고 싶은](service-fabric-content-roadmap.md) 가요?
* 서비스 [패브릭 응용 프로그램 시나리오에](service-fabric-application-scenarios.md)대해 읽어보십시오.
* 서비스 패브릭 프로그래밍 [모델 개요를](service-fabric-choose-framework.md)읽고 프로그래밍 모델 선택 사항을 이해합니다.



## <a name="application-design-guidance"></a>응용 프로그램 설계 지침
서비스 패브릭 응용 프로그램의 [일반적인 아키텍처와](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) [디자인 고려 사항에 익숙해지십시오.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>API 게이트웨이 선택
백 엔드 서비스에 통신하는 API 게이트웨이 서비스를 사용하여 확장할 수 있습니다. 사용되는 가장 일반적인 API 게이트웨이 서비스는 다음과 같습니다.

- [Azure API 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), [서비스 패브릭과 통합되어](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)있습니다.
- [ServiceFabricProcessor를](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) 사용하여 이벤트 허브 파티션에서 읽을 수 있는 [Azure IoT 허브](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure 이벤트 허브입니다.](https://docs.microsoft.com/azure/event-hubs/)
- [Træfik 역 방향 프록시](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), [Azure 서비스 패브릭 공급자를](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)사용 하 여 .
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure 응용 프로그램 게이트웨이는 서비스 패브릭과 직접 통합되지 않습니다. Azure API 관리는 일반적으로 선호되는 선택입니다.
- 사용자 고유의 맞춤형 [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) 웹 애플리케이션 게이트웨이.

### <a name="stateless-services"></a>상태 비수기 서비스
[항상 신뢰할 수 있는 서비스를](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 사용 하 여 상태 비저장 서비스를 구축 하 고 Azure 데이터베이스, Azure 코스모스 DB 또는 Azure 저장소에 상태를 저장 하 여 시작 하는 것이 좋습니다. 외부화된 상태는 대부분의 개발자에게 더 친숙한 접근 방식입니다. 또한 이 방법을 사용하면 저장소에서 쿼리 기능을 활용할 수 있습니다.  

### <a name="when-to-use-stateful-services"></a>상태 충실 서비스 사용 시기
짧은 대기 시간에 대한 시나리오가 있고 데이터를 계산에 가깝게 유지해야 하는 경우 상태 보호 서비스를 고려합니다. 몇 가지 예 시나리오에는 IoT 디지털 트윈 장치, 게임 상태, 세션 상태, 데이터베이스의 데이터 캐싱 및 다른 서비스에 대한 호출을 추적하는 장기 실행 워크플로가 포함됩니다.

데이터 보존 기간을 결정합니다.

- **캐시된 데이터**. 외부 저장소에 대한 대기 시간이 문제가 될 때 캐싱을 사용합니다. 상태 저장 서비스를 자신의 데이터 캐시로 사용하거나 [오픈 소스 SoCreate 서비스 패브릭 분산 캐시를](https://github.com/SoCreate/service-fabric-distributed-cache)사용하는 것이 좋습니다. 이 시나리오에서는 캐시에 있는 모든 데이터를 손실 하는 경우 걱정할 필요가 없습니다.
- **시간 바인딩된 데이터입니다.** 이 시나리오에서는 대기 시간에 대 한 시간 동안 계산에 가까운 데이터를 유지 해야 하지만 *재해에*데이터를 잃을 여유가 있습니다. 예를 들어 많은 IoT 솔루션에서 지난 며칠 동안의 평균 온도가 계산되는 경우와 같이 데이터가 계산에 가까워야 하지만 이 데이터가 손실되면 기록된 특정 데이터 요소가 그다지 중요하지 않습니다. 또한 이 시나리오에서는 일반적으로 개별 데이터 포인트를 백업하는 데 신경 쓰지 않습니다. 외부 저장소에 주기적으로 기록되는 계산된 평균 값만 백업합니다.  
- **장기 데이터**. 신뢰할 수 있는 컬렉션은 데이터를 영구적으로 저장할 수 있습니다. 그러나 이 경우 클러스터에 대한 정기적인 [백업 정책을 구성하는 등](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) [재해 복구를 준비해야](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)합니다. 실제로 재해로 클러스터가 소멸된 경우 어떤 일이 발생하는지, 새 클러스터를 만들어야 하는 위치, 새 응용 프로그램 인스턴스를 배포하고 최신 백업에서 복구하는 방법을 구성합니다.

비용 절감 및 가용성 향상:
- 원격 저장소에서 데이터 액세스 및 트랜잭션 비용이 발생하지 않고 Redis용 Azure Cache와 같은 다른 서비스를 사용할 필요가 없기 때문에 상태 저장 서비스를 사용하여 비용을 절감할 수 있습니다.
- 주로 저장소에 대 한 및 계산에 대 한 상태 저장 서비스를 사용 하 여 비용이 많이 드는, 그리고 우리는 그것을 권장 하지 않습니다. 상태 저장 서비스를 저렴한 로컬 저장소로 계산하는 것으로 간주합니다.
- 다른 서비스에 대한 종속성을 제거하면 서비스 가용성을 향상시킬 수 있습니다. 클러스터에서 HA를 가진 상태를 관리하면 다른 서비스 가동 중단 시간 이나 대기 시간 문제로부터 격리됩니다.

## <a name="how-to-work-with-reliable-services"></a>신뢰할 수 있는 서비스 로 작업하는 방법
서비스 패브릭 신뢰할 수 있는 서비스를 사용하면 상태 비수기 및 상태 비상태 서비스를 쉽게 만들 수 있습니다. 자세한 내용은 신뢰할 [수 있는 서비스 소개를](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)참조하세요.
- 항상 상태 비수기 `RunAsync()` 및 상태 비 수 서비스에 `ChangeRole()` 대 한 메서드및 상태 이면 서비스에 대 한 메서드에서 [취소 토큰을](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) 사용 합니다. 그렇지 않으면 서비스 패브릭이 서비스를 닫을 수 있는지 알지 못합니다. 예를 들어 취소 토큰을 지키지 않으면 응용 프로그램 업그레이드 시간이 훨씬 길어지더 오래 발생할 수 있습니다.
-    적시에 [의사 소통 을](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) 열고 닫고 취소 토큰을 존중합니다.
-    동기화 코드를 비동기 코드와 혼합하지 마십시오. 예를 들어 비동기 `.GetAwaiter().GetResult()` 호출에는 사용하지 마세요. 호출 *스택을* 통해 모든 방법을 비동기를 사용합니다.

## <a name="how-to-work-with-reliable-actors"></a>신뢰할 수 있는 액터작업
서비스 패브릭 신뢰할 수 있는 액터를 사용하면 상태 풀, 가상 액터를 쉽게 만들 수 있습니다. 자세한 내용은 신뢰할 [수 있는 액터 에 대한 소개를](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)참조하십시오.

- 응용 프로그램의 크기를 조정하기 위해 액터 간에 pub/sub 메시징을 사용하는 것이 좋습니다. 이 서비스를 제공하는 도구에는 [오픈 소스 SoCreate 서비스 패브릭 Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 및 [Azure 서비스 버스가](https://docs.microsoft.com/azure/service-bus/)포함됩니다.
- 행위자 상태를 [가능한 한 세분화합니다.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- [액터의 수명 주기를](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)관리합니다. 액터를 다시 사용하지 않을 경우 액터를 삭제합니다. 모든 상태가 메모리에 저장되므로 불필요한 액터를 삭제하는 것은 [휘발성 상태 공급자를](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)사용할 때 특히 중요합니다.
- [턴 기반 동시성](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)으로 인해 액터는 독립적인 개체로 가장 잘 사용됩니다. 다중 행위자, 동기 메서드 호출(각각은 별도의 네트워크 호출이 됨)의 그래프를 만들거나 순환 행위자 요청을 만들지 마십시오. 이는 성능과 규모에 큰 영향을 미칩니다.
- 동기화 코드를 비동기 코드와 혼합하지 마십시오. 성능 문제를 방지하기 위해 비동기를 일관되게 사용합니다.
- 액터에서 장기 통화를 하지 마십시오. 장기 실행 호출은 턴 기반 동시성으로 인해 동일한 행위자에 대한 다른 호출을 차단합니다.
- [Service Fabric 원격을](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) 사용하여 다른 서비스와 통신하는 경우 `ServiceProxyFactory`를 만들고 있는 경우 행위자 수준이 *아닌* [행위자 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) 수준에서 팩터리를 만듭니다.


## <a name="application-diagnostics"></a>애플리케이션 진단
서비스 호출에 [응용 프로그램 로깅을](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) 추가하는 데 대해 철저히 세세하십시오. 서비스가 서로 호출하는 시나리오를 진단하는 데 도움이 됩니다. 예를 들어 A가 B를 호출할 때 C가 D를 호출하면 호출이 어디서나 실패할 수 있습니다. 로깅이 충분하지 않으면 오류를 진단하기가 어렵습니다. 통화 량으로 인해 서비스가 너무 많이 로깅하는 경우 최소한 오류 및 경고를 기록해야 합니다.

## <a name="iot-and-messaging-applications"></a>IoT 및 메시징 애플리케이션
[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure 이벤트 허브에서](https://docs.microsoft.com/azure/event-hubs/)메시지를 읽을 때 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)를 사용합니다. ServiceFabricProcessor는 서비스 패브릭 신뢰할 수 있는 서비스와 통합되어 이벤트 허브 파티션에서 읽는 상태를 `IEventProcessor::ProcessEventsAsync()` 유지하고 메서드를 통해 서비스에 새 메시지를 푸시합니다.


## <a name="design-guidance-on-azure"></a>Azure에 대한 디자인 지침
* [Azure에서](https://docs.microsoft.com/azure/architecture/microservices/) 마이크로 서비스 빌드에 대한 설계 지침을 보려면 Azure 아키텍처 센터를 [방문하십시오.](https://docs.microsoft.com/azure/architecture/microservices/)

* 게임 [용 Azure를](https://docs.microsoft.com/gaming/azure/) 방문하여 게임 [서비스에서 서비스 패브릭 사용에](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)대한 디자인 지침을 참조하세요.
