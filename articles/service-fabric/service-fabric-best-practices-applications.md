---
title: Azure Service Fabric 응용 프로그램 설계 모범 사례 | Microsoft Docs
description: Service Fabric 응용 프로그램을 개발 하기 위한 모범 사례입니다.
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
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203449"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 응용 프로그램 설계 모범 사례

이 문서에서는 Azure Service Fabric에서 응용 프로그램 및 서비스를 구축 하기 위한 최선의 방법 지침을 제공 합니다.
 
## <a name="get-familiar-with-service-fabric"></a>Service Fabric에 알아보기
* 읽기를 [Service Fabric에 대해 학습 하려면 있도록?](service-fabric-content-roadmap.md) 문서.
* 읽어보세요 [Service Fabric 응용 프로그램 시나리오](service-fabric-application-scenarios.md)합니다.
* 참조 하 여 프로그래밍 모델 선택 항목을 이해 [Service Fabric 프로그래밍 모델 개요](service-fabric-choose-framework.md)합니다.



## <a name="application-design-guidance"></a>응용 프로그램 디자인 지침
에 대해 잘 알고 합니다 [일반 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) Service Fabric 응용 프로그램 및 해당 [디자인 고려 사항](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>API 게이트웨이 선택 합니다.
그런 다음 스케일 아웃할 수 있습니다 하는 백 엔드 서비스에 통신 하는 API 게이트웨이 서비스를 사용 합니다. 사용 되는 가장 일반적인 API 게이트웨이 서비스는 다음과 같습니다.

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), 즉 [Service Fabric과 통합](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)합니다.
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)를 사용 하 여 합니다 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) 이벤트 허브 파티션에서 읽을 수 있습니다.
- [Træfik 역방향 프록시](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)를 사용 하 여 합니다 [Azure Service Fabric 공급자](https://docs.traefik.io/configuration/backends/servicefabric/)합니다.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway Service Fabric과 직접 통합 되지 않습니다. Azure API Management는 일반적으로 기본으로 선택 합니다.
- 사용자 고유의 사용자 지정 빌드된 [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) 웹 응용 프로그램 게이트웨이.

### <a name="stateless-services"></a>상태 비저장 서비스
사용 하 여 상태 비저장 서비스를 구축 하 여 항상 시작 하는 것이 좋습니다 [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 및 Azure database, Azure Cosmos DB 또는 Azure Storage에서 상태를 저장 합니다. 구체화 된 상태는 대부분의 개발자를 위한 더 친숙 한 방법입니다. 또한이 이렇게를 사용 하면 저장소에 포함 된 쿼리 기능을 활용할 수 있습니다.  

### <a name="when-to-use-stateful-services"></a>상태 저장 서비스를 사용 하는 경우
짧은 대기 시간에 대 한 시나리오 및 계산에 가까운 데이터를 유지 해야 하는 경우 상태 저장 서비스를 고려 합니다. 몇 가지 예제 시나리오는 디지털 쌍둥이 장치 IoT, 게임 상태, 데이터베이스 및 기타 서비스에 대 한 호출을 추적 하는 장기 실행 워크플로에서 데이터 캐싱 세션 상태를 포함 합니다.

데이터 보존 기간 결정:

- **캐시 된 데이터**입니다. 외부 저장소 대기 시간에 문제가 있을 때 캐싱을 사용 합니다. 상태 저장 서비스 자체 데이터 캐시로 사용 또는 사용을 고려 합니다 [오픈 소스 SoCreate 서비스 패브릭 분산 캐시](https://github.com/SoCreate/service-fabric-distributed-cache)합니다. 이 시나리오에서는 필요가 없습니다를 고려해 야 캐시의 모든 데이터가 손실 된 경우.
- **시간 제한 데이터**입니다. 이 시나리오에서는 대기 시간 기간에 대 한 계산에 데이터를 유지 해야 하지만 손실 데이터를 이용할 수 있습니다는 *재해*합니다. 예를 들어, 많은 IoT 솔루션에서 지난 며칠 동안 평균 온도 계산 되는 경우와 같은 계산을 위해 닫기 데이터 해야 하지만이 데이터 손실 된 경우 기록 된 특정 데이터 요소에는 중요 하지 않습니다. 또한이 시나리오에서는 일반적으로 신경을 쓰지 않는 개별 데이터 요소를 백업 합니다. 외부 저장소에 주기적으로 기록 되는 계산 된 평균 값을 백업할만 있습니다.  
- **장기 데이터**입니다. 신뢰할 수 있는 컬렉션 데이터를 영구적으로 저장할 수 있습니다. 하지만 경우 필요가 [재해 복구 준비](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)등 [정기적인 백업 정책 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) 클러스터에 대 한 합니다. 실제로 클러스터 재해에 손상 된 경우 어떻게 되나요, 새 클러스터를 만들 해야 및 새 응용 프로그램 인스턴스를 배포 하 고 최신 백업에서 복구 하는 방법을 구성 합니다.

비용을 절감 하 고 가용성을 향상 시킵니다.
- 원격 저장소에서 데이터 액세스를 유발 하지 않으므로 상태 저장 서비스를 사용 하 여 비용 및 트랜잭션 비용을 절감 하 고 Redis에 대 한 Azure 캐시와 같은 다른 서비스를 사용할 필요가 없기 때문에 수 있습니다.
- 저장소 시스템용 및 계산에 대 한 상태 저장 서비스를 사용 하는 저렴 하 고 권장 하지 않습니다. 상태 저장 서비스 저렴 한 로컬 저장소를 사용 하 여 계산으로 간주 합니다.
- 다른 서비스에 대 한 종속성을 제거 하 여 서비스 가용성을 향상할 수 있습니다. 클러스터에서 HA 사용 하 여 상태를 관리 합니다. 다른 서비스 가동 중지 시간 또는 대기 시간 문제를 격리 합니다.

## <a name="how-to-work-with-reliable-services"></a>Reliable Services를 사용 하는 방법
Service Fabric Reliable Services를 사용 하면 상태 비저장 및 상태 저장 서비스를 쉽게 만들 수 있습니다. 자세한 내용은 참조는 [Reliable Services 소개](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)합니다.
- 항상 우선 합니다 [취소 토큰](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) 에 `RunAsync()` 상태 비저장 및 상태 저장 서비스에 대 한 메서드 및 `ChangeRole()` 상태 저장 서비스에 대 한 메서드. 이렇게 하지 않으면 Service Fabric 서비스를 닫을 수 있습니다 하는 경우 알지 못합니다. 예를 들어, 취소 토큰을 인식 하지 않습니다, 응용 프로그램 업그레이드 시간이 훨씬 더 발생할 수 있습니다.
-   열기 및 닫기 [통신 수신기](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) 시기 적절 한 방식으로 취소 토큰을 적용 합니다.
-   비동기 코드를 사용 하 여 동기화 코드를 혼합 하지 않아야 합니다. 예를 들어, 사용 하지 않는 `.GetAwaiter().GetResult()` 비동기 호출에 있습니다. 비동기를 사용 하 여 *움직인* 호출 스택을 통해.

## <a name="how-to-work-with-reliable-actors"></a>Reliable Actors를 사용 하는 방법
서비스 패브릭 Reliable Actors를 사용 하면 쉽게 가상, 상태 저장 행위자를 만들 수 있습니다. 자세한 내용은 참조는 [Reliable Actors 소개](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)합니다.

- 심각 하 게 응용 프로그램 크기 조정에 대 한 행위자 간의 메시징 pub/sub를 사용 하는 것이 좋습니다. 이 서비스를 제공 하는 도구가 포함 된 [오픈 소스 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) 하 고 [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/)합니다.
- 행위자 상태를 확인 하십시오 [최대한 세분화](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)합니다.
- 관리 된 [행위자의 수명 주기](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)합니다. 다시 사용 하도록 하려는 경우에 행위자를 삭제 합니다. 불필요 한 행위자를 삭제 하는 사용 하는 경우 특히 중요 합니다 [일시적 상태 공급자](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)이므로 모든 상태는 메모리에 저장 합니다.
- 때문에 해당 [턴 기반 동시성](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), 행위자는 독립적인 개체로 가장 적합 합니다. 다중 행위자, 동기 메서드 호출 (각각 가능성이 별도 네트워크 호출 됨)의 그래프 만들기 또는 순환 행위자 요청을 작성 하지 마세요. 이러한 성능 및 확장성을 크게 영향 을지 것입니다.
- 비동기 코드를 사용 하 여 동기화 코드를 혼합 하지 마세요. 성능 문제를 방지 하려면 비동기를 일관 되 게 사용 합니다.
- 행위자에서 장기 실행 호출을 수행 하지 마십시오. 장기 실행 호출이 턴 기반 동시성으로 인해 동일한 행위자에 대 한 다른 호출을 차단 합니다.
- 사용 하 여 다른 서비스와 통신 하는 경우 [Service Fabric remoting](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) 만들려는 및를 `ServiceProxyFactory`에서 팩터리를 만드는 합니다 [행위자 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) 수준 및 *하지* 행위자 수준입니다.


## <a name="application-diagnostics"></a>애플리케이션 진단
추가 하는 방법에 대 한 심층적 [응용 프로그램 로깅](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) 서비스 호출에서 합니다. 이 시나리오는 서비스 서로 호출을 진단 하는 데 도움이 됩니다. 예를 들어 b 호출 C D를 호출 하면 호출 아무 곳 이나 실패할 수 있습니다. 충분 한 로깅에 없는 경우 오류를 진단 하기 어려운는입니다. 서비스를 호출 볼륨으로 인해 너무 많이 로그인 하는 경우에 적어도 오류와 경고를 기록 하도록 해야 합니다.

## <a name="iot-and-messaging-applications"></a>IoT 및 메시징 응용 프로그램
메시지를 읽는 경우 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 하거나 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)를 사용 하 여 [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)합니다. 이벤트 허브에서 읽는 상태를 분할 및 새 메시지를 통해 서비스에 푸시를 유지 하기 위해 Service Fabric Reliable Services와 통합 되어 ServiceFabricProcessor는 `IEventProcessor::ProcessEventsAsync()` 메서드.


## <a name="design-guidance-on-azure"></a>Azure에 대 한 디자인 지침
* 방문 합니다 [Azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/microservices/) 디자인에 대 한 지침은 [Azure에서 마이크로 서비스 구축](https://docs.microsoft.com/azure/architecture/microservices/)합니다.

* 방문 [게임에 대 한 Azure를 사용 하 여 시작](https://docs.microsoft.com/gaming/azure/) 디자인에 대 한 지침은 [게임 서비스에서 Service Fabric을 사용 하 여](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)입니다.
