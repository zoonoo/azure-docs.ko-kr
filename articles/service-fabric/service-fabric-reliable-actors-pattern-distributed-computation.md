<properties
   pageTitle="분산 계산 패턴 | Microsoft Azure"
   description="서비스 패브릭의 신뢰할 수 있는 행위자는 병렬 비동기 메시징, 관리하기 쉬운 분산 상태 및 병렬 계산에 적합합니다."
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
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Reliable Actors 설계 패턴: 분산 계산
터무니없이 짧은 시간 안에 서비스 패브릭 Reliable Actors에서 재무 계산을 수행하는 실제 고객을 감시하려면 이 기능의 도움을 받아야 합니다. 위험 계산에 대한 Monte Carlo 시뮬레이션이었습니다.

도메인별 정보가 없으면 더 일반적인 방법(예: MapReduce 또는 메시지 전달 인터페이스) 대신 이 종류의 워크로드를 처리하는 서비스 패브릭을 사용하는 혜택은 바로 알 수 없을 수 있습니다.

하지만 서비스 패브릭은 다음 다이어그램에 나오는 것처럼 병렬 비동기 메시징, 관리하기 쉬운 분산 상태 및 병렬 계산에 적합합니다.

![서비스 패브릭 병렬 비동기 메시징, 관리하기 쉬운 분산 상태 및 병렬 계산][1]

다음 예제에서는 단순히 Monte Carlo 시뮬레이션을 사용하여 Pi 계산을 수행합니다. 다음과 같은 행위자를 사용합니다.

* 풀링된 작업 행위자를 사용하여 Pi 계산을 담당하는 프로세서

* Monte Carlo 시뮬레이션 및 결과를 집계로 전송하는 작업을 담당하는 풀링된 작업

* 결과를 집계하고 종료자로 보내는 작업을 담당하는 집계

* 최종 결과를 계산하고 화면에 인쇄하는 작업을 담당하는 종료자

## 분산 계산 코드 샘플--Monte Carlo 시뮬레이션

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : StatelessActor, IProcessor
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

public class PooledTask : StatelessActor, IPooledTask
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

서비스 패브릭에서 결과를 집계하는 일반적인 방법은 타이머를 사용하는 것입니다. 상태 비저장 행위자를 사용하는 주요 이유는 두 가지입니다. 런타임에서 동적으로 필요한 집계 수를 결정하므로 필요한 규모를 제공하고 해당 행위자를 “로컬로” 인스턴스화합니다. 즉, 호출 중인 행위자와 동일한 사일로에서 발생하므로 네트워크 홉 수가 감소됩니다.

집계 및 종료자는 다음과 같습니다.

## 분산 계산 코드 샘플--집계

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

public class Aggregator : StatefulActor<AggregatorState>, IAggregator
{
    protected override Task OnActivateAsync()
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

public class Finaliser : StatefulActor<FinalizerState>, IFinaliser
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

        return Task.FromResult(true);
    }
}
```

이 시점에서 집계를 통해 Leaderboard 예제에서 규모와 성능을 어떻게 강화시킬 수 있는지 명확히 해야 합니다.

서비스 패브릭이 빅 데이터 프레임워크 또는 고성능 컴퓨팅의 다른 분산 계산을 임시로 대체하는 방법이라는 말은 아닙니다. 다른 방법 보다 몇 가지를 잘 처리하도록 작성되었습니다. 하지만 서비스 패브릭에서 제공하는 단순성에서 이점을 얻으면서 동시에 워크플로 및 분산 병렬 계산을 모델링할 수 있습니다.

## 다음 단계
[패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)

[패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 사물 인터넷](service-fabric-reliable-actors-pattern-internet-of-things.md)

[일부 안티패턴](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 신뢰할 수 있는 행위자 소개](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=AcomDC_0121_2016-->