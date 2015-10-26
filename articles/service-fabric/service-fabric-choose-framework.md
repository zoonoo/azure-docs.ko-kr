<properties
   pageTitle="서비스 패브릭 프레임워크 선택 | Microsoft Azure"
   description="서비스 패브릭은 서비스 빌드에 대해 행위자 프레임워크 및 서비스 프레임워크라는 두 가지 높은 수준의 프레임워크를 제공합니다. 각 값을 이해하면 응용 프로그램에 대해 올바른 아키텍처를 결정하는 데 도움이 될 것입니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2015"
   ms.author="seanmck"/>

# 서비스에 대한 프레임워크 선택

서비스 패브릭은 서비스 빌드에 대해 신뢰할 수 있는 행위자 API 및 신뢰할 수 있는 서비스 API라는 두 가지 높은 수준의 프레임워크를 제공합니다. 동일한 서비스 패브릭 코어에서 둘 모두 빌드하면 동시성, 분할 및 통신의 측면에서 단순성과 유연성 사이에서 서로 다르게 균형을 유지합니다. 이는 두 모델을 이해하는 데 유용하므로 응용 프로그램 내의 특정 서비스에 대해 적절한 프레임을 선택할 수 있습니다.

## 신뢰할 수 있는 행위자 API와 신뢰할 수 있는 서비스 API 비교

|**신뢰할 수 있는 행위자 API**|**신뢰할 수 있는 서비스 API**|
|-----------------------|--------------------------|
|문제 영역은 상태 및 논리의 많은 작은 독립적인 단위와 관련됩니다.|여러 구성 요소 간에 논리를 유지해야 합니다.|
|규모 조정이 가능하고 일관성을 유지하면서 단일 스레드 개체로 작업하고자 합니다.|신뢰할 수 있는 컬렉션(.NET 디렉터리 및 큐 등)을 사용하여 상태를 저장하고 관리하고자 합니다.|
|상태의 동시성 및 세분성을 관리하는 프레임워크를 원합니다.|상태의 세분성 및 동시성을 제어하고자 합니다.|
|통신을 관리할 플랫폼을 원합니다.|서비스에 대한 통신을 제어하고 분할된 체계를 제어하고자 합니다.|

앱 내에서 서로 다른 서비스에 대해 서로 다른 프레임워크를 사용하는 것이 지극히 합리적임을 기억하십시오. 예를 들어 행위자 수로 생성된 데이터를 집계하는 상태 저장 서비스를 가질 수 있습니다.

## 다음 단계

- [신뢰할 수 있는 행위자 API에 대해 알아보기](service-fabric-reliable-actors-introduction.md)
- [신뢰할 수 있는 서비스 API에 대해 알아보기](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=Oct15_HO3-->