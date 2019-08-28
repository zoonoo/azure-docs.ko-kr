---
title: Durable Functions 미리 보기 기능-Azure Functions
description: Durable Functions 미리 보기 기능에 대해 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 1609931cd5fcab0977ff64f680fbb1f253f3caaf
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782182"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 미리 보기 (Azure Functions)

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. Durable Functions에 익숙하지 않은 경우 [개요 설명서](durable-functions-overview.md)를 참조 하세요.

Durable Functions 1.x는 Azure Functions의 GA (일반 공급) 기능 이지만 현재 공개 미리 보기로 제공 되는 몇 가지 하위 기능도 포함 하 고 있습니다. 이 문서에서는 새로 릴리스된 미리 보기 기능에 대해 설명 하 고 작동 방법 및 사용을 시작 하는 방법에 대해 자세히 설명 합니다.

> [!NOTE]
> 이러한 미리 보기 기능은 현재 몇 가지 주요 변경 내용이 포함 된 **미리 보기 품질 릴리스입니다** Durable Functions 2.0 릴리스에 포함 되어 있습니다. Azure Functions 영 속 확장 패키지 빌드는 **2.0.0-ax**형식의 버전을 포함 하는 nuget.org에서 찾을 수 있습니다. 이러한 빌드는 프로덕션 워크 로드를 위한 것이 아니며 후속 릴리스에는 추가 주요 변경 사항이 포함 될 수 있습니다.

## <a name="breaking-changes"></a>호환성이 손상되는 변경

Durable Functions 2.0에 몇 가지 주요 변경 사항이 도입 되었습니다. 기존 응용 프로그램은 코드를 변경 하지 않고 Durable Functions 2.0와 호환 될 것으로 예상 되지 않습니다. 이 섹션에서는 다음과 같은 몇 가지 변경 사항을 설명 합니다.

### <a name="hostjson-schema"></a>Host. json 스키마

다음 코드 조각은 호스트 json에 대 한 새 스키마를 보여 줍니다. 유의 해야 할 주요 사항은 새로운 하위 섹션입니다.

* `"storageProvider"`저장소 관련 구성 `"azureStorage"` 에 대 한 (및 하위 섹션)
* `"tracking"`추적 및 로깅 구성
* `"notifications"`event grid 알림 `"eventGrid"` 구성의 및 하위 섹션

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Durable Functions 2.0이 계속 안정화 되 면 호스트 json `durableTask` 섹션에 더 많은 변경 내용이 도입 됩니다. 이러한 변경 내용에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/641)를 참조 하세요.

### <a name="public-interface-changes"></a>공용 인터페이스 변경

Durable Functions에서 지 원하는 다양 한 "context" 개체에는 단위 테스트에 사용 하기 위한 추상 기본 클래스가 있습니다. Durable Functions 2.0의 일부로 이러한 추상 기본 클래스는 인터페이스로 대체 되었습니다. 구체적 형식을 직접 사용 하는 함수 코드는 영향을 받지 않습니다.

다음 표는 주요 변경 내용을 나타냅니다.

| 이전 형식 | 새 형식 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

추상 기본 클래스가 가상 메서드를 포함 하는 경우 이러한 가상 메서드는에 `DurableContextExtensions`정의 된 확장 메서드로 대체 됩니다.

## <a name="entity-functions"></a>Entity 함수

엔터티 함수는 *영 속 엔터티*라고 하는 작은 상태를 읽고 업데이트 하기 위한 작업을 정의 합니다. 오 케 스트레이 터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용 하는 함수입니다. Orchestrator 함수와 달리 entity 함수는 특정 코드 제약 조건을 포함 하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 표시 하지 않고 명시적으로 상태를 관리 합니다.

### <a name="net-programing-models"></a>.NET 프로그래밍 모델

영 속 엔터티를 제작 하기 위한 두 가지 선택적 프로그래밍 모델이 있습니다. 다음 코드는 표준 함수로 구현 된 간단한 *카운터* 엔터티의 예입니다. 이 함수는 `get` `add` `reset` 각각정수상태값에대해작동하는세개의작업,,및를정의`currentValue`합니다.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

이 모델은 간단한 엔터티 구현 또는 동적 작업 집합이 있는 구현에 가장 적합 합니다. 그러나 정적 이지만 더 복잡 한 구현을 포함 하는 엔터티에 대해 유용한 클래스 기반 프로그래밍 모델도 있습니다. 다음 예제는 .net 클래스 및 메서드를 사용 `Counter` 하는 엔터티의 동일한 구현입니다.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

