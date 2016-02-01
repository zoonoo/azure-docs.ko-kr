<properties
   pageTitle="행위자 형식 직렬화에 대한 Reliable Actors 참고 사항 | Microsoft Azure"
   description="서비스 패브릭 Reliable Actors 상태 및 인터페이스를 정의하는 데 사용될 수 있는 직렬화가 가능 클래스를 정의하기 위한 기본 요구 사항을 설명합니다."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# 서비스 패브릭 신뢰할 수 있는 행위자 형식 직렬화에 대한 참고 사항

행위자의 인터페이스 및 상태를 정의하는 경우 몇 가지 중요한 측면을 기억해야 합니다. 형식은 데이터 계약 직렬화가 가능해야 합니다. 데이터 계약에 대한 자세한 내용은 [MSDN에서 찾을 수](https://msdn.microsoft.com/library/ms731923.aspx) 있습니다.

## 행위자 인터페이스 형식

[행위자 인터페이스](service-fabric-reliable-actors-introduction.md#actors)에 정의된 각 메서드에서 작업의 결과 형식 뿐만 아니라 모든 메서드의 인수는 데이터 계약 직렬화가 가능해야 합니다. 또한 [행위자 이벤트 인터페이스](service-fabric-reliable-actors-events.md#actor-events)에 정의된 메서드의 인수에도 적용됩니다. (행위자 이벤트 인터페이스 메서드는 항상 void를 반환합니다.) 예를 들어, `IVoiceMail` 인터페이스가 다음과 같이 메서드를 정의하는 경우

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>`는 이미 데이터 계약 직렬화가 가능한 표준 .NET 형식입니다. 또한 `Voicemail` 형식은 데이터 계약 직렬화가 가능해야 합니다.

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

행위자의 상태는 데이터 계약 직렬화가 가능해야 합니다. 예를 들어 행위자 클래스 정의는 다음과 같을 수 있습니다.

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

상태 클래스는 각각 **DataContract** 및 **DataMember** 특성이 주석으로 첨부된 클래스 및 해당 멤버를 사용하여 정의됩니다.

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

<!---HONumber=AcomDC_0121_2016-->