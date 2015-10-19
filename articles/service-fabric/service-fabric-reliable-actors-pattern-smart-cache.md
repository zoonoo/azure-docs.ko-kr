<properties
   pageTitle="신뢰할 수 있는 행위자 스마트 캐시 디자인 패턴"
   description="웹 기반 응용 프로그램에서 캐싱 인프라로 신뢰할 수 있는 행위자를 사용하는 방법에 대한 패턴 디자인"
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# 신뢰할 수 있는 행위자 디자인 패턴: 스마트 캐시
웹 계층, 캐싱 계층, 저장소 계층 및 경우에 따라 작업자 계층을 조합하는 방식은 오늘날의 응용 프로그램에서 거의 표준에 가깝습니다. 일반적으로 캐싱 계층은 성능에 중요하며, 사실상 여러 계층 자체로 구성될 수 있습니다 . 많은 캐시가 간단한 키-값 쌍이며 [Redis](http://redis.io)처럼 캐시로 사용되는 다른 시스템이 훨씬 다양한 의미 체계를 제공합니다. 여전히, 특별한 부분에서 캐싱 계층은 의미 체계에 제한이 있으며 무엇 보다도 아직 관리해야 하는 또 다른 계층입니다. 그 대신, 로컬 변수에 개체의 상태가 유지되고 이러한 개체를 지속형 저장소에 자동으로 스냅숏하거나 유지할 수 있다면 어떨까요? 또한 해당 문제와 관련하여, 목록, 정렬된 집합, 큐 및 기타 사용자 지정 형식 등 다양한 컬렉션은 멤버 변수와 메서드를 모델링하기만 하면 됩니다.

![][1]

## leaderboard 샘플
리더 보드를 예로 들어 보겠습니다. Leaderboard 개체는 쿼리할 수 있도록 정렬된 플레이어 목록과 점수를 유지 관리해야 합니다. 예를 들어, "상위 100대 플레이어"를 찾거나 리더 보드상에서 한 플레이어의 위치가 해당 위치를 기준으로 하여 위아래로 +- N 플레이어의 어느 위치인지 찾을 수 있습니다. 기존 도구를 사용한 일반적인 솔루션에서는 Leaderboard 개체(Score라는 이름의 새로운 <Player  Points> 튜플 삽입을 지원하는 컬렉션)에 대해 'GET'을 수행하고 정렬한 후 마지막으로 다시 캐시에 'PUT'을 수행해야 합니다. 일관성을 위해 Leaderboard 개체에 대해 LOCK(GETLOCK, PUTLOCK)을 수행하게 됩니다. 상태와 동작이 함께 있는 행위자 기반 솔루션을 만들도록 하겠습니다. 두 가지 옵션이 있습니다.

* 행위자의 일부로 Leaderboard 컬렉션 구현
* 멤버 변수를 유지할 수 있는 컬렉션에 대한 인터페이스로 행위자를 사용 우선 인터페이스에 대해 살펴보겠습니다.

## 스마트 캐시 코드 샘플 – Leaderboard 인터페이스

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

다음으로, 이 인터페이스를 구현하고 두 번째 옵션을 사용하고, 행위자에서 이 컬렉션의 동작을 캡슐화합니다.

## 스마트 캐시 코드 샘플 – Leaderboard 행위자

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

클래스의 상태 멤버는 행위자의 상태를 제공하며, 위의 샘플 코드에서는 데이터 읽기/쓰기에 대한 메서드도 제공합니다.

## 스마트 캐시 코드 샘플 – Leaderboard 컬렉션

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

하나의 클라이언트에만 서비스를 제공하는 단일 응용 프로그램의 단일 개체인 것처럼 데이터 전달이나 잠금 없이 분산 런타임에서 원격 개체를 조작하고 여러 클라이언트에 서비스를 제공합니다. 샘플 클라이언트는 다음과 같습니다.

## 스마트 캐시 코드 샘플 – Leaderboard 행위자 호출

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

출력은 다음과 같이 표시됩니다.

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## 아키텍처 크기 조정
위의 예제는 Leaderboard 인스턴스에서 병목 지점을 만들 수 있다고 생각할 수 있습니다. 예를 들어, 수백, 수천 명의 플레이어를 지원하려고 계획한다면 어떨까요? 이를 처리하는 한 가지 방법은 버퍼처럼 동작하는 상태 비저장 집계를 도입하는 것으로, 부분 점수(즉, 부분합)를 보관한 다음 주기적으로 Leaderboard 행위자에게 보내면 최종 Leaderboard를 유지 관리할 수 있습니다. 이 "집계" 기술에 대한 자세한 내용은 나중에 설명합니다. 또한 뮤텍스, 세마포 또는 동시 실행 프로그램을 올바르게 작동시키는 데 전통적으로 필요한 기타 동시성 구문을 고려할 필요가 없습니다. 아래는 행위자로 구현할 수 있는 풍부한 의미 체계를 보여주는 또 다른 캐시 예제입니다. 이번에는 우선 순위 큐(숫자가 낮을수록 우선 순위가 높음)의 논리를 행위자 구현의 일부로 구현합니다. IJobQueue의 인터페이스는 다음과 같습니다.

## 스마트 캐시 코드 샘플 – 작업 큐 인터페이스

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

또한 다음과 같은 작업 항목을 정의해야 합니다.

## 스마트 캐시 코드 샘플 – 작업

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

마지막으로 세부적으로 IJobQueue 인터페이스를 구현합니다. 여기서는 명확성을 위해 자세한 우선 순위 큐의 구현은 생략했습니다. 샘플 구현은 함께 제공되는 샘플에서 찾을 수 있습니다.

## 스마트 캐시 코드 샘플 – 작업 큐

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

출력은 다음과 같이 표시됩니다.

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## 유연성을 제공하는 행위자
위의 Leaderboard 및 JobQueue 샘플에서는 두 가지 기법이 사용되었습니다.

* Leaderboard 샘플에서는 Leaderboard 개체를 행위자의 개인 멤버 변수로 캡슐화하고 단순히 이 개체(상태 및 기능성 둘 모두)에 대한 인터페이스를 제공했습니다.

* 반면에 JobQueue 샘플에서는 다른 곳에서 정의한 다른 개체를 참조하는 대신 행위자를 우선 순위 큐 자체로 구현했습니다.

행위자는 개발자에게 행위자 또는 행위자 외부의 참조 개체 그래프의 일부로 다양한 개체 구조를 정의할 수 있는 유연성을 제공합니다. 캐시 조건에서 행위자는 write-behind 또는 write-through를 수행하거나 멤버 변수 세분성에 따라 서로 다른 기술을 사용할 수 있습니다. 즉, 유지할 내용과 유지할 시기를 전체 제어할 수 있습니다. 임시 상태 또는 저장된 상태에서 빌드할 수 있는 상태는 유지할 필요가 없습니다. 그러면 이러한 행위자를 캐시로 채우는 것은 어떨까요? 여러 가지 방법으로 이 작업을 수행할 수 있습니다. 행위자는 행위자의 인스턴스가 활성화 및 비활성화될 때 알려줄 수 있도록 OnActivateAsync() 및 OnDectivateAsync()라는 가상 메서드를 제공합니다. 행위자는 첫 번째 요청을 보낼 때 필요에 따라 활성화됩니다. OnActivateAsync()를 사용하면 read-through에서처럼 안정적인 외부 저장소에서 필요에 따라 상태를 채울 수 있습니다. 또는 타이머 즉, 최신 환율을 기준으로 변환 함수를 제공하는 Exchange Rate 행위자에 상태를 채울 수 있습니다. 이 행위자는 외부 서비스에서 해당 상태를 주기적(즉, 5초 마다)으로 채우고 변환 함수에서 해당 상태를 사용할 수 있습니다. 아래 예제를 참조하세요.

## 스마트 캐시 코드 샘플 – 환율 변환기

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

기본적으로 스마트 캐시는 다음을 제공합니다.

* 메모리에서 서비스 요청에 따라 높은 처리량/낮은 대기 시간 제공.
* 영구 저장소에 대한 경합 없이 항목에 대한 요청의 단일 인스턴스 라우팅 및 단일 스레드 Serialization.
* Enqueue(Job item)와 같은 의미 체계 작업.
* 간편한 write-through 또는 write-behind 구현.
* LRU(오래 전에 사용한 항목) 항목 자동 제거(리소스 관리).
* 자동 탄력성 및 안정성.


## 다음 단계
[패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)

[패턴: 분산 계산](service-fabric-reliable-actors-pattern-distributed-computation.md)

[일부 패턴 방지](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 행위자 소개](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=Oct15_HO2-->