클래스 기반 모델은 프로그래밍 모델 mvvm by [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)와 비슷합니다. 이 모델에서 엔터티 형식은 .NET 클래스로 정의 됩니다. 클래스의 각 메서드는 외부 클라이언트에서 호출할 수 있는 작업입니다. 그러나 Orleans와 달리 .NET 인터페이스는 선택 사항입니다. 이전 *카운터* 예제에서는 인터페이스를 사용 하지 않았지만 다른 함수 또는 HTTP API 호출을 통해 호출할 수 있습니다.

엔터티 *인스턴스* 는 고유 식별자 인 *엔터티 ID*를 통해 액세스 됩니다. 엔터티 ID는 단순히 엔터티 인스턴스를 고유 하 게 식별 하는 문자열 쌍입니다. ASE는 다음으로 구성됩니다.

* **엔터티 이름**: 엔터티 형식을 식별 하는 이름입니다 (예: "Counter").
* **엔터티 키**: 동일한 이름의 다른 모든 엔터티 (예: GUID)에서 엔터티를 고유 하 게 식별 하는 문자열입니다.

예를 들어 *카운터* 엔터티 함수는 온라인 게임에서 점수를 유지 하는 데 사용 될 수 있습니다. 게임의 각 인스턴스에는, 등의 고유한 엔터티 ID `@Counter@Game1` `@Counter@Game2`가 있습니다.

### <a name="comparison-with-virtual-actors"></a>가상 행위자와 비교

