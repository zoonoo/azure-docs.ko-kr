<properties
   pageTitle="행위자 형식 Serialization에 대한 신뢰할 수 있는 행위자 참고 사항"
   description="서비스 패브릭의 신뢰할 수 있는 행위자 상태 및 인터페이스를 정의하는 데 사용할 수 있는 직렬화 가능 클래스를 정의하기 위한 기본 요구 사항 설명"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="claudioc"/>

# 서비스 패브릭 신뢰할 수 있는 행위자 형식 직렬화에 대한 참고 사항

행위자의 인터페이스 및 상태를 정의하는 동안 형식이 데이터 계약 직렬화 가능해야 하는 등, 염두에 두어야 하는 몇 가지 중요한 사항이 있습니다. 데이터 계약에 대한 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/ms731923.aspx)을 참조하세요.

## 행위자 인터페이스에서 사용되는 형식

[행위자 인터페이스](service-fabric-reliable-actors-introduction.md#actors)에 정의된 각 메서드에서 반환한 모든 메서드의 인수와 작업 결과 형식은 데이터 계약 직렬화 가능해야 합니다. 또한 [행위자 이벤트 인터페이스](service-fabric-reliable-actors-events.md#actor-events)에 정의된 메서드의 인수에도 적용됩니다. (행위자 이벤트 인터페이스 메서드는 항상 void를 반환합니다.) 예를 들어, `IVoiceMail` 인터페이스가 다음과 같이 메서드를 정의하는 경우

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>`는 이미 데이터 계약 직렬화 가능한 표준 .NET 형식입니다. `Voicemail` 형식은 데이터 계약 직렬화 가능해야 합니다.

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## 행위자 상태 클래스

행위자 상태는 데이터 계약 직렬화 가능해야 합니다. 예를 들어, 다음과 같은 행위자 클래스 정의가 있는 경우

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

상태 클래스는 각각 DataContract 및 DataMember 특성이 주석으로 첨부된 클래스 및 해당 멤버를 사용하여 정의됩니다.

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=August15_HO7-->