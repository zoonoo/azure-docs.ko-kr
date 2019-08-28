---
title: Azure Service Fabric 응용 프로그램 디자인 모범 사례 | Microsoft Docs
description: Service Fabric 응용 프로그램 개발을 위한 최선의 구현 방법입니다.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 06af1f4326e3f6a6dcb53c8710a126f43e2d2f6a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875111"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 응용 프로그램 디자인 모범 사례

이 문서에서는 Azure Service Fabric에서 응용 프로그램 및 서비스를 빌드하기 위한 모범 사례 지침을 제공 합니다.
 
## <a name="get-familiar-with-service-fabric"></a>Service Fabric에 대해 알아보기
* [Service Fabric에 대해 알아보려면](service-fabric-content-roadmap.md) 다음 문서를 참조 하세요.
* [Service Fabric 응용 프로그램 시나리오](service-fabric-application-scenarios.md)에 대해 읽어 보세요.
* 프로그래밍 모델 선택 [Service Fabric 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조 하세요.



## <a name="application-design-guidance"></a>응용 프로그램 디자인 지침
Service Fabric 응용 프로그램의 [일반적인 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) 와 해당 [디자인 고려 사항을](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)익힙니다.

### <a name="choose-an-api-gateway"></a>API 게이트웨이 선택
확장할 수 있는 백 엔드 서비스와 통신 하는 API gateway 서비스를 사용 합니다. 가장 일반적으로 사용 되는 API gateway 서비스는 다음과 같습니다.

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview) [Service Fabric와 통합](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)됩니다.
- [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) 를 사용 하 여 이벤트 허브 파티션에서 읽는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
- [Træfik 역방향 프록시](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)를 사용 하 여 [Azure Service Fabric 공급자](https://docs.traefik.io/configuration/backends/servicefabric/)를 사용 합니다.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure 애플리케이션 게이트웨이는 Service Fabric와 직접 통합 되지 않습니다. Azure API Management은 일반적으로 선호 되는 옵션입니다.
- 사용자 고유의 사용자 지정 빌드 [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) 웹 응용 프로그램 게이트웨이.

### <a name="stateless-services"></a>상태 비저장 서비스
항상 [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 를 사용 하 여 상태 비저장 서비스를 빌드하고 Azure 데이터베이스, Azure Cosmos DB 또는 Azure Storage에 상태를 저장 하는 것이 좋습니다. 표면화 된 상태는 대부분의 개발자에 게 친숙 한 방법입니다. 이 방법을 사용 하면 저장소의 쿼리 기능을 활용할 수도 있습니다.  

### <a name="when-to-use-stateful-services"></a>상태 저장 서비스를 사용 하는 경우
대기 시간이 짧고 데이터를 계산에 가깝게 유지 해야 하는 경우 상태 저장 서비스를 고려 합니다. 몇 가지 예제 시나리오에는 IoT 디지털 쌍 장치, 게임 상태, 세션 상태, 데이터베이스의 데이터 캐싱 및 다른 서비스에 대 한 호출을 추적 하는 장기 실행 워크플로가 포함 되어 있습니다.

데이터 보존 시간 프레임을 결정 합니다.

- **캐시 된 데이터**입니다. 외부 저장소에 대 한 대기 시간이 문제인 경우 캐싱을 사용 합니다. 사용자 고유의 데이터 캐시로 상태 저장 서비스를 사용 하거나 [오픈 소스 SoCreate Service Fabric 분산 캐시](https://github.com/SoCreate/service-fabric-distributed-cache)를 사용 하는 것이 좋습니다. 이 시나리오에서는 캐시의 모든 데이터가 손실 되는 경우 걱정 하지 않아도 됩니다.
- **시간 범위 데이터**. 이 시나리오에서는 대기 시간에 대해 일정 기간 동안 데이터를 가까이 유지 해야 하지만 *재해가*발생 해도 데이터가 손실 될 수 있습니다. 예를 들어 많은 IoT 솔루션에서는 지난 며칠 동안의 평균 온도가 계산 되는 경우와 같이 데이터를 계산에 가까이 두어야 하지만,이 데이터가 손실 된 경우에는 기록 된 특정 데이터 요소가 중요 하지 않습니다. 또한이 시나리오에서는 개별 데이터 요소를 백업 하는 방법을 일반적으로 고려 하지 않습니다. 정기적으로 외부 저장소에 기록 되는 계산 된 평균 값만 백업 합니다.  
- **장기적인 데이터**. 신뢰할 수 있는 컬렉션은 데이터를 영구적으로 저장할 수 있습니다. 그러나이 경우에는 클러스터에 대 한 [주기적인 백업 정책 구성을](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) 포함 하 여 [재해 복구를 준비](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)해야 합니다. 실제로 클러스터가 재해에서 제거 되는 경우 발생 하는 상황, 새 클러스터를 만들어야 하는 경우, 새 응용 프로그램 인스턴스를 배포 하 고 최신 백업에서 복구 하는 방법을 구성 합니다.

비용 절감 및 가용성 향상:
- 원격 저장소에서 데이터 액세스 및 트랜잭션 비용이 발생 하지 않으며 Redis 용 Azure Cache와 같은 다른 서비스를 사용할 필요가 없기 때문에 상태 저장 서비스를 사용 하 여 비용을 줄일 수 있습니다.
- 주로 저장소에 대해 상태 저장 서비스를 사용 하 고 계산에는 사용 하지 않는 것이 좋지만 권장 하지 않습니다. 상태 저장 서비스는 로컬 저장소가 저렴 한 계산으로 생각 하면 됩니다.
- 다른 서비스에 대 한 종속성을 제거 하 여 서비스 가용성을 향상 시킬 수 있습니다. 클러스터에서 HA를 사용 하 여 상태를 관리 하는 경우 다른 서비스 가동 중지 시간 또는 대기 시간 문제를 격리 합니다.

## <a name="how-to-work-with-reliable-services"></a>Reliable Services로 작업 하는 방법
Service Fabric Reliable Services를 사용 하면 상태 비저장 및 상태 저장 서비스를 쉽게 만들 수 있습니다. 자세한 내용은 [Reliable Services 소개](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)를 참조 하세요.
- 상태 비저장 및 상태 저장 서비스와 `RunAsync()` `ChangeRole()` 상태 저장 서비스에 대 한 메서드에는 항상 [취소 토큰](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) 을 적용 합니다. 그렇지 않은 경우에는 Service Fabric 서비스를 닫을 수 있는지 알 수 없습니다. 예를 들어 취소 토큰을 고려 하지 않는 경우 훨씬 긴 응용 프로그램 업그레이드 시간이 발생할 수 있습니다.
-   적시에 [통신 수신기](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) 를 열고 닫고 취소 토큰을 적용 합니다.
-   동기화 코드와 비동기 코드를 혼합 하지 마세요. 예를 들어 비동기 호출 `.GetAwaiter().GetResult()` 에는를 사용 하지 마세요. 호출 스택을 통해 비동기 *방식으로 비동기를* 사용 합니다.

## <a name="how-to-work-with-reliable-actors"></a>Reliable Actors로 작업 하는 방법
Service Fabric Reliable Actors를 사용 하면 상태 저장 가상 행위자를 쉽게 만들 수 있습니다. 자세한 내용은 [Reliable Actors 소개](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)를 참조 하세요.

- 응용 프로그램의 크기를 조정 하기 위해 행위자 간에 pub/sub 메시지를 사용 하는 것이 심각 합니다. 이 서비스를 제공 하는 도구에는 [공개 소스 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 및 [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/)가 포함 됩니다.
- 행위자 상태를 [최대한 세부적](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)으로 만듭니다.
- [행위자의 수명 주기](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)를 관리 합니다. 행위자를 다시 사용 하지 않으려는 경우 삭제 합니다. 불필요 한 행위자를 삭제 하는 것은 모든 상태가 메모리에 저장 되기 때문에 [일시적 상태 공급자](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)를 사용 하는 경우에 특히 중요 합니다.
- 이러한 [동시성 기반 동시성](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)때문에 행위자는 독립적 개체로 사용 되는 것이 가장 좋습니다. 다중 행위자, 동기 메서드 호출 (각각 별도의 네트워크 호출이 될 가능성이 높음)의 그래프를 만들지 않거나 순환 행위자 요청을 만듭니다. 이러한 기능은 성능 및 확장성에 큰 영향을 줍니다.
- 동기화 코드와 비동기 코드를 혼합 하지 마세요. 비동기를 일관 되 게 사용 하 여 성능 문제를 방지 합니다.
- 행위자에서 장기 실행 호출을 수행 하지 마세요. 장기 실행 호출은 턴 기반 동시성 때문에 동일한 행위자에 대 한 다른 호출을 차단 합니다.
- [Service Fabric 원격](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) 기능을 사용 하 여 다른 서비스와 통신 하 고를 `ServiceProxyFactory`만드는 경우 행위자 수준이 *아닌* [행위자 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) 수준에서 팩터리를 만듭니다.


## <a name="application-diagnostics"></a>애플리케이션 진단
서비스 호출에서 [응용 프로그램 로깅](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) 추가에 대해 자세히 알아봅니다. 서비스가 서로를 호출 하는 시나리오를 진단 하는 데 도움이 됩니다. 예를 들어 A가 B를 호출 하는 경우 C를 호출 하면 모든 위치에서 호출이 실패할 수 있습니다. 충분 한 로깅이 없으면 오류를 진단 하기 어렵습니다. 호출 볼륨으로 인해 서비스가 너무 많이 로깅 되는 경우에는 적어도 오류 및 경고 로그를 기록해 야 합니다.

## <a name="iot-and-messaging-applications"></a>IoT 및 메시징 응용 프로그램
[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)에서 메시지를 읽는 경우 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)를 사용 합니다. ServiceFabricProcessor는 Service Fabric Reliable Services와 통합 되어 이벤트 허브 파티션에서 읽기 상태를 유지 하 고 메서드를 `IEventProcessor::ProcessEventsAsync()` 통해 새 메시지를 서비스로 푸시합니다.


## <a name="design-guidance-on-azure"></a>Azure에 대 한 디자인 지침
* Azure [에서 마이크로 서비스를 구축](https://docs.microsoft.com/azure/architecture/microservices/)하는 방법에 대 한 디자인 지침은 [azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/microservices/) 를 참조 하세요.

* 게임 [서비스에서 Service Fabric 사용](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)에 대 한 디자인 지침은 [게임을 위한 Azure 시작](https://docs.microsoft.com/gaming/azure/) 을 참조 하세요.
