<properties
   pageTitle="서비스 패브릭 서비스 개발"
   description="신뢰할 수 있는 행위자나 신뢰할 수 있는 서비스 프로그래밍 모델을 사용하여 서비스 패브릭 서비스를 개발하는 방법을 이해할 수 있도록 도와 주는 개념 정보 및 자습서입니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/14/2015"
   ms.author="ryanwi"/>

# 서비스 패브릭 서비스 개발
이 페이지에는 서비스 패브릭 서비스를 개발하는 방법을 배울 수 있는 자습서와 개요 및 개념 관련 문서에 대한 링크가 있습니다. 서비스 패브릭은 서비스 빌드에 대해 신뢰할 수 있는 작업자 API 및 신뢰할 수 있는 서비스 API라는 두 가지 높은 수준의 프로그래밍 모델을 제공합니다. 동일한 서비스 패브릭 코어에서 둘 모두 빌드하면 동시성, 분할 및 통신의 측면에서 단순성과 유연성 사이에서 서로 다르게 균형을 유지합니다. 이는 두 모델을 이해하는 데 유용하므로 응용 프로그램 내의 특정 서비스에 대해 적절한 프레임을 선택할 수 있습니다.

- [프로그래밍 모델 선택](service-fabric-choose-framework.md)
- [서비스 패브릭 행위자 모델 소개](service-fabric-reliable-actors-introduction.md)
- [신뢰할 수 있는 서비스 프로그래밍 모델 소개](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## 신뢰할 수 있는 행위자 프로그래밍 모델
 신뢰할 수 있는 행위자는 비동기, 단일 스레드 행위자 모델을 제공합니다. 행위자는 높은 확장성을 위해 클러스터 전체에 분산된 상태 및 계산의 단위를 나타냅니다. 신뢰할 수 있는 행위자 모델은 기본 서비스 패브릭 플랫폼이 제공하는 분산 저장소를 활용하여 응용 프로그램 개발자에 대해 높은 가용성과 일관성 있는 상태 관리를 제공합니다. 자세한 내용은 다음을 참조하십시오.

- [신뢰할 수 있는 행위자로 시작하기](service-fabric-reliable-actors-get-started.md)
- [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
- [패브릭 행위자가 서비스 패브릭 플랫폼을 사용하는 방법](service-fabric-reliable-actors-platform.md)
- [Azure 서비스 패브릭 행위자 형식 직렬화에 대한 참고 사항](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

행위자와의 통신은 다음에서 설명합니다.

- [서비스 패브릭 행위자 모델 소개](service-fabric-reliable-actors-introduction.md#actor-communication)
- [서비스와의 통신](service-fabric-connect-and-communicate-with-services.md)

이 문서에서는 유용한 디자인 패턴 및 시나리오를 설명합니다.

- [행위자 모델 디자인 패턴](service-fabric-reliable-actors-patterns-introduction.md)  
- [패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)
- [패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)
- [패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [패턴: 분산 계산](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [일부 패턴 방지](service-fabric-reliable-actors-anti-patterns.md)

단순 턴 기반 동시성이 신뢰할 수 있는 행위자 메서드에 제공됩니다. 동시성, 타이머와 알림, 및 다시 표시는 다음 문서에 설명되어 있습니다.

- [동시성](service-fabric-reliable-actors-introduction.md#concurrency)
- [동시성에 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md)
- [행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)
- [행위자 타이머](service-fabric-reliable-actors-timers-reminders.md)
 
다음에 있는 신뢰할 수 있는 행위자 구성에 대한 정보

- [KVSActorStateProvider 구성](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [신뢰할 수 있는 행위자 구성 - ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

신뢰할 수 있는 행위자는 서비스의 진단 및 모니터링에 사용할 수 있는 이벤트 및 성능 카운터를 내보냅니다.

- [행위자 진단](service-fabric-reliable-actors-diagnostics.md)
- [행위자 이벤트](service-fabric-reliable-actors-events.md)


## 신뢰할 수 있는 서비스 프로그래밍 모델
신뢰할 수 있는 서비스는 단순하고 강력한 최고 수준의 프로그래밍 모델을 제공하여 응용 프로그램에 중요한 내용을 나타낼 수 있도록 합니다. 자세한 내용은 다음을 참조하십시오.

- [신뢰할 수 있는 서비스로 시작하기](service-fabric-reliable-services-quick-start.md)
- [프로그래밍 모델 개요](../service-fabric-reliable-services-service-overview.md)  
- [아키텍처](service-fabric-reliable-services-platform-architecture.md)
- [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)
- [상태 저장 신뢰할 수 있는 서비스 구성](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [신뢰할 수 있는 서비스 프로그래밍 모델 고급 사용](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

다음에서는 신뢰할 수 있는 서비스와의 통신 및 클라이언트가 서비스 끝점을 발견하고 통신하는 데 사용할 수 있는 추상화를 설명합니다.

- [서비스와의 통신](service-fabric-connect-and-communicate-with-services.md)
- [서비스 통신 모델](service-fabric-reliable-services-communication.md)
- [신뢰할 수 있는 서비스 프레임워크가 제공하는 기본 통신 스택](service-fabric-reliable-services-communication-default.md)
- [신뢰할 수 있는 서비스에 대한 WCF 기반 통신 스택](service-fabric-reliable-services-communication-wcf.md)
- [OWIN 자체 호스팅(VS 2015 RC)으로 Microsoft Azure 서비스 패브릭 웹 API 서비스 시작](service-fabric-reliable-services-communication-webapi.md)

신뢰할 수 있는 서비스는 서비스의 진단 및 모니터링에 사용할 수 있는 이벤트 및 성능 카운터를 내보냅니다.

- [상태 저장 신뢰할 수 있는 서비스 진단](service-fabric-reliable-services-diagnostics.md)
 

<!---HONumber=July15_HO2-->