<properties
   pageTitle="Reliable Actors 다시 표시 | Microsoft Azure"
   description="서비스 패브릭 Reliable Actors의 다시 표시에 대해 소개합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>


# Reliable Actors 다시 표시
기본적으로 Reliable Actors 런타임을 사용하면 논리적 호출 컨텍스트를 기반으로 다시 표시할 수 있습니다. 따라서 동일한 호출 컨텍스트 체인에 있는 경우 행위자가 다시 표시되도록 할 수 있습니다. 예를 들어 행위자 A가 행위자 C에 메시지를 보내는 행위자 B에 메시지를 보내는 경우 메시지 처리 과정의 일부로 행위자 C가 행위자 A를 호출하면 해당 메시지가 다시 표시됩니다. 다른 호출 컨텍스트의 일부인 다른 모든 메시지는 처리를 완료할 때까지 행위자 A에서 차단됩니다.


`ActorReentrancyMode` 열거에 정의된 행위자 다시 표시에 두 개의 옵션을 사용할 수 있습니다.

 - `LogicalCallContext`(기본 동작)
 - `Disallowed` - 다시 표시 비활성화

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

다시 표시는 등록하는 동안 `ActorService`의 설정에서 구성될 수 있습니다. 행위자 서비스에서 만든 모든 행위자 인스턴스에 설정이 적용됩니다.

다음 예제에서는 다시 표시 모드를 `ActorReentrancyMode.Disallowed`로 설정하는 행위자 서비스를 보여 줍니다. 이 경우, 행위자가 다시 표시 메시지를 다른 행위자에게 보내면 `FabricException` 형식의 예외가 발생합니다.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context, 
                    actorType, () => new Actor1(), 
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```

## 다음 단계
 - [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
 - [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [샘플 코드](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->