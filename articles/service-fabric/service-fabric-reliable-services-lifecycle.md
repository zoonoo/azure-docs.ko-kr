---
title: "Azure Service Fabric Reliable Services의 수명 주기 개요 | Microsoft Docs"
description: "Service Fabric Reliable Services의 다른 수명 주기 이벤트에 대해 알아보기"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: 1472bd1647b620d1f0489cdabbed58e72585aef7
ms.openlocfilehash: ad1f713f2b12af4f778b9b14edf33a568d2aa8b1

---

# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 수명 주기 개요
Reliable Services의 수명 주기를 고려할 경우 수명 주기에 대한 기본 사항이 가장 중요합니다. 일반적으로:

* 시작 중
  * 서비스가 생성됩니다.
  * 0개 이상의 수신기를 생성하고 반환할 수 있습니다.
  * 반환된 모든 수신기 열리면 서비스와의 통신이 가능합니다.
  * 서비스의 RunAsync 메서드를 호출하면 서비스가 장기 실행 중인 백그라운드 작업을 수행할 수 있습니다.
* 종료 중
  * RunAsync에 전달된 취소 토큰이 취소되고 수신기 닫힙니다.
  * 작업이 완료되면 서비스 개체 자체는 소멸됩니다.

이러한 이벤트의 정확한 순서에 대한 세부 정보가 있습니다. 특히 이벤트의 순서는 Reliable Service가 상태 비저장인지 또는 상태 저장인지에 따라 약간 변경될 수 있습니다. 또한 상태 저장 서비스의 경우 주 복제본 교환 시나리오를 처리해야 합니다. 이 과정 중에 서비스를 종료하지 않고 주 복제본의 역할을 다른 복제본으로 전송하거나 복구합니다. 마지막으로 오류 또는 실패 조건에 대해 고려해야 합니다.

## <a name="stateless-service-startup"></a>상태 비저장 서비스 시작
상태 비저장 서비스의 수명 주기는 매우 간단합니다. 이벤트의 순서는 다음과 같습니다.

1. 서비스를 생성합니다.
2. 그런 다음 두 가지 작업이 병렬로 수행됩니다.
    - `StatelessService.CreateServiceInstanceListeners()`이 호출되고 반환된 모든 수신기가 열립니다(`ICommunicationListener.OpenAsync()`이 각 수신기에서 호출됨).
    - 서비스의 RunAsync 메서드(`StatelessService.RunAsync()`)를 호출합니다.
3. 있는 경우 서비스의 OnOpenAsync 메서드를 호출합니다.(특히, `StatelessService.OnOpenAsync()`을 호출합니다. 이 항목은 일반적이지 않은 재정의이지만 사용 가능합니다).

수신기 및 RunAsync를 만들고 여는 호출 간에 순서가 없다는 점이 중요합니다. RunAsync가 시작되기 전에 수신기가 열릴 수 있습니다. 마찬가지로 통신 수신기를 열고 생성하기 전에 RunAsync를 호출할 수 있습니다. 동기화가 필요한 경우 구현 작업에 연습으로 남겨 둡니다. 일반적인 솔루션:

* 경우에 따라 다른 정보를 만들거나 수행할 때까지 수신기는 작동할 수 없습니다. 상태 비저장 서비스의 경우 일반적으로 서비스의 생성자에서 `CreateServiceInstanceListeners()`를 호출하는 동안이나 수신기 구성의 일부로 해당 작업을 수행할 수 있습니다.
* 경우에 따라 RunAsync의 코드는 수신기 열릴 때까지 시작하지 않습니다. 이 경우에 추가 조정이 필요합니다. 어떤 일반적인 솔루션은 완료된 시기를 나타내는 수신기 내의 일부 플래그이며 실제 작업을 계속하기 전에 RunAsync에서 확인됩니다.

## <a name="stateless-service-shutdown"></a>상태 비저장 서비스 종료
상태 비저장 서비스를 종료할 경우 동일한 패턴이 역방향으로 다음과 같이 따라옵니다.

