<properties
   pageTitle="Azure 서비스 패브릭 행위자 개요"
   description="Azure 서비스 패브릭 행위자 프로그래밍 모델 소개"
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
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Azure 서비스 패브릭 행위자 소개
Azure 서비스 패브릭 행위자는 [서비스 패브릭](service-fabric-technical-overview.md)을 위한 행위자 프로그래밍 모델입니다. 서비스 패브릭은 클라우드와 온-프레미스에서 개발하기 쉽고 관리하기 쉬우며 신뢰성이 뛰어나고 확장 가능한 응용 프로그램을 빌드하기 위한 플랫폼입니다.

패브릭 행위자는 비동기 단일 스레드 행위자 모델을 제공합니다. 행위자는 상태 및 계산의 단위를 나타냅니다. 행위자는 높은 확장성을 위해 클러스터 전체에 걸쳐 분산됩니다. 행위자를 호스트하는 프로세스에 오류가 발생하면 다른 프로세스에 행위자가 다시 생성됩니다. 마찬가지로, 행위자의 호스트 프로세스가 실행되는 노드에서 오류가 발생하면 다른 노드의 호스트 프로세스에서 행위자가 다시 생성됩니다. 행위자 런타임에서는 기본 서비스 패브릭 플랫폼이 제공하는 분산 저장소를 활용하여 행위자에 대해 높은 가용성과 일관성 있는 상태 관리를 제공합니다. 따라서 행위자 기반의 배포된 클라우드 응용 프로그램을 굉장히 쉽게 개발하고 유지 관리할 수 있습니다.

## 행위자
행위자는 상태 및 동작을 모두 캡슐화하는 격리된 단일 스레드 구성 요소입니다. .NET 개체와 유사하므로 개발자에게 자연스러운 프로그래밍 모델을 제공합니다. .NET 개체가 .NET 형식의 인스턴스인 것과 유사하게 모든 행위자는 행위자 형식의 인스턴스입니다. 예를 들어, 계산기의 기능을 구현하는 행위자 형식이 있을 수 있으며 클러스터 전체에 걸쳐 다양한 노드에 배포되는 해당 형식의 여러 행위자가 있을 수 있습니다. 이러한 각 행위자는 행위자 ID에 의해 고유하게 식별됩니다.

행위자는 요청-응답 패턴으로 비동기 메시지를 전달하여 다른 행위자를 비롯한 시스템의 나머지 부분과 상호 작용합니다. 이러한 상호 작용은 인터페이스에서 비동기 메서드로 정의됩니다. 예를 들어, 계산기의 기능을 구현하는 행위자 형식의 인터페이스는 다음과 같이 정의할 수 있습니다.

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

행위자 형식은 다음과 같이 위의 인터페이스를 구현할 수 있습니다.

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

각 인터페이스 메서드의 경우 반환되는 인수 및 작업 결과 형식은 [데이터 계약 직렬화 가능](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)해야 합니다.

