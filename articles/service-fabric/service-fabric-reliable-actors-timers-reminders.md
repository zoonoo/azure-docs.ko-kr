---
title: "Reliable Actors 타이머 및 미리 알림 | Microsoft Docs"
description: "서비스 패브릭 Reliable Actors의 타이머 및 미리 알림에 대해 소개합니다."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 56b2e13c3bf053175e357a627d45a91d9a9a4ba9


---
# <a name="actor-timers-and-reminders"></a>행위자 타이머 및 미리 알림
행위자는 타이머 또는 미리 알림을 등록하여 정기적인 작업을 예약할 수 있습니다. 이 문서에서는 타이머와 미리 알림을 사용하는 방법을 보여 주고 둘 간의 차이점을 설명합니다.

## <a name="actor-timers"></a>행위자 타이머
행위자 타이머는 콜백 메서드가 행위자 런타임에서 제공하는 턴 기반 동시성 보증을 준수하는 .NET 타이머에 대한 간단한 래퍼를 제공합니다.

행위자는 기본 클래스에서 `RegisterTimer` 및 `UnregisterTimer` 메서드를 사용하여 해당 타이머를 등록 및 등록 취소할 수 있습니다. 아래 예제에서는 타이머 API의 사용 방법을 보여줍니다. API는 .NET 타이머와 매우 유사합니다. 이 예제의 경우 타이머가 만료되면 행위자 런타임에서는 `MoveObject` 메서드를 호출합니다. 메서드는 턴 기반 동시성을 준수해야 합니다. 즉, 다른 행위자 메서드나 타이머/미리 알림 콜백은 이 콜백의 실행이 완료될 때까지 진행되어야 합니다.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

다음 타이머 시간 간격은 콜백 실행이 완료된 후 시작됩니다. 이는 타이머 콜백이 실행되는 동안 타이머는 중지되고 콜백이 완료되면 시작된다는 것을 의미합니다.

행위자 런타임은 콜백이 완료되면 행위자의 상태 관리자에 대한 변경 내용을 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다. 

행위자가 가비지 수집의 일환으로 비활성화되면 모든 타이머가 중지됩니다. 그다음 타이머 콜백이 호출되지 않습니다. 또한 행위자 런타임은 비활성화 전에 실행 중이었던 타이머에 대한 정보를 유지하지 않습니다. 나중에 다시 활성화될 때 필요한 모든 타이머를 등록하는 것은 행위자의 일입니다. 자세한 내용은 [행위자 가비지 수집](service-fabric-reliable-actors-lifecycle.md)섹션을 참조하세요.

## <a name="actor-reminders"></a>행위자 미리 알림
미리 알림은 행위자에서 지정된 시간에 영구 콜백을 트리거하는 메커니즘입니다. 기능은 타이머와 비슷합니다. 하지만 타이머와 달리 미리 알림은 행위자가 명시적으로 등록을 취소하거나 행위자가 명시적으로 삭제할 때까지 모든 상황에서 트리거됩니다. 구체적으로, 미리 알림은 행위자 런타임이 행위자의 미리 알림에 대한 정보를 유지하므로 행위자 비활성화 및 장애 조치를 통해 트리거됩니다.

미리 알림을 등록하기 위해서는 행위자가 기본 클래스에서 제공된 `RegisterReminderAsync` 메서드를 아래 예제와 같이 호출합니다.

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

이 예제에서 `"Pay cell phone bill"` 은 미리 알림 이름입니다. 행위자가 미리 알림을 고유하게 식별하는 데 사용하는 문자열입니다. `BitConverter.GetBytes(amountInDollars)` 는 해당 미리 알림에 연결되는 컨텍스트입니다. 또한 이 값은 미리 알림 콜백의 인수( `IRemindable.ReceiveReminderAsync`)로 행위자에게 다시 전달됩니다.

미리 알림을 사용하는 행위자는 아래 예제에 나온 대로 `IRemindable` 인터페이스를 구현해야 합니다.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

미리 알림이 트리거되면 Reliable Actors 런타임에서 행위자에 대해 `ReceiveReminderAsync` 메서드를 호출합니다. 행위자는 미리 알림을 여러 개 등록할 수 있으며 `ReceiveReminderAsync` 메서드는 이러한 미리 알림 중 하나가 트리거되면 언제든지 호출됩니다. 행위자는 `ReceiveReminderAsync` 메서드로 전달되는 미리 알림 이름을 사용하여 미리 알림이 트리거되었는지 알아낼 수 있습니다.

행위자 런타임은 `ReceiveReminderAsync` 호출이 완료되면 행위자의 상태를 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다. 

미리 알림을 등록 취소하려면 행위자가 아래 예제에 나온 대로 `UnregisterReminderAsync` 메서드를 호출합니다.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```

위에 나온 것처럼 `UnregisterReminderAsync` 메서드는 `IActorReminder` 인터페이스를 허용합니다. 행위자 기본 클래스는 미리 알림 이름에 전달하여 `IActorReminder` 인터페이스를 검색하는 데 사용할 수 있는 `GetReminder` 메서드를 지원합니다. 이 방법은 행위자가 `RegisterReminder` 메서드에서 반환된 `IActorReminder` 인터페이스를 유지할 필요가 없기 때문에 편리합니다.

## <a name="next-steps"></a>다음 단계
* [행위자 이벤트](service-fabric-reliable-actors-events.md)
* [행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [샘플 코드](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->