1. 병렬로
    - 열려 있는 수신기 닫힙니다(각 수신기에서 `ICommunicationListener.CloseAsync()`을 호출함).
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다(취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고 토큰 `ThrowIfCancellationRequested` 메서드가 호출되면 `OperationCanceledException`를 반환함).
2. `CloseAsync()`이 각 수신기에 완료되고 `RunAsync()`도 완료되면 있는 경우 서비스의 `StatelessService.OnCloseAsync()` 메서드를 호출합니다(일반적이지 않은 재정의임).
3. `StatelessService.OnCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-startup"></a>상태 저장 서비스 시작
상태 저장 서비스는 몇 가지를 변경한 상태 비저장 서비스 패턴과 비슷합니다. 상태 저장 서비스를 시작하면 이벤트의 순서는 다음과 같습니다.

1. 서비스를 생성합니다.
2. `StatefulServiceBase.OnOpenAsync()`을 호출합니다. (서비스에서 일반적이지 않게 재정의됩니다.)
3. 문제가 발생한 서비스 복제본이 주 복제본인 경우 다음 작업이 병렬로 발생하거나 그렇지 않으면 서비스가 4단계로 건너뜁니다.
    - `StatefulServiceBase.CreateServiceReplicaListeners()`이 호출되고 반환된 모든 수신기가 열립니다(`ICommunicationListener.OpenAsync()`이 각 수신기에서 호출됨).
    - 서비스의 RunAsync 메서드(`StatefulServiceBase.RunAsync()`)를 호출합니다.
4. 모든 복제 수신기의 `OpenAsync()` 호출이 완료되고 `RunAsync()`가 시작되거나 이 복제본이 현재 보조 복제본이므로 이 단계를 건너뛰게 되면 `StatefulServiceBase.OnChangeRoleAsync()`이 호출됩니다. (서비스에서 일반적이지 않게 재정의됩니다.)

상태 비저장 서비스와 마찬가지로 수신기가 생성되고 열린 순서와 RunAsync를 호출하는 순서가 조정되지 않습니다. 솔루션은 한 가지 추가 사례를 제외하고 동일합니다. 통신 수신기에 도달한 호출이 작동하려면 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md) 내에 유지된 정보가 필요합니다. 신뢰할 수 있는 컬렉션을 읽기 가능하거나 쓰기 가능하게 되고 RunAsync가 시작하기 전에 통신 수신기를 열 수 있기 때문에 일부 추가 조정이 필요합니다. 가장 간단하고 일반적인 솔루션은 통신 수신기에서 클라이언트가 사용하는 몇 가지 오류 코드를 반환하여 요청을 다시 시도하는 것입니다.

## <a name="stateful-service-shutdown"></a>상태 저장 서비스 종료
상태 비저장 서비스와 마찬가지로 종료 중 수명 주기 이벤트는 시작 시와 동일하지만 역방향으로 이뤄집니다. 상태 저장 서비스가 종료되면 다음과 같은 이벤트가 발생합니다.

1. 병렬로
    - 열려 있는 수신기 닫힙니다(각 수신기에서 `ICommunicationListener.CloseAsync()`을 호출함).
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다(취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고 토큰 `ThrowIfCancellationRequested` 메서드가 호출되면 `OperationCanceledException`를 반환함).
2. `CloseAsync()`이 각 수신기에서 완료되고 `RunAsync()`도 완료되면(이 서비스 복제본이 주 복제본인 경우에만 필요함) 서비스의 `StatefulServiceBase.OnChangeRoleAsync()`이 호출됩니다. (서비스에서 일반적이지 않게 재정의됩니다.)
3. `StatefulServiceBase.OnChangeRoleAsync()` 메서드가 완료되면 `StatefulServiceBase.OnCloseAsync()` 메서드를 호출합니다(일반적이지 않은 재정의이지만 사용 가능함).
3. `StatefulServiceBase.OnCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-primary-swaps"></a>상태 저장 서비스 주 교환
상태 저장 서비스가 실행되는 동안 해당 상태 저장 서비스의 주 복제본은 해당 통신 수신기를 열고 해당 RunAsync 메서드를 호출합니다. 보조 복제본을 생성하지만 더 이상 호출하지 않습니다. 하지만 상태 저장 서비스가 실행되는 동안 현재 주 복제본인 복제본을 변경할 수 있습니다. 복제본이 확인할 수 있는 수명 주기 이벤트의 측면에서 무슨 의미인가요? 상태 저장 복제본이 확인하는 동작은 강등되는 복제본인지 승격되는 복제본인지에 따라 달라 집니다.

### <a name="for-the-primary-being-demoted"></a>강등되는 주 복제본의 경우
Service Fabric은 이 복제본에서 메시지 처리를 중지하고 수행 중인 모든 백그라운드 작업을 종료하도록 합니다. 결과적으로 이 단계는 서비스가 종료될 경우와 유사합니다. 한 가지 차이점은 서비스가 보조 복제본으로 그대로 남아 있으므로 소멸되거나 종료되지 않는다는 점입니다. 다음 API가 호출됩니다.