이러한 메서드의 구현에는 해당 행위자 런타임이 행위자 메서드에 대해 턴 기반 동시성을 제공하기 때문에 잠금 또는 기타 동시성 문제에 대한 처리는 포함되지 않습니다. 자세한 내용은 [동시성](#concurrency) 섹션을 참조하세요.

> [AZURE.TIP]패브릭 행위자 런타임에서는 일부 [행위자 메서드와 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

행위자 인터페이스 메서드와 관련된 다음과 같은 규칙에 주의해야 합니다. 행위자 인터페이스 메서드는 오버로드될 수 없습니다. 행위자 인터페이스 메서드는 출력, ref 또는 선택적인 매개 변수를 포함할 수 없습니다.

## 행위자 통신
행위자 클라이언트 API는 행위자 인스턴스 및 행위자 클라이언트 간의 통신을 제공합니다. 행위자와 통신하기 위해 클라이언트는 행위자 인터페이스를 구현하는 행위자 프록시 개체를 만듭니다. 클라이언트는 프록시 개체에서 메서드를 호출하여 행위자와 상호 작용합니다. 행위자 프록시는 행위자-행위자 통신뿐만 아니라 클라이언트-행위자 통신에도 사용할 수 있습니다. 계산기의 예를 계속 보면, 계산기 행위자의 클라이언트 코드를 아래와 같이 작성할 수 있습니다.

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

위의 예제에 나온 대로 행위자 프록시 개체를 만드는 데는 두 가지 정보(행위자 ID 및 응용 프로그램 이름)가 사용되었습니다. 행위자 ID는 행위자를 고유하게 식별하는 식별자입니다. 응용 프로그램 이름은 행위자를 배포할 때 사용되는 [서비스 패브릭 응용 프로그램](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors)의 이름입니다.

행위자는 가상 행위자로, 항상 존재함을 의미합니다. 명시적으로 행위자를 만들거나 소멸시킬 필요는 없습니다. 행위자 런타임은 해당 행위자에 대한 요청을 처음으로 받은 행위자를 자동으로 활성화합니다. 행위자를 일정 시간 동안 사용하지 않으면 행위자 런타임이 해당 행위자에 대해 가비지 수집을 수행하고 나중에 필요한 경우 활성화합니다. 자세한 내용은 [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)에서 해당 섹션을 참조하세요.

또한 행위자 클라이언트 API는 위치 투명성 및 장애 조치 기능을 제공합니다. 클라이언트 측의 `ActorProxy` 클래스는 필요한 확인을 수행하고 지정된 ID를 가진 행위자가 호스트된 행위자 서비스 [파티션](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)을 찾아서 통신 채널을 엽니다. `ActorProxy`에서는 통신 오류 및 장애 조치 시 통신을 다시 시도합니다. 따라서 행위자 구현에서 동일한 클라이언트로부터 중복 메시지를 가져올 수도 있습니다.

## 동시성
행위자 런타임은 행위자 메서드에 대해 간단한 턴 기반 동시성을 제공합니다. 따라서 행위자 코드 내에는 항상 둘 이상의 스레드가 활성화될 수 없습니다.

하나의 턴은 다른 행위자 또는 클라이언트의 요청에 대한 응답 시 행위자 메서드의 완전한 실행 또는 [타이머/미리 알림](service-fabric-reliable-actors-timers-reminders.md) 콜백의 완전한 실행으로 이루어집니다. 메서드와 콜백이 비동기인 경우에도 행위자 런타임은 이러한 메서드와 콜백을 인터리빙하지 않습니다. 하나의 턴을 완전히 완료해야 새로운 턴이 허용됩니다. 즉, 메서드 또는 콜백에 대한 새로운 호출을 허용하기 전에 현재 실행 중인 행위자 메서드 또는 타이머/미리 알림 콜백을 완전히 완료해야 합니다. 메서드 또는 콜백에서 실행이 반환된 경우 및 메서드 또는 콜백에서 반환된 작업이 완료된 경우, 메서드 또는 콜백이 완료된 것으로 간주합니다. 서로 다른 메서드, 타이머 및 콜백 전체에 걸쳐 턴 기반 동시성이 준수된다는 것을 강조할 가치가 있습니다.

행위자 런타임은 턴 시작 시 행위자별 잠금을 획득하고 턴 종료 시 해당 잠금을 해제하여 턴 기반 동시성을 적용합니다. 따라서 행위자들 전체가 아닌 각각의 행위자 단위로 턴 기반 동시성이 적용됩니다. 행위자 메서드와 타이머/미리 알림 콜백은 여러 행위자를 대신하여 동시에 실행할 수 있습니다.

다음 예제에서는 위의 개념을 설명합니다. 두 비동기 메서드(즉, *Method1* 및 *Method2*), 타이머 및 미리 알림을 구현하는 행위자 형식을 고려합니다. 아래 다이어그램에서는 이 행위자 형식에 속하는 행위자(*ActorId1* 및 *ActorId2*)를 대신하여 이러한 메서드와 콜백이 실행되는 타임라인의 예를 보여줍니다.

![][1]

위의 다이어그램에 따른 규칙은 다음과 같습니다.

- 각 세로줄은 특정 행위자를 대신한 메서드 또는 콜백의 논리적 실행 흐름을 보여줍니다.
- 각 세로줄에 표시된 이벤트는 이전 이벤트 아래에 최신 이벤트가 발생하는 시간적 순서에 따라 발생합니다.
- 서로 다른 색은 서로 다른 행위자에 해당하는 타임라인에 사용됩니다.
- 강조 표시는 행위자별 잠금이 메서드 또는 콜백을 대신하여 유지되는 기간을 나타내는 데 사용됩니다.

위의 다이어그램에 대해 다음과 같은 사항에 주의해야 합니다.

- *Method1*이 클라이언트 요청 *xyz789*에 대한 응답으로 *ActorId2*를 대신하여 실행되는 경우, *ActorId2*에서 *Method1*을 실행해야 하는 다른 클라이언트 요청 *abc123*이 도착합니다. 그러나 나중의 *Method1* 실행은 이전 실행이 완료될 때까지 시작되지 않습니다. 비슷하게, *ActorId2*에서 등록한 미리 알림은 클라이언트 요청 *xyz789*에 대한 응답으로 *Method1*이 실행되는 동안 실행됩니다. 미리 알림 콜백은 두 *Method1*의 실행이 완료된 후에만 실행됩니다. 모두가 *ActorId2*에 적용되는 턴 기반 동시성으로 인한 것입니다.
- 비슷하게, 턴 기반 동시성은 *Method1*, *Method2* 및 순차적으로 발생하는 *ActorId1* 대신 타이머 콜백을 실행하여 증명된 것처럼 *ActorId1*에 대해서도 적용됩니다.
- *ActorId1*을 대신한 *Method1*의 실행은 *ActorId2*를 대신한 실행과 겹칩니다. 이는 턴 기반 동시성이 전체 행위자들이 아닌 하나의 행위자 내에만 적용되기 때문입니다.
- 일부 메서드/콜백 실행에서 메서드/콜백에 의해 반환된 `Task`의 경우, 해당 메서드가 반환된 후 완료됩니다. 다른 몇몇에서는 `Task`의 경우, 메서드/콜백이 반환되는 시점에 이미 완료된 상태입니다. 두 경우 모두, 행위자별 잠금은 이들 둘이 발생한 후 즉, 메서드/콜백이 반환되고 `Task`가 완료된 후에만 해제됩니다.

기본적으로 행위자 런타임은 다시 표시를 허용합니다. 따라서 행위자 A의 행위자 메서드가 행위자 B의 메서드를 호출하고 행위자 B에서는 행위자 A의 다른 메서드를 차례로 호출하는 경우, 동일한 논리 호출 체인 컨텍스트의 일부로 해당 메서드를 실행할 수 있습니다. 모든 타이머 및 미리 알림 호출은 새로운 논리 호출 컨텍스트에서 시작됩니다. 자세한 내용은 [다시 표시](service-fabric-reliable-actors-reentrancy.md) 섹션을 참조하세요.

행위자 런타임은 이러한 메서드의 호출을 제어하는 상황에서 이러한 동시성을 보증합니다. 예를 들어, 클라이언트 요청 수신에 대한 응답으로 수행되는 메서드 호출 및 타이머와 미리 알림 콜백에 대해 이러한 보증을 제공합니다. 하지만 행위자 코드가 행위자 런타임에서 제공하는 메커니즘 외부에서 이러한 메서드를 호출하는 경우 런타임은 어떠한 동시성도 보장할 수 없습니다. 예를 들어, 행위자 메서드에서 반환된 작업과 연결되지 않은 일부 작업의 컨텍스트에서 메서드를 호출하거나 행위자가 자체적으로 만드는 스레드에서 호출하는 경우, 런타임은 동시성을 보장할 수 없습니다. 따라서 백그라운드 작업을 수행하려면 행위자가 턴 기반 동시성을 따르는 [행위자 타이머 또는 행위자 미리 알림](service-fabric-reliable-actors-timers-reminders.md)을 사용해야 합니다.

> [AZURE.TIP]패브릭 행위자 런타임에서는 일부 [동시성과 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

## 행위자 상태 관리
패브릭 행위자를 사용하면 상태 비저장 또는 상태 저장 행위자를 만들 수 있습니다.

### 상태 비저장 행위자
상태 비저장 행위자는 이름에서 알 수 있듯이 행위자 런타임에 의해 관리되는 상태를 유지하지 않습니다. 상태 비저장 행위자는 행위자 기본 클래스에서 파생됩니다. 멤버 변수를 가질 수 있으며 다른 .NET 형식과 마찬가지로 해당 행위자의 수명이 유지되는 동안 보존됩니다. 그러나 해당 행위자 인스턴스가 일정 시간 동안 유휴 상태가 지속된 후 가비지 수집되면 해당 멤버 변수에 저장된 상태를 잃을 수 있습니다. 비슷하게, 상태는 업그레이드, 리소스 균형 조정 작업 또는 행위자 프로세스나 행위자 프로세스를 호스트하는 노드의 오류 등과 같은 상황에서 발생할 수 있는 장애 조치로 인해 손실될 수 있습니다.

다음은 상태 비저장 행위자의 예입니다.

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### 상태 저장 행위자
상태 저장 행위자는 행위자 가비지 수집 및 장애 조치 전체에서 유지되어야 하는 상태를 갖습니다. 상태 저장 행위자는 `Actor<TState>` 기본 클래스에서 파생되며, `TState`는 가비지 수집 및 장애 조치 전체에서 유지되어야 하는 상태의 유형입니다. 상태는 기본 클래스 `Actor<TState>`의 `State` 속성을 통해 행위자 메서드에서 액세스할 수 있습니다. 다음은 상태에 액세스하는 상태 저장 행위자의 예입니다.

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

행위자 상태의 형식은 [데이터 계약 직렬화 가능](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)이어야 합니다.

> [참고] 인터페이스 및 행위자 상태 형식 정의 방법에 대한 자세한 모드 정보는 [Serialization에 대한 신뢰할 수 있는 행위자 참고 사항](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) 문서를 참조하세요.

#### 행위자 상태 제공자
상태 저장 및 검색 기능은 행위자 상태 제공자에 의해 제공됩니다. 상태 제공자는 행위자별로 구성되거나 상태 제공자별 특성에 따라 어셈블리 내의 모든 행위자에 대해 구성할 수 있습니다. 행위자 런타임에 포함된 일부 기본 행위자 상태 제공자가 있습니다. 상태의 내구성 및 안정성은 상태 제공자에서 제공하는 보증에 의해 결정됩니다. 행위자가 활성화되면 해당 상태가 메모리에 로드됩니다. 행위자 메서드가 완료되면 수정된 상태는 행위자 런타임에서 상태 제공자의 메서드를 호출하여 자동으로 저장됩니다. 상태 저장 중에 오류가 발생하는 경우 행위자 런타임이 해당 행위자 인스턴스를 재활용합니다. 새 행위자 인스턴스가 생성되어 상태 제공자의 마지막 일관된 상태와 함께 로드됩니다.

기본적으로 상태 저장 행위자는 키 값 저장소 행위자 상태 제공자를 사용합니다. 이 상태 제공자는 서비스 패브릭 플랫폼에서 제공하는 분산된 키-값 저장소에 빌드되어 있습니다. 자세한 내용은 [상태 제공자 선택](service-fabric-reliable-actors-platform.md#actor-state-provider-choices)에 대한 항목을 참조하세요.

> [AZURE.TIP]패브릭 행위자 런타임에서는 일부 [행위자 상태 관리와 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

#### 읽기 전용 메서드
기본적으로 행위자 런타임은 행위자 메서드 호출, 타이머 콜백 및 미리 알림 콜백이 완료되면 행위자 상태를 저장합니다. 상태 저장이 완료될 때까지 다른 행위자 호출은 허용되지 않습니다. 상태 제공자에 따라서는 상태를 저장하는 데 시간이 걸릴 수 있으며 이 시간 동안 해당 행위자에서 다른 행위자 호출이 허용되지 않습니다.

상태를 수정하지 않는 행위자 메서드가 있을 수 있으며, 이 경우 상태를 저장하는 데 소요된 추가적인 시간은 시스템의 전반적인 처리량에 영향을 줄 수 있습니다. 이 문제를 피하기 위해 상태를 수정하지 않는 메서드 및 타이머 콜백을 읽기 전용으로 표시할 수 있습니다.

아래 예제는 `Readonly` 특성을 사용하여 행위자 메서드를 읽기 전용으로 표시하는 방법을 보여줍니다.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

비슷한 방법으로 `Readonly` 특성을 사용하여 타이머 콜백을 표시할 수 있습니다. 미리 알림의 경우, 읽기 전용 플래그가 미리 알림을 등록하기 위해 호출되는 `RegisterReminder` 메서드에 인수로 전달됩니다.

## 다음 단계
### 개념
[행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)

[행위자 타이머 및 미리 알림](service-fabric-reliable-actors-timers-reminders.md)

[행위자 이벤트](service-fabric-reliable-actors-events.md)

[행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)

[패브릭 행위자가 서비스 패브릭 플랫폼을 사용하는 방법](service-fabric-reliable-actors-platform.md)

[KVSActorStateProvider 행위자 구성](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->