<properties
   pageTitle="신뢰할 수 있는 행위자 수명 주기 | Microsoft Azure"
   description="서비스 패브릭 신뢰할 수 있는 행위자에 대한 수명 주기 및 가비지 수집을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="myamanbh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/15/2016"
   ms.author="amanbha"/>


# 행위자 수명 주기 및 가비지 수집
첫 번째 호출이 수행되면 행위자가 활성화되고 일정 기간 동안 사용되지 않으면 비활성화됩니다(행위자 런타임에 의한 가비지 수집). 이 기간을 구성하려면 아래의 행위자 가비지 수집 섹션을 참조하세요.

행위자 활성화 시 수행하는 작업

- 행위자를 호출하는 경우 아직 활성화되지 않았으면 새 행위자를 만듭니다.
- 행위자의 상태가 로드됩니다(상태 저장 행위자인 경우).
- `OnActivateAsync` 메서드(행위자 구현 시 재정의될 수 있음)를 호출합니다.
- 활성 행위자 테이블에 행위자가 추가됩니다.

행위자 비활성화 시 수행하는 작업

- 행위자를 일정 기간 동안 사용하지 않으면 활성 행위자 테이블에서 제거됩니다.
- `OnDeactivateAsync` 메서드(행위자 구현 시 재정의될 수 있음)를 호출합니다. 그러면 행위자에 대한 모든 타이머가 지워집니다.

> [AZURE.TIP] 패브릭 행위자 런타임에서는 일부 [행위자 활성화 및 비활성화 관련 이벤트](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

## 행위자 가비지 수집
행위자 런타임은 일정 기간 동안 사용되지 않은 행위자를 주기적으로 스캔하여 비활성화합니다. 비활성화되고 나면 행위자는 CLR(공용 언어 런타임)에서 수집된 가비지일 수 있습니다.

가비지 수집 용도로 "사용 중"으로 계산되는 경우

- 호출 수신
- 호출 중인 `IRemindable.ReceiveReminderAsync` 메서드(행위자가 미리 알림을 사용하는 경우에만 해당).

행위자가 타이머를 사용하고 해당 타이머 콜백이 호출된 경우 "사용 중"으로 계산**하지** 않습니다.

가비지 수집에 대한 세부 정보를 살펴보기 전에 다음과 같은 용어를 정의하는 것이 중요합니다.

- *스캔 간격*. 행위자 런타임이 가비지 수집될 수 있는 행위자에 대해 해당 활성 행위자 테이블을 스캔하는 간격입니다. 기본값은 1분입니다.
- *유휴 시간 제한*. 행위자가 가비지 수집되기 전에 미사용(유휴) 상태를 유지해야 하는 시간. 기본값은 60분입니다.

일반적으로 이 기본값은 변경할 필요가 없습니다. 하지만 필요한 경우 이러한 간격은 어셈블리의 모든 행위자 형식에 대해 해당 어셈블리 수준에서 또는 `ActorGarbageCollection` 특성을 사용하여 행위자 형식 수준에서 변경할 수 있습니다. 다음 예제에서는 HelloActor에 대한 가비지 수집 간격 변경 방법을 보여줍니다.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

어셈블리 수준에서 `ActorGarbageCollection` 특성의 기본값을 변경하려면 다음 코드 조각을 `AssemblyInfo.cs`에 추가합니다.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

해당 활성 행위자 테이블에 있는 각 행위자의 경우 행위자 런타임은 유휴 상태였던 시간(사용되지 않은 시간)을 기록합니다. 행위자 런타임은 `ScanIntervalInSeconds`마다 각 행위자를 검사하여 가비지 수집 가능한지 확인하고 `IdleTimeoutInSeconds` 동안 유휴 상태였는지 수집합니다.

행위자를 사용할 때마다 유휴 시간이 0으로 다시 설정됩니다. 이후부터는 `IdleTimeoutInSeconds` 동안 다시 유휴 상태인 경우에만 행위자가 가비지 수집됩니다. 행위자는 행위자 미리 알림 콜백이 실행되는 행위자 인터페이스 메서드 중 하나인 경우, 사용된 것으로 간주됩니다. 타이머 콜백이 실행되는 경우 해당 행위자는 사용된 것으로 간주되지 **않습니다**.

아래 다이어그램에는 이러한 개념을 설명하는 예가 포함되어 있습니다.

![유휴 시간의 예][1]

이 예제에서는 활성 행위자 테이블에 활성 행위자가 하나만 있다고 가정합니다. 이 예제에서는 행위자 메서드 호출에 대한 영향 및 이 행위자의 수명에 대한 타이머를 보여 줍니다. 이 예제에 대해 다음과 같은 사항에 주의해야 합니다.

- ScanInterval 및 IdleTimeout은 각각 5와 10으로 설정합니다. 이 예제에서는 개념만 보여 주므로 단위는 중요하지 않습니다.
- 스캔 간격이 5로 정의되어 있으므로 가비지 수집할 행위자의 스캔은 T=0,5,10,15,20,25에서 수행됩니다.
- 주기적 타이머는 T=4,8,12,16,20,24에서 발생하며 해당 콜백이 실행됩니다. 행위자의 유휴 시간에는 영향을 주지 않습니다.
- 행위자는 T=7에서 호출되고 유휴 시간을 0으로 재설정하고 행위자의 가비지 수집을 지연시킵니다.
- 행위자 미리 알림 콜백은 T=14에서 실행되며 추가로 행위자의 가비지 수집이 지연됩니다.
- T=25에서 가비지 수집 스캔이 수행되는 동안, 결국 행위자의 유휴 시간이 10으로 설정된 유휴 시간 제한을 초과하고 행위자는 가비지 수집됩니다.

메서드 중 하나를 실행하는 동안에는 해당 메서드를 실행하는 데 소요되는 시간에 상관 없이 행위자는 절대 가비지 수집되지 않습니다. 앞서 설명한 대로 행위자 인터페이스 메서드 및 미리 알림 콜백을 실행하면 행위자의 유휴 시간을 0으로 다시 설정하여 가비지 수집을 방지합니다. 타이머 콜백 실행의 경우는 유휴 시간이 0으로 설정되지 않습니다. 하지만 행위자의 가비지 수집은 타이머 콜백의 실행이 완료될 때까지는 지연됩니다.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_0316_2016-->