1. 병렬로
    - 열려 있는 수신기 닫힙니다(각 수신기에서 `ICommunicationListener.CloseAsync()`을 호출함).
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다(취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고 토큰 `ThrowIfCancellationRequested` 메서드가 호출되면 `OperationCanceledException`를 반환함).
2. `CloseAsync()`이 각 수신기에서 완료되고 `RunAsync()`도 완료되면 서비스의 `StatefulServiceBase.OnChangeRoleAsync()`이 호출됩니다. (서비스에서 일반적이지 않게 재정의됩니다.)

### <a name="for-the-secondary-being-promoted"></a>승격되는 보조 복제본의 경우
마찬가지로 Service Fabric은 이 복제본이 통신 중에 메시지를 수신 대기하고, 고려하는 모든 백그라운드 작업을 시작하도록 해야 합니다. 결과적으로 이 프로세스는 복제본 자체가 이미 존재한다는 점을 제외하면 서비스를 만들 때와 유사합니다. 다음 API가 호출됩니다.

1. 병렬로
    - `StatefulServiceBase.CreateServiceReplicaListeners()`이 호출되고 반환된 모든 수신기가 열립니다(`ICommunicationListener.OpenAsync()`이 각 수신기에서 호출됨).
    - 서비스의 RunAsync 메서드(`StatefulServiceBase.RunAsync()`)를 호출합니다.
4. 모든 복제 수신기의 `OpenAsync()` 호출이 완료되고 `RunAsync()`가 시작되거나 이 복제본이 보조 복제본이므로 이 단계를 건너뛰게 되면 `StatefulServiceBase.OnChangeRoleAsync()`이 호출됩니다. (서비스에서 일반적이지 않게 재정의됩니다.)

## <a name="notes-on-service-lifecycle"></a>서비스 수명 주기에 대한 참고 사항
* `RunAsync()` 메서드 및 `CreateServiceReplicaListeners/CreateServiceInstanceListeners` 호출은 모두 선택 사항입니다. 서비스에는 이러한 항목이 있거나 없을 수도 있습니다. 예를 들어, 서비스가 모든 작업을 사용자 호출에 대한 응답으로 수행할 경우 `RunAsync()`을(를) 구현할 필요가 없습니다. 통신 수신기 및 해당 관련 코드만이 필요합니다. 마찬가지로 통신 수신기를 만들고 반환하는 작업은 선택적이며 서비스가 백그라운드 작업을 수행해야 하므로 `RunAsync()`을 구현해야 합니다.
* 서비스가 `RunAsync()`을 성공적으로 완료하고 거기에서 반환하는 작업이 유효합니다. 실패 조건으로 간주되지 않고 완료 중인 서비스의 백그라운드 작업을 나타냅니다. 상태 저장 Reliable Services의 경우 서비스가 주 복제본에서 강등되는 경우 `RunAsync()`을 다시 호출한 다음 다시 주 복제본으로 승격됩니다.
* 서비스가 일부 예기치 않은 예외를 throw하여 `RunAsync()`에서 종료되는 경우 오류로서 서비스 개체가 종료되고 상태 오류가 보고됩니다.
* 이러한 메서드에서 반환에는 시간 제한이 없으며 신뢰할 수 있는 컬렉션에 작성할 수 있는 기능이 즉시 손실되고 모든 실제 작업을 완료할 수 없습니다. 취소 요청을 받는 즉시 최대한 신속하게 반환하는 것이 좋습니다. 서비스가 적절한 시간 내에 이러한 API 호출에 응답하지 않으면 Service Fabric은 서비스를 강제로 종료할 수 있습니다. 이러한 상황은 일반적으로 응용 프로그램을 업그레이드하거나 서비스를 삭제할 때 발생합니다. 이 시간 제한은 기본적으로 15분입니다.
* 상태 저장 서비스의 경우 보조 복제본에서 시작할 수 있는 ServiceReplicaListeners에 추가 옵션이 있습니다. 일반적이지 않지만 복제본이 보조 복제본인 경우에도 수명 주기에서 유일한 변경 내용은 `CreateServiceReplicaListeners()`을 호출하고 결과 수신기가 열린 것입니다. 마찬가지로 복제본이 나중에 주 복제본으로 변환되는 경우 수신기가 닫히고 소멸하며 새로 만들어지고 주 복제본에 대한 변경 내용의 일부로 열립니다.
* `OnCloseAsync()` 경로의 오류로 인해 `OnAbort()`가 호출되고 이것이 서비스에서 요구하는 리소스를 정리하고 릴리스할 수 있는 마지막 방법입니다.

## <a name="next-steps"></a>다음 단계
* [Reliable Services 소개](service-fabric-reliable-services-introduction.md)
* [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
* [신뢰할 수 있는 서비스 고급 사용법](service-fabric-reliable-services-advanced-usage.md)



<!--HONumber=Jan17_HO1-->


