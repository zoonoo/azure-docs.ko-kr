<properties
   pageTitle="리소스 관리 디자인 패턴 | Microsoft Azure"
   description="서비스 패브릭의 신뢰할 수 있는 행위자를 사용하여 수직 확장이 필요하지만 제한된 리소스를 사용해야 하는 응용 프로그램을 모델링하는 방법에 대한 설계 패턴"
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

# Reliable Actors 설계 패턴: 리소스 관리

즉시 크기를 조정할 수 없는 온-프레미스 또는 클라우드에서 제한된 리소스를 가진 개발자(또는 엔터프라이즈)가 이 패턴 및 관련 시나리오를 쉽게 알 수 있습니다. 또한 대규모 응용 프로그램 및 데이터를 클라우드로 보내려는 개발자에게 익숙합니다.

엔터프라이즈 환경에서는 데이터베이스 등과 같이 제한된 리소스가 확장된 하드웨어에서 실행됩니다. 엔터프라이즈 환경에서 오래 작업한 사람은 이것이 온-프레미스 환경에서 일반적인 상황이라는 것을 압니다. 클라우드 규모에서도 개발자는 클라우드 서비스가 주소/포트 튜플 간 연결의 64000TCP 제한을 초과하려고 할 때 이 상황이 발생한다는 것을 압니다. 또한 동시 연결 수를 제한하는 클라우드 기반 데이터베이스에 연결을 시도하면 발생합니다.

이전에는 이런 상황이 일반적으로 메시지 기반 미들웨어를 통해 제한하거나 사용자가 작성한 풀링 및 외관 메커니즘을 사용하여 해결되었습니다. 이 문제는 올바르게 해결되기 어려운데, 특히 중간 계층의 규모를 조정해야 하지만 올바른 연결 수도 유지해야 할 때는 더욱 그렇습니다. 취약하고 복잡한 솔루션입니다.

스마트 캐시 패턴과 마찬가지로 이 패턴은 제한된 리소스로 작업 중인 시스템이 이미 있는 고객과 여러 다양한 시나리오를 확장합니다. 해당 시스템은 단지 서비스 뿐만 아니라 메모리 내의 해당 상태와 더불어 안정적인 저장소에 유지된 상태까지 규모를 확장해야 합니다.

아래 다이어그램에는 이 시나리오가 나와 있습니다.

![상태 비저장 행위자, 분할 및 제한된 리소스][1]

## 행위자를 사용한 모델 캐시 시나리오

리소스 또는 리소스 그룹(예: 연결)에 프록시의 역할을 하는 한 행위자 또는 여러 행위자를 사용하여 리소스에 대한 액세스를 모델링할 수 있습니다. 그런 다음, 직접 개별 행위자를 통해 리소스를 관리하거나 리소스 행위자를 관리하는 조정 행위자를 사용합니다.

이 개념을 더 구체적으로 설정하려면 성능 및 확장성을 이유로 분할된 저장소 계층에 대해 작업해야 하는 일반적인 필요성을 제기합니다. 첫 번째 옵션은 매우 기본적입니다. 행위자를 매핑하고 다운스트림 리소스로 처리하는 정적 함수를 사용할 수 있습니다. 이러한 함수는 해당 입력을 가진 연결 문자열 등을 반환할 수 있습니다. 해당 함수를 구현하는 방법은 사용자에게 달려 있습니다. 하지만 이 방법은 리소스를 다시 분할하거나 행위자를 리소스에 다시 매핑하기 굉장히 어렵게 만드는 정적 선호도 등의 단점도 있습니다.

다음은 간단한 예제입니다. **userId**를 사용하여 데이터베이스 이름을 확인하는 모듈 산술 작업을 수행하고 **지역**을 사용하여 데이터베이스 서버를 식별합니다.

### 리소스 관리 코드 샘플: 정적 확인

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

이 옵션은 간단하지만 유연하지 않습니다. 이제 좀더 유용한 고급 접근 방식을 살펴보겠습니다.

우선, 실제 리소스와 행위자 사이의 선호도를 모델링합니다. **확인자**라는 행위자를 통해 수행합니다. 사용자, 논리 파티션 및 실제 리소스 간의 매핑을 이해합니다. 확인자는 지속형 저장소에 데이터를 유지 관리하지만 쉽게 조회될 수 있도록 캐시됩니다. [스마트 캐시 패턴의 환율 샘플](service-fabric-reliable-actors-pattern-smart-cache.md)에서 설명한 대로 확인자는 타이머를 사용하여 최신 정보를 사전에 인출할 수 있습니다. 사용자 행위자가 사용해야 하는 리소스를 확인하면 해당 리소스를 **\_resolution**이라는 로컬 변수에 캐시하고 수명 동안 사용합니다.

작업에서 제공하는 유연성 때문에 단순 해시 또는 범위 해시에 대해 조회 기반 처리(아래 그림)를 선택했습니다. 이를 통해 크기를 조절하고 한 리소스에서 다른 리소스로 사용자를 이동할 수 있습니다.

![조회 확인자 솔루션][2]

위의 그림에서 행위자 B23이 먼저 해당 리소스(resolution) **DB1**을 확인하고 캐시합니다. 이제 후속 작업에서는 캐시된 resolution을 사용하여 제한된 리소스에 액세스할 수 있습니다. 행위자가 단일 스레드 실행을 지원하므로 개발자는 더 이상 리소스에 대한 동시 액세스에 대해 걱정할 필요가 없습니다. 사용자 및 확인자 행위자를 보려면 다음 코드 샘플을 참조하세요.