내구성 있는 엔터티의 디자인은 [행위자 모델](https://en.wikipedia.org/wiki/Actor_model)의 영향을 크게 받습니다. 행위자에 대해 잘 알고 있는 경우 내구성이 있는 엔터티의 개념은 잘 알고 있어야 합니다. 특히 지 속성 엔터티는 다음과 같은 다양 한 방식으로 [가상 행위자](https://research.microsoft.com/projects/orleans/) 와 비슷합니다.

* 영 속 엔터티는 *엔터티 ID*를 통해 주소를 지정할 수 있습니다.
* 영 속 엔터티 작업은 경합 상태를 방지 하기 위해 한 번에 하나씩 순차적으로 실행 됩니다.
* 지 속성 엔터티는 호출 되거나 신호를 받을 때 자동으로 생성 됩니다.
* 작업을 실행 하지 않을 때 지 속성 엔터티는 메모리에서 자동으로 언로드됩니다.

그러나 다음과 같은 몇 가지 중요 한 차이점이 있습니다.

* 지 속성 엔터티는 *대기 시간*보다 *내구성* 의 우선 순위를 지정 하므로 엄격한 대기 시간 요구 사항이 있는 응용 프로그램에는 적합 하지 않을 수 있습니다.
* 엔터티 간에 전송 된 메시지는 순서 대로 안정적으로 전달 됩니다.
* 지 속성 엔터티는 지속형 오케스트레이션과 함께 사용할 수 있으며 분산 잠금으로 사용할 수 있으며,이에 대해서는이 문서의 뒷부분에서 설명 합니다.
* 엔터티에 대 한 요청/응답 패턴은 오케스트레이션으로 제한 됩니다. 엔터티 간 통신의 경우 원래 행위자 모델과 같이 단방향 메시지 ("신호" 라고도 함)만 허용 됩니다. 이 동작은 분산 교착 상태를 방지 합니다.

### <a name="durable-entity-net-apis"></a>지 속성 Entity .NET Api

엔터티 지원에는 몇 가지 Api가 포함 됩니다. 엔터티에 대해 작업을 호출할 때 수행 되는 작업을 지정 하는 위와 같이 엔터티 함수를 정의 하는 새로운 API가 있습니다. 또한 클라이언트 및 오케스트레이션의 기존 Api는 엔터티와 상호 작용 하는 새로운 기능으로 업데이트 되었습니다.

#### <a name="implementing-entity-operations"></a>엔터티 작업 구현

엔터티에 대 한 작업 실행은 컨텍스트 개체 (`IDurableEntityContext` .net)에서 이러한 멤버를 호출할 수 있습니다.

* **OperationName**: 작업의 이름을 가져옵니다.
* **Getinput\<TInput >** : 작업에 대 한 입력을 가져옵니다.
* **Getstate\<tstate >** : 엔터티의 현재 상태를 가져옵니다.
* **SetState**: 엔터티의 상태를 업데이트 합니다.
* **SignalEntity**: 엔터티에 단방향 메시지를 보냅니다.
* **Self**: 엔터티의 ID를 가져옵니다.
* **Return**: 작업을 호출한 클라이언트 또는 오케스트레이션에 값을 반환 합니다.
* **Isnewlyconstructed**: 작업 `true` 전에 엔터티가 존재 하지 않는 경우를 반환 합니다.
* **DestructOnExit**: 작업을 완료 한 후 엔터티를 삭제 합니다.

작업은 오케스트레이션 보다 제한이 낮습니다.

* 작업은 동기 또는 비동기 Api를 사용 하 여 외부 i/o를 호출할 수 있습니다 (비동기 작업을 사용 하는 것이 좋습니다).
* 작업은 명확 하지 않을 수 있습니다. 예를 들어, `DateTime.UtcNow` `Guid.NewGuid()` 또는 `new Random()`를 호출 하는 것이 안전 합니다.

#### <a name="accessing-entities-from-clients"></a>클라이언트에서 엔터티 액세스

지 속성 엔터티는 `orchestrationClient` .net`IDurableOrchestrationClient` 의 바인딩을 통해 일반 함수에서 호출 될 수 있습니다. 지원 되는 메서드는 다음과 같습니다.

* **ReadEntityStateAsync\<T >** : 엔터티 상태를 읽습니다.
* **SignalEntityAsync**: 엔터티에 단방향 메시지를 보내고 큐에 대기 될 때까지 기다립니다.
* **SignalEntityAsync\<T >** :와 `SignalEntityAsync` 동일 하지만 형식의 `T`생성 된 프록시 개체를 사용 합니다.

이전 `SignalEntityAsync` 호출에서는 엔터티 작업 `string` 이름을으로 지정 하 고 작업 `object`의 페이로드를로 지정 해야 합니다. 다음 샘플 코드는이 패턴의 예입니다.

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

형식 안전 액세스에 대 한 프록시 개체를 생성할 수도 있습니다. 형식이 안전한 프록시를 생성 하려면 엔터티 형식이 인터페이스를 구현 해야 합니다. 예를 들어 앞에서 `Counter` 언급 한 엔터티가 다음과 같이 `ICounter` 정의 된 인터페이스를 구현 했다고 가정 합니다.

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

클라이언트 코드는를 사용 `SignalEntityAsync<T>` 하 고 인터페이스 `ICounter` 를 형식 매개 변수로 지정 하 여 형식이 안전한 프록시를 생성할 수 있습니다. 다음 코드 샘플에서는 형식이 안전한 프록시를 사용 하는 방법을 보여 줍니다.

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

이전 예제 `proxy` 에서 매개 변수는에 대 한 호출 `Add` 을의 해당 `ICounter`(형식화 되지 않은) 호출 `SignalEntityAsync`로 내부적으로 변환 하는의 동적으로 생성 된 인스턴스입니다.

의 `SignalEntityAsync<T>` 형식 매개 변수에는 다음과 같은 제한 사항이 있습니다.

* 형식 매개 변수는 인터페이스 여야 합니다.
* 메서드만 인터페이스에 정의할 수 있습니다. 속성은 지원 되지 않습니다.
* 각 메서드는 매개 변수를 하나 또는 전혀 정의 하지 않아야 합니다.
* `void`각 메서드는, `Task`또는 `Task<T>` 중 하나를 반환 `T` 해야 합니다. 여기서은 serializeable 형식입니다.
* 인터페이스는 인터페이스 어셈블리 내에서 정확히 한 형식으로 구현 해야 합니다.

대부분의 경우 이러한 요구 사항을 충족 하지 않는 인터페이스는 런타임 예외를 발생 시킵니다.

> [!NOTE]
> 의 `ReadEntityStateAsync` `SignalEntityAsync` 및 메서드는일관성보다성능에우선순위를둡니다.`IDurableOrchestrationClient` `ReadEntityStateAsync`는 오래 된 값을 반환할 수 `SignalEntityAsync` 있으며 작업이 완료 되기 전에를 반환할 수 있습니다.

#### <a name="accessing-entities-from-orchestrations"></a>오케스트레이션에 서 엔터티 액세스

오케스트레이션은 개체를 `IDurableOrchestrationContext` 사용 하 여 엔터티에 액세스할 수 있습니다. 단방향 통신 (화재 및 잊어버린)과 양방향 통신 (요청 및 응답) 중에서 선택할 수 있습니다. 각 메서드는 다음과 같습니다.

* **SignalEntity**: 엔터티에 단방향 메시지를 보냅니다.
* **Callentityasync**: 엔터티에 메시지를 보내고 작업이 완료 되었음을 나타내는 응답을 기다립니다.
* **Callentityasync\<T >** : 엔터티에 메시지를 보내고 T 형식의 결과가 포함 된 응답을 기다립니다.

양방향 통신을 사용 하는 경우 작업을 실행 하는 동안 throw 되는 모든 예외도 호출 오케스트레이션으로 다시 전송 되 고 다시 throw 됩니다. 반면, fire를 사용 하는 경우 예외가 관찰 되지 않습니다.

형식이 안전한 액세스의 경우 오케스트레이션 함수는 인터페이스를 기반으로 프록시를 생성할 수 있습니다. 확장 `CreateEntityProxy` 메서드는 다음과 같은 용도로 사용할 수 있습니다.

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

이전 예제에서 "counter" 엔터티는 `IAsyncCounter` 인터페이스를 구현 하는 것으로 간주 됩니다. 그러면 오케스트레이션에 서 `IAsyncCounter` 형식 정의를 사용 하 여 엔터티와 동기적으로 상호 작용 하기 위한 프록시 형식을 생성할 수 있었습니다.

### <a name="locking-entities-from-orchestrations"></a>오케스트레이션에 서 엔터티 잠그기

오케스트레이션이 엔터티를 잠글 수 있습니다. 이 기능은 *중요 한 부분*을 사용 하 여 원치 않는 경합을 방지 하는 간단한 방법을 제공 합니다.

Context 개체는 다음 메서드를 제공 합니다.

* **Lockasync**: 하나 이상의 엔터티에 대 한 잠금을 획득 합니다.
* **Islocked**: 현재 임계 영역에 있으면 true를 반환 하 고 그렇지 않으면 false를 반환 합니다.

오케스트레이션이 종료 되 면 임계 섹션이 종료 되 고 모든 잠금이 해제 됩니다. .Net `LockAsync` 에서 삭제 시 임계 `IDisposable` 영역을 종료 하는를 반환 합니다 .이는 `using` 절과 함께 사용 하 여 임계 영역에 대 한 구문 표현을 가져올 수 있습니다.

예를 들어 두 개의 플레이어를 사용할 수 있는지 여부를 테스트 해야 하는 오케스트레이션을 고려 하 고 게임에 둘 다 할당 합니다. 이 태스크는 다음과 같이 중요 한 섹션을 사용 하 여 구현할 수 있습니다.

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

임계 영역 내에서 두 플레이어 엔터티는 모두 잠겨 있습니다. 즉, 임계 영역 내에서 호출 된 작업 이외의 다른 작업은 실행 되지 않습니다. 이 동작은 다른 게임에 할당 되는 플레이어와 같이 충돌 하는 작업과의 경합을 방지 하 고 로그 아웃 합니다.

중요 섹션을 사용 하는 방법에 대 한 몇 가지 제한 사항이 적용 됩니다. 이러한 제한 사항은 교착 상태 및 재진입을 방지 하는 데 사용 됩니다.

* 임계 섹션은 중첩할 수 없습니다.
* 임계 영역은 하위 오케스트레이션을 만들 수 없습니다.
* 임계 영역은 잠긴 엔터티만 호출할 수 있습니다.
* 임계 영역은 여러 병렬 호출을 사용 하 여 동일한 엔터티를 호출할 수 없습니다.
* 임계 영역은 잠기지 않은 엔터티만 신호를 보낼 수 있습니다.

## <a name="alternate-storage-providers"></a>대체 저장소 공급자

지 속성 작업 프레임 워크는 [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [메모리 내 에뮬레이터](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)및 실험적 [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) 공급자를 포함 하 여 오늘날 여러 저장소 공급자를 지원 합니다. 그러나 지금 까지는 Azure Functions에 대 한 지 속성 작업 확장이 Azure Storage 공급자만 지원 합니다. Durable Functions 2.0부터 대체 저장소 공급자에 대 한 지원이 Redis 공급자부터 추가 됩니다.

> [!NOTE]
> Durable Functions 2.0는 2.0 호환 공급자 .NET Standard 지원 합니다. 이 문서를 작성할 당시 Azure Service Bus 공급자는 .NET Standard 2.0를 지원 하지 않으므로 대체 저장소 공급자로 사용할 수 없습니다.

### <a name="emulator"></a>에뮬레이터

[Microsoft.azure.webjobs.extensions.durabletask](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) 공급자는 로컬 테스트 시나리오에 적합 한 로컬 메모리, 내구성이 없는 저장소 공급자입니다. 다음 최소 **호스트 json** 스키마를 사용 하 여 구성할 수 있습니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (실험적)

[Microsoft.azure.webjobs.extensions.durabletask Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) 공급자는 구성 된 Redis 클러스터에 모든 오케스트레이션 상태를 유지 합니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

는 `connectionStringName` 앱 설정 또는 환경 변수의 이름을 참조 해야 합니다. 해당 앱 설정 또는 환경 변수는 *server: port*형식의 Redis 연결 문자열 값을 포함 해야 합니다. 예를 `localhost:6379` 들어 로컬 Redis 클러스터에 연결 하는 경우입니다.

> [!NOTE]
> Redis 공급자는 현재 실험적 이며 단일 노드에서 실행 되는 함수 앱만 지원 합니다. Redis 공급자가 일반적으로 사용할 수 있게 될 것 이며 이후 릴리스에서 제거 될 수 있습니다.
