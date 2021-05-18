---
title: Azure Service Fabric 애플리케이션 설계 모범 사례
description: Azure Service Fabric를 사용하여 애플리케이션과 서비스를 개발하기 위한 모범 사례 및 설계 고려 사항입니다.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91538501"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 애플리케이션 설계 모범 사례

이 문서에서는 Azure Service Fabric에서 애플리케이션과 서비스를 빌드하기 위한 모범 사례 지침을 제공합니다.
 
## <a name="get-familiar-with-service-fabric"></a>Service Fabric 살펴보기
* [Service Fabric이 궁금하신가요?](service-fabric-content-roadmap.md) 문서를 읽어봅니다.
* [Service Fabric 애플리케이션 시나리오](service-fabric-application-scenarios.md)를 읽어봅니다.
* [Service Fabric 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 읽고 프로그래밍 모델 선택 항목을 이해합니다.



## <a name="application-design-guidance"></a>애플리케이션 설계 지침
Service Fabric 애플리케이션의 [일반적인 아키텍처](/azure/architecture/reference-architectures/microservices/service-fabric) 와 [설계 고려 사항](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)을 살펴봅니다.

### <a name="choose-an-api-gateway"></a>API 게이트웨이 선택
스케일 아웃될 수 있는 백 엔드 서비스와 통신하는 API 게이트웨이 서비스를 사용합니다. 가장 일반적으로 사용되는 API 게이트웨이 서비스는 다음과 같습니다.

- [Service Fabric과 통합](./service-fabric-tutorial-deploy-api-management.md)된 [Azure API Management](./service-fabric-api-management-overview.md)
- [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor)를 사용하여 이벤트 허브 파티션에서 읽는 [Azure IoT Hub](../iot-hub/index.yml) 또는 [Azure Event Hubs](../event-hubs/index.yml)
- [Azure Service Fabric 공급자](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)를 사용하는 [Træfik 역방향 프록시](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
- [Azure Application Gateway](../application-gateway/index.yml).

   > [!NOTE] 
   > Azure Application Gateway는 Service Fabric과 직접 통합되지 않습니다. 일반적으로 Azure API Management를 선택하는 경우가 많습니다.
- 사용자 고유의 사용자 지정 빌드된 [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) 웹 애플리케이션 게이트웨이

### <a name="stateless-services"></a>상태 비저장 서비스
항상 [Reliable Services](./service-fabric-reliable-services-introduction.md)를 사용하여 상태 비저장 서비스를 먼저 빌드하고 Azure 데이터베이스, Azure Cosmos DB 또는 Azure Storage에 상태를 저장하는 것이 좋습니다. 외부화된 상태가 대부분의 개발자에게 더 친숙한 방법입니다. 이 방법을 사용하면 저장소에서 쿼리 기능도 활용할 수 있습니다.  

### <a name="when-to-use-stateful-services"></a>상태 저장 서비스를 사용해야 하는 경우
대기 시간이 짧은 시나리오를 위해 데이터와 컴퓨팅을 가까운 곳에 유지해야 하는 경우 상태 저장 서비스를 사용하는 것이 좋습니다. 몇 가지 예제 시나리오로 IoT 디지털 트윈 디바이스, 게임 상태, 세션 상태, 데이터베이스의 데이터 캐싱, 다른 서비스 호출을 추적하는 장기 실행 워크플로 등이 있습니다.

데이터 보존 시간 프레임을 결정합니다.

- **캐시된 데이터**. 외부 저장소에 대한 대기 시간이 이슈인 경우 캐싱을 사용합니다. 상태 저장 서비스를 사용자 고유의 데이터 캐시로 사용하거나 [오픈 소스 SoCreate Service Fabric 분산 캐시](https://github.com/SoCreate/service-fabric-distributed-cache)를 사용하는 것이 좋습니다. 이 시나리오에서는 캐시의 모든 데이터가 손실되더라도 걱정할 필요가 없습니다.
- **시간이 제한된 데이터**. 이 시나리오에서는 대기 시간을 위해 일정 기간 동안 데이터와 컴퓨팅을 가까운 곳에 유지해야 하지만 ‘재해’ 발생 시 데이터가 손실되어도 됩니다. 예를 들어 많은 IoT 솔루션에서는 지난 며칠 동안의 평균 온도를 계산하는 경우와 같이 데이터와 컴퓨팅을 가까운 곳에 유지해야 하지만 이 데이터가 손실된 경우 기록된 특정 데이터 요소는 그리 중요하지 않습니다. 또한 이 시나리오에서는 일반적으로 개별 데이터 요소 백업이 필요하지 않습니다. 정기적으로 외부 스토리지에 기록되는 컴퓨팅된 평균 값만 백업합니다.  
- **장기 데이터**. 신뢰할 수 있는 컬렉션은 데이터를 영구적으로 저장할 수 있습니다. 그러나 이 경우에는 클러스터에 대한 [정기 백업 정책 구성](./service-fabric-backuprestoreservice-configure-periodic-backup.md)을 포함하여 [재해 복구를 준비](./service-fabric-disaster-recovery.md)해야 합니다. 실제로 클러스터가 재해로 인해 제거될 경우 어떻게 되는지, 새 클러스터를 어디에 만들어야 하는지, 어떻게 새 애플리케이션 인스턴스를 배포하고 최신 백업에서 복구해야 하는지를 구성합니다.

비용 절감 및 가용성 향상:
- 상태 저장 서비스를 사용하면 원격 저장소에서 데이터 액세스 및 트랜잭션 비용이 발생하지 않고 Azure Cache for Redis와 같은 다른 서비스를 사용할 필요가 없기 때문에 비용을 줄일 수 있습니다.
- 상태 저장 서비스를 주로 스토리지에 사용하고 컴퓨팅에는 사용하지 않을 경우 비용이 많이 들기 때문에 권장되지 않습니다. 상태 저장 서비스는 저렴한 로컬 스토리지를 사용하는 컴퓨팅으로 간주하면 됩니다.
- 다른 서비스에 대한 종속성을 제거하여 서비스 가용성을 높일 수 있습니다. 클러스터에서 HA를 사용하여 상태를 관리하면 다른 서비스 가동 중지 시간 또는 대기 시간 이슈로부터 격리됩니다.

## <a name="how-to-work-with-reliable-services"></a>Reliable Services로 작업하는 방법
Service Fabric Reliable Services를 사용하면 상태 비저장 및 상태 저장 서비스를 쉽게 만들 수 있습니다. 자세한 내용은 [Reliable Services 소개](./service-fabric-reliable-services-introduction.md)를 참조하세요.
- 상태 비저장 및 상태 저장 서비스의 `RunAsync()` 메서드와 상태 저장 서비스의 `ChangeRole()` 메서드에서 항상 [취소 토큰](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)을 사용합니다. 사용하지 않으면 Service Fabric에서 서비스를 닫아도 되는지를 알 수 없습니다. 예를 들어 취소 토큰을 사용하지 않으면 애플리케이션 업그레이드 시간이 훨씬 길어질 수 있습니다.
-    적시에 [통신 수신기](./service-fabric-reliable-services-communication.md)를 열거나 닫고 취소 토큰을 사용합니다.
-    동기 코드와 비동기 코드를 함께 사용하지 않습니다. 예를 들어 비동기 호출에 `.GetAwaiter().GetResult()`를 사용해서는 안 됩니다. 호출 스택 ‘전체’에서 비동기를 사용합니다.

## <a name="how-to-work-with-reliable-actors"></a>Reliable Actors로 작업하는 방법
Service Fabric Reliable Actors를 사용하면 상태 저장 가상 작업자를 쉽게 만들 수 있습니다. 자세한 내용은 [Reliable Actors 소개](./service-fabric-reliable-actors-introduction.md)를 참조하세요.

- 작업자 간 게시/구독 메시지를 애플리케이션 스케일링에 사용하는 것이 좋습니다. 이 서비스를 제공하는 도구로 [오픈 소스 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 및 [Azure Service Bus](/azure/service-bus/)가 있습니다.
- 작업자를 [가능한 한 세분화](./service-fabric-reliable-actors-state-management.md#best-practices)합니다.
- [작업자의 수명 주기](./service-fabric-reliable-actors-state-management.md#best-practices)를 관리합니다. 작업자를 다시 사용하지 않으려는 경우 삭제합니다. 모든 상태는 메모리에 저장되기 때문에 [휘발성 상태 제공자](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)를 사용하는 경우 불필요한 작업자 삭제가 특히 중요합니다.
- 작업자는 [턴 기반 동시성](./service-fabric-reliable-actors-introduction.md#concurrency) 때문에 독립 개체로 사용하는 것이 가장 좋습니다. 다중 작업자 동기 메서드 호출(각각 별도의 네트워크 호출이 될 가능성이 높음) 그래프를 만들거나 순환 작업자 요청을 만들지 않습니다. 해당 작업은 성능 및 스케일링에 큰 영향을 줍니다.
- 동기 코드와 비동기 코드를 함께 사용하지 않습니다. 성능 이슈를 방지하기 위해 비동기를 일관되게 사용합니다.
- 작업자에서 장기 실행 호출을 수행하지 않습니다. 장기 실행 호출은 턴 기반 동시성 때문에 동일한 작업자에 대한 다른 호출을 차단합니다.
- [Service Fabric 원격](./service-fabric-reliable-services-communication-remoting.md)을 사용하여 다른 서비스와 통신하고 `ServiceProxyFactory`를 만드는 경우 작업자 수준이 ‘아닌’ [작업자 서비스](./service-fabric-reliable-actors-using.md) 수준에서 팩터리를 만듭니다.


## <a name="application-diagnostics"></a>애플리케이션 진단
서비스 호출에서 [애플리케이션 로깅](./service-fabric-diagnostics-event-generation-app.md)을 추가할 때는 주의해야 합니다. 그러면 서비스가 서로를 호출하는 시나리오를 진단하는 데 도움이 됩니다. 예를 들어 A가 B를 호출하고 B가 C를 호출하고 C가 D를 호출하는 경우 어디에서든 호출이 실패할 수 있습니다. 충분한 로깅이 없으면 오류를 진단하기 어렵습니다. 호출 볼륨 때문에 서비스 로깅이 너무 많은 경우 최소한 오류와 경고는 로깅해야 합니다.

## <a name="iot-and-messaging-applications"></a>IoT 및 메시징 애플리케이션
[Azure IoT Hub](../iot-hub/index.yml) 또는 [Azure Event Hubs](../event-hubs/index.yml)에서 메시지를 읽는 경우 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)를 사용합니다. ServiceFabricProcessor는 Service Fabric Reliable Services와 통합되어 이벤트 허브 파티션에서 읽기 상태를 유지하고 `IEventProcessor::ProcessEventsAsync()` 메서드를 통해 새 메시지를 서비스로 푸시합니다.


## <a name="design-guidance-on-azure"></a>Azure에 대한 설계 지침
* [Azure에서 마이크로 서비스를 빌드](/azure/architecture/microservices/)하는 방법에 대한 설계 지침은 [Azure 아키텍처 센터](/azure/architecture/microservices/)를 참조하세요.

* [게임 서비스에서 Service Fabric을 사용](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)하는 방법에 대한 설계 지침은 [게임용 Azure 시작](/gaming/azure/)을 참조하세요.
