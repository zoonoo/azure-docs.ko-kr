<properties
   pageTitle="Azure 서비스 패브릭 행위자 타이머 및 미리 알림"
   description="Azure 서비스 패브릭 행위자의 타이머 및 미리 알림에 대해 소개합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="amanbha"/>


# 행위자 타이머
행위자 타이머는 콜백 메서드가 행위자 런타임에서 제공하는 턴 기반 동시성 보증을 준수하는 .NET 타이머에 대한 간단한 래퍼를 제공합니다.

행위자는 기본 클래스에서 `RegisterTimer` 및 `UnregisterTimer` 메서드를 사용하여 해당 타이머를 등록 및 등록 취소할 수 있습니다. 아래 예제에서는 타이머 API의 사용 방법을 보여줍니다. API는 .NET 타이머와 매우 유사합니다. 아래 예에서 타이머가 만료되면 `MoveObject` 메서드가 행위자 런타임에 의해 호출되며 턴 기반 동시성 준수가 보장됩니다. 따라서 이 콜백이 실행을 완료할 때까지 다른 행위자 메서드 또는 타이머/미리 알림 콜백은 진행되지 않습니다.

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
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
        return TaskDone.Done;
    }
}
```

다음 타이머 시간 간격은 콜백 실행이 완료된 후 시작됩니다. 이는 타이머 콜백이 실행되는 동안 타이머는 중지되고 콜백이 완료되면 시작된다는 것을 의미합니다.

행위자 런타임은 행위자가 위의 예제와 같이 상태 저장 행위자인 경우 콜백이 완료되면 행위자 상태를 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다. 행위자 상태를 수정하지 않는 콜백 메서드는 [읽기 전용 메서드](service-fabric-reliable-actors-introduction.md#readonly-methods) 관련 섹션에서 설명한 대로, 타이머 콜백에 대해 읽기 전용 특성을 지정하여 읽기 전용 타이머 콜백으로 등록할 수 있습니다.

행위자가 가비지 수집의 일환으로 비활성화되고 그 후 타이머 콜백이 호출되지 않으면 모든 타이머가 중지됩니다. 또한 행위자 런타임은 비활성화 전에 실행 중이었던 타이머에 대한 정보를 유지하지 않습니다. 나중에 다시 활성화될 때 필요한 모든 타이머를 등록하는 것은 행위자의 일입니다. 자세한 내용은 [행위자 가비지 수집](service-fabric-reliable-actors-lifecycle.md) 섹션을 참조하세요.

## 행위자 미리 알림
미리 알림은 행위자에서 지정된 시간에 영구 콜백을 트리거하는 메커니즘입니다. 기능은 타이머와 비슷하지만 타이머와 달리 미리 알림은 미리 알림이 행위자에 의해 명시적으로 등록 취소될 때까지 어떠한 경우든 트리거됩니다. 구체적으로, 미리 알림은 행위자 런타임이 행위자의 미리 알림에 대한 정보를 유지하므로 행위자 비활성화 및 장애 조치를 통해 트리거됩니다.

미리 알림은 상태 저장 행위자에 대해서만 지원됩니다. 상태 비저장 행위자는 미리 알림을 사용할 수 없습니다. 행위자가 등록한 미리 알림에 대한 정보는 행위자 상태 제공자가 저장해야 합니다.

미리 알림을 등록하기 위해서는 행위자가 기본 클래스에서 제공된 `RegisterReminder` 메서드를 아래 예에 나온 대로 호출합니다.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

위의 예에서 `"Pay cell phone bill"`은 미리 알림 이름으로, 행위자가 미리 알림을 고유하게 식별하는 데 사용하는 문자열입니다. `BitConverter.GetBytes(amountInDollars)`는 해당 미리 알림에 연결되는 컨텍스트입니다. 또한 이 값은 미리 알림 콜백의 인수(`IRemindable.ReceiveReminderAsync`)로 행위자에게 다시 전달됩니다.

미리 알림을 사용하는 행위자는 아래 예에 나온 대로 `IRemindable` 인터페이스를 구현해야 합니다.

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
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

미리 알림이 트리거되면 패브릭 행위자 런타임에서 행위자에 대해 `ReceiveReminderAsync` 메서드를 호출합니다. 행위자는 미리 알림을 여러 개 등록할 수 있으며 `ReceiveReminderAsync` 메서드는 이러한 미리 알림 중 하나가 트리거되면 언제든지 호출됩니다. 행위자는 `ReceiveReminderAsync` 메서드로 전달되는 미리 알림 이름을 사용하여 미리 알림이 트리거되었는지 알아낼 수 있습니다.

행위자 런타임은 `ReceiveReminderAsync` 호출이 완료되면 행위자 상태를 저장합니다. 상태를 저장하는 중에 오류가 발생하는 경우 해당 행위자 개체는 비활성화되고 새 인스턴스가 활성화됩니다. 미리 알림 콜백 완료 시 상태가 저장되지 않도록 지정하려면 `RegisterReminder` 메서드를 호출하여 미리 알림을 만들 때 `ActorReminderAttributes.ReadOnly` 플래그를 `attributes` 매개 변수에 설정할 수 있습니다.

미리 알림을 등록 취소하려면 아래 예에 나온 대로 `UnregisterReminder` 메서드를 호출해야 합니다.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

위에 나온 것처럼 `UnregisterReminder` 메서드는 `IActorReminder` 인터페이스를 허용합니다. 행위자 기본 클래스는 미리 알림 이름에 전달하여 `IActorReminder` 인터페이스를 검색하는 데 사용할 수 있는 `GetReminder` 메서드를 지원합니다. 이 방법은 행위자가 `RegisterReminder` 메서드에서 반환된 `IActorReminder` 인터페이스를 유지할 필요가 없기 때문에 편리합니다.
 

<!---HONumber=July15_HO2-->