<properties
   pageTitle="Azure 서비스 패브릭 행위자 분산 네트워크 및 그래프 디자인 패턴"
   description="서비스 패브릭 행위자를 사용하여 응용 프로그램을 분산 네트워크 및 그래프로 모델링하는 방법에 대한 패턴을 디자인합니다."
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
   ms.author="claudioc"/>

# 서비스 패브릭 행위자 디자인 패턴: 분산 네트워크 및 그래프
Azure 패브릭 서비스 행위자는 관계와 관련된 복잡한 솔루션을 모델링하고 이러한 관계를 개체로 모델링하는 데 가장 적합합니다.

![][1]

이 다이어그램에 나온 것처럼 사용자를 행위자 인스턴스(네트워크의 노드)로 간단하게 모델링할 수 있습니다. 예를 들어, Facebook 및 Twitter의 작동 방식과 비슷하게 "친구 피드"("팔로워" 문제라고도 함)를 사용하면 연결되어 있는 사람들의 상태 업데이트를 볼 수 있습니다. 행위자 모델은 구체화 문제에 접근하는 유연성을 제공합니다. 아래 그림과 같이 업데이트가 게시되는 순간 모든 친구의 친구 피드를 업데이트하여 이벤트 시간에 친구 피드를 채울 수 있습니다.

![][2]


## 스마트 캐시 코드 샘플 – 소셜 네트워크 친구 피드(이벤트 시간)

친구 피드를 채우는 샘플 코드:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

또는 쿼리 타이머에서 즉, 사용자가 친구 피드를 요청할 때 행위자를 모델링하여 친구 피드를 팬아웃 및 컴파일할 수 있습니다. 사용 가능한 또 다른 방법은 타이머에서 친구 피드를 구체화하는 것입니다(예: 5분 마다). 또는 모델을 최적화하고 로그인 빈도 또는 업데이트 게시 빈도와 같은 사용자 습관에 따라 타이머 기반 모델을 사용하여 처리하는 쿼리 시간과 이벤트 시간을 결합할 수도 있습니다. 소셜 네트워크에서 행위자를 모델링할 때는 수백만의 팔로워가 있는 "슈퍼 사용자"를 고려해야 합니다. 개발자는 요구 사항을 충족하도록 해당 사용자의 상태와 동작을 다르게 모델링해야 합니다. 비슷하게, 여러 사용자 행위자를 단일 활동 행위자(허브 및 스포크)에 연결하는 활동을 모델링하려는 경우에도 마찬가지입니다. 그룹 채팅 또는 게임 호스팅 시나리오를 예로 들 수 있습니다. 그룹 채팅 예를 살펴보겠습니다. 아래 예와 같이, 일련의 참가자가 한 참가자의 메시지를 그룹에 배포할 수 있는 그룹 채팅 행위자를 만듭니다.

## 스마트 캐시 코드 샘플 - GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

실제 할 일은 신뢰할 수 있는 행위자의 기능을 활용하여 모든 행위자가 ID를 기준으로 클러스터의 다른 모든 행위자를 처리하고 배치, 주소 지정, 캐싱, 메시징, Serialization 또는 라우팅에 대해 걱정할 필요 없이 통신할 수 있도록 하는 것입니다.

## 다음 단계
[패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)

[패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)

[패턴: 분산 계산](service-fabric-reliable-actors-pattern-distributed-computation.md)

[일부 패턴 방지](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 행위자 소개](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png
 

<!---HONumber=July15_HO4-->