### 리소스 관리 코드 샘플: 확인자

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : StatefulActor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

#### 리소스 관리: 확인자 예제

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : StatefulActor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## 한정된 기능이 있는 리소스에 액세스

이제 다른 예로, 제한된 처리량 기능을 가진 데이터베이스, 저장소 계정 및 파일 시스템 등의 중요 리소스에 대한 단독 액세스를 살펴보겠습니다. 이 시나리오에서는 EventProcessor라는 행위자를 사용하여 이벤트를 처리하려고 합니다. 이 행위자는 이 경우에 간단히 .csv 파일에 이벤트를 처리하고 유지하는 일을 담당합니다. 위에서 설명한 분할 방법에 따라 리소스를 확장하지만 동시성 문제도 여전히 해결해야 합니다. 여러 행위자에서 단일 파일에 작성하는 작업은 동시성 문제를 발생시키기 때문에 파일 기반 예제를 선택하여 이 지점을 설명합니다. 문제를 해결하기 위해 제한된 리소스의 단독 소유권을 가진 EventWriter라는 다른 행위자가 사용되었습니다. 이 시나리오는 아래에 나와 있습니다.

![EventWriter 및 EventProcessor를 사용하여 이벤트 작성 및 처리][3]

EventProcessor 행위자를 상태 비저장 작업자로 표시하면 런타임이 필요에 따라 클러스터 전체에서 크기를 조정할 수 있습니다. 이러한 행위자에 대해 위의 그림에 나온 식별자를 사용하지 않았습니다. 상태 비저장 행위자는 런타임에 의해 유지 관리되는 작업자의 풀입니다.

아래 샘플 코드에서 EventProcessor 행위자는 두 가지 기능이 있습니다. 먼저 어떤 EventWriter(및 따라서 리소스)를 사용할지 결정한 다음 선택한 행위자를 호출하여 처리된 이벤트를 작성합니다. 간단히 하기 위해 EventWriter 행위자에 대한 식별자로 이벤트 형식을 선택했습니다. 즉, 이 이벤트 형식에 대해 하나의 EventWriter만 있게 되며 리소스에 대한 단일 스레드 및 단독 액세스를 제공합니다.

### 리소스 관리 코드 샘플: EventProcessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

이제 EventWriter 행위자를 살펴보겠습니다. 제한된 리소스(이 경우는 파일 및 파일에 대해 작성된 이벤트)에 대한 단독 액세스를 제어하지만 많은 것을 하지 않습니다.

### 리소스 관리 코드 샘플: EventWriter

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

리소스에 대한 책임이 있는 단일 행위자를 사용하여 버퍼링과 같은 기능을 추가할 수 있습니다. 타이머를 사용하거나 버퍼가 꽉 찼을 때 들어오는 이벤트를 버퍼링하고 정기적으로 해당 이벤트를 작성할 수 있습니다. 다음 코드 샘플은 간단한 타이머 기반 예제를 제공합니다.

### 리소스 관리 코드 샘플: 버퍼가 있는 EventWriter

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

위의 코드가 잘 작동하지만 해당 이벤트가 기본 저장소에 기록되었는지 여부를 클라이언트는 모릅니다. 버퍼링을 허용하고 해당 요청에 대해 어떤 일이 발생하는지에 대한 정보를 클라이언트에 제공하려면 다음 방법을 사용하여 .csv 파일에 해당 이벤트가 기록될 때까지 클라이언트가 기다리도록 할 수 있습니다.

### 리소스 관리 코드 샘플: 비동기 배치

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

이 클래스를 사용하여 (클라이언트를 차단하는)불완전한 작업 목록을 만들고 유지 관리합니다. 버퍼링된 이벤트를 저장소에 작성한 후에 한 번에 완료합니다.

EventWriter 클래스에서는 세 가지 작업을 수행해야 합니다. 행위자 클래스를 재진입으로 표시하고 **SubmitNext()**의 결과를 반환하고 타이머를 플러시합니다. 아래 수정된 코드를 참조하세요.

### 리소스 관리 코드 샘플: 비동기 배치로 버퍼링

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

이 방법은 엔터프라이즈 환경의 역량을 착각하기 쉽습니다. 이 아키텍처를 사용하여 다음을 가져옵니다.

* 위치 독립적인 리소스 주소 지정.
* 리소스를 대신하여 동작하는 행위자 수의 변경에 따라 간단하게 조정 가능한 풀 크기.
* 클라이언트 측에서 조정된 풀 사용량(그림 참조) 또는 서버 측 사용량.(그림에서 각각의 풀 앞에 있는 단일 행위자를 생각해 봅니다)
* 확장 가능한 풀 추가(새 리소스를 나타내는 행위자 추가).
* 앞서 말한 대로 타이머를 사용하여 주문형 또는 사전 캐시의 백 엔드 리소스에서 결과를 캐시할 수 있는 행위자. 이렇게 하면 백 엔드 리소스에 도달할 필요가 줄어듭니다.
* 효율적인 비동기 발송.
* 미들웨어 전문가가 아닌 모든 개발자에게 친숙한 코딩 환경.

이 패턴은 개발자가 개발해야 하는 제한된 리소스를 가진 시나리오에서 매우 일반적입니다. 또한 개발자가 대규모 확장 시스템을 구축하는 경우에도 일반적입니다.


## 다음 단계

[패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)

[패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)

[패턴: 분산 계산](service-fabric-reliable-actors-pattern-distributed-computation.md)

[일부 안티패턴](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 신뢰할 수 있는 행위자 소개](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->