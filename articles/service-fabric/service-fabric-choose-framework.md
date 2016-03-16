<properties
   pageTitle="서비스 패브릭 프로그래밍 모델 | Microsoft Azure"
   description="서비스 패브릭은 서비스 빌드에 대해 행위자 프레임워크 및 서비스 프레임워크라는 두 가지 프레임워크를 제공합니다. 이 두 프레임 간에는 단순성과 제어 면에서 고유의 장단점이 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# 서비스에 대한 프레임워크 선택

Azure 서비스 패브릭은 서비스 빌드에 대해 Reliable Services API 및 Reliable Actors API라는 두 가지 높은 수준의 프레임워크를 제공합니다. 동일한 서비스 패브릭 코어에서 둘 모두 빌드하면 동시성, 분할 및 통신의 측면에서 단순성과 유연성 사이에서 서로 다르게 균형을 유지합니다. 이는 두 모델을 이해하는 데 유용하므로 응용 프로그램 내의 특정 서비스에 대해 적절한 프레임을 선택할 수 있습니다.

## Reliable Actors API 및 Reliable Services API 비교

|**Reliable Actors API를 선택해야 하는 경우**|**Reliable Services API를 선택해야 하는 경우**|
|-----------------------|--------------------------|
|문제 영역은 1,000개 이상의 많은 작고 독립적인 상태 및 논리 단위와 관련됩니다.|여러 구성 요소 간에 논리를 유지해야 합니다.|
|중요한 외부 조작이 필요 없는 단일 스레드 개체 작업을 수행하려고 합니다.|신뢰할 수 있는 컬렉션(.NET 신뢰할 수 있는 사전 및 신뢰할 수 있는 큐 등)을 사용하여 상태를 저장하고 관리하고자 합니다.|
|통신을 관리할 플랫폼을 원합니다.|서비스에 대한 통신을 제어하고 분할된 체계를 제어하고자 합니다.|

앱 내에서 서로 다른 서비스에 대해 서로 다른 프레임워크를 사용하는 것이 지극히 합리적임을 기억하십시오. 예를 들어 행위자 수로 생성된 데이터를 집계하는 상태 저장 서비스를 가질 수 있습니다.

## 다음 단계

- [Reliable Actors API에 대해 알아보기](service-fabric-reliable-actors-introduction.md)
- [Reliable Services API에 대해 알아보기](service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0211_2016-->