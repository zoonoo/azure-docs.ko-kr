<properties
   pageTitle="일부 Azure 서비스 패브릭 행위자 안티패턴 | Microsoft Azure"
   description="Azure 서비스 패브릭 행위자에 대해 배우는 중인 고객에게 해당되는 일부 잠재적인 문제"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Reliable Actors 설계 패턴: 일부 안티패턴

서비스 패브릭 Reliable Actors에 대해 배우는 중인 고객에게서 다음과 같은 잠재적인 문제가 파악되었습니다.

* 신뢰할 수 있는 행위자를 트랜잭션 시스템으로 처리합니다. 서비스 패브릭 Reliable Actors는 원자성, 일관성, 격리성 및 영속성(ACID)을 제공하는 2단계 커밋 기반 시스템이 아닙니다. 선택적 지속성을 구현하지 않고 행위자가 실행되고 있는 컴퓨터가 중단된 경우 현재 상태가 중단됩니다. 행위자가 다른 노드에서 매우 빠르게 나타나도 지원 지속성을 구현하지 않으면 해당 상태는 사라집니다. 그러나 재시도, 중복 필터링 및/또는 idempotent 설계를 활용하여 높은 수준의 안정성과 일관성을 얻을 수 있습니다.

* 차단. Reliable Actors에서 수행하는 모든 것이 비동기여야 합니다. 이제 비동기 API가 Microsoft 플랫폼에서 많이 사용되므로 일반적으로 쉽습니다. 하지만 차단 API만 제공하는 시스템과 상호 작용해야 하는 경우 명시적으로 .NET 스레드 풀을 사용하는 래퍼에 두어야 합니다.

* Overarchitect 및 환경이 작동되도록 합니다. 동시 수집 및 잠금에 대해 걱정하거나 도구를 사용하여 XML에서 개체를 컴파일하는 데 익숙한 개발자에게는 변수 또는 일정 작업에 값을 할당하는 등의 간단한 작업을 수행하는 클래스를 코딩하는 것은 어려울 수 있습니다. 예약된 작업은 기본 제공이고 잠금은 필요하지 않습니다. 상태가 모든 문제의 근원은 아닙니다. 대규모 환경에서 수많은 서버 쪽 작업을 수행한 많은 개발자의 경우 이 작업에 익숙해지는 데 시간이 걸릴 수 있습니다.

* 단일 행위자를 병목 상태로 만듭니다. 수백만의 행위자가 다른 행위자의 단일 인스턴스를 통해 이동하기 때문에 쉽게 이 문제에 빠지게 됩니다. [분산 계산 디자인 패턴](service-fabric-reliable-actors-pattern-distributed-computation.md)에 보여준 집계 방식을 사용합니다.

* 엔터티 모델을 무작정 매핑합니다. 이것은 문제가 엔터티 및 그 관계를 사용하여 모델링되는 관계형 환경의 개발자에게 해당됩니다. 이 방법은 주체 도메인을 이해하는 데 유용하지만 서비스 지향 사고와 결합하고 동작과 혼합되어야 합니다.

<!---HONumber=AcomDC_0128_2016-->