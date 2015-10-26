<properties
   pageTitle="신뢰할 수 있는 행위자 분산 계산"
   description="서비스 패브릭 신뢰할 수 있는 행위자는 병렬 비동기 메시징, 관리하기 쉬운 분산 상태 및 병렬 계산에 적합합니다."
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
   ms.date="09/08/2015"
   ms.author="vturecek"/>

# 신뢰할 수 있는 행위자 디자인 패턴: 분산 계산
터무니없이 짧은 시간 안에 서비스 패브릭 신뢰할 수 있는 행위자에서 재무 계산을 수행하는 실제 고객을 감시하려면 어느 정도는 위험한 계산을 정확하게 하는 Monte Carlo 시뮬레이션의 도움을 받아야 합니다.

처음에는, 특히 도메인 관련 지식이 없는 사람들에게는, Azure 서비스 패브릭에서 제공하는 이런 종류의 작업 처리는, 말하자면 Map/Reduce 또는 MPI 등의 전통적인 방식과는 대조적인 이런 방식은 명확하지 않을 수 있습니다.

하지만 Azure 서비스 패브릭은 다음 다이어그램에 나오는 것처럼 병렬 비동기 메시징, 관리하기 쉬운 분산 상태 및 병렬 계산에 적합하다는 것이 판명되었습니다.

![][1]

다음 예제에서는 단순히 Monte Carlo 시뮬레이션을 사용하여 Pi 계산을 수행합니다. 다음과 같은 행위자가 있습니다.

* PoolTask 행위자를 사용하여 Pi 계산을 담당하는 프로세서.

* Monte Carlo 시뮬레이션을 담당하고 결과를 집계로 보내는 PoolTask.

* 결과를 집계하여 종료자로 보내는 집계.

* 최종 결과를 계산하고 화면에 인쇄하는 종료자.

## 분산 계산 코드 샘플 – Monte Carlo 시뮬레이션

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Azure 서비스 패브릭에서 결과를 집계하는 일반적인 방법은 타이머를 사용하는 것입니다. 상태 비저장 행위자를 사용하는 주요 이유는 두 가지입니다. 런타임에서 동적으로 필요한 집계 수를 결정하므로 필요에 따라 규모를 확장할 수 있습니다. 또한 이러한 행위자는 "로컬"로, 다시 말해 호출 중인 행위자와 동일한 사일로에서 인스턴스화되므로 네트워크 홉 수가 감소됩니다. 집계 및 종료자는 다음과 같습니다.

## 분산 계산 코드 샘플 - 집계

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

이 시점에서 집계를 통해 Leaderboard 예제에서 규모와 성능을 잠재적으로 어떻게 확장시킬 수 있는지 명확히 해야 합니다.

Azure 서비스 패브릭이 빅 데이터 프레임워크 또는 고성능 컴퓨팅의 다른 분산 계산을 임시로 대체하는 방법은 절대로 아닙니다. 다른 것들 보다 더 나은 처리를 위해 빌드된 것일 뿐입니다. 하지만 Azure 서비스 패브릭에서 제공하는 단순성 이점을 얻으면서 워크플로 및 분산 병렬 계산을 모델링할 수 있습니다.

## 다음 단계
[패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)

[패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)

[일부 패턴 방지](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 행위자 소개](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=Oct15_HO3-->