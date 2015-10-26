<properties
   pageTitle="신뢰할 수 있는 행위자 다시 표시"
   description="서비스 패브릭 신뢰할 수 있는 행위자의 다시 표시에 대해 소개합니다."
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
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# 신뢰할 수 있는 행위자 다시 표시
기본적으로 패브릭 행위자를 사용하면 놀리 호출 컨텍스트를 기반으로 다시 표시할 수 있습니다. 따라서 동일한 호출 컨텍스트 체인에 있는 경우 행위자가 다시 표시되도록 할 수 있습니다. 예를 들어, 행위자 A가 행위자 C에 메시지를 보내는 행위자 B에 메시지를 보내는 경우, 메시지 처리 과정의 일부로 행위자 C가 행위자 A를 호출하면 해당 메시지가 다시 표시됩니다. 다른 호출 컨텍스트의 일부인 다른 모든 메시지는 처리를 완료할 때까지 행위자 A에서 차단됩니다.

논리적 호출 컨텍스트 기반 다시 표시를 허용하지 않는 행위자는 행위자 클래스를 `ReentrantAttribute(ReentrancyMode.Disallowed)`로 데코레이트하여 비활성화할 수 있습니다.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

다음 코드에서는 다시 표시 모드를 `ReentrancyMode.Disallowed`로 설정하는 행위자 클래스를 보여줍니다. 이 경우, 행위자가 다시 표시 메시지를 다른 행위자에게 보내면 `FabricException` 유형의 예외가 발생합니다.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=Oct15_HO3-->