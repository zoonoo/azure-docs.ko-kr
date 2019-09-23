---
title: 지속성 엔터티 - Azure Functions
description: 지속성 엔터티에 대한 기본 사항 및 Azure Functions용 Durable Functions 확장에서 이를 사용하는 방법을 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 99e61cef55bd97704063e4d2da90909d0376c327
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961456"
---
# <a name="entity-functions-preview"></a>엔터티 함수(미리 보기)

엔터티 함수는 *지속성 엔터티*라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. 오케스트레이터 함수와 달리 엔터티 함수에는 특정 코드 제약 조건이 없습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 Durable Functions 2.0 이상에서만 사용할 수 있습니다. 엔터티 함수는 현재 공개 미리 보기로 있습니다.

## <a name="entity-identity"></a>엔터티 ID

엔터티(엔터티 *인스턴스*라고도 함)는 고유 식별자인 *엔터티 ID*를 통해 액세스됩니다. 엔터티 ID는 단순히 엔터티 인스턴스를 고유하게 식별하는 문자열 쌍입니다. ASE는 다음으로 구성됩니다.

* **엔터티 이름**: 엔터티 형식을 식별하는 이름(예: "Counter")입니다.
* **엔터티 키**: 동일한 이름의 다른 모든 엔터티 중에서 해당 엔터티를 고유하게 식별하는 문자열(예: GUID)입니다.

예를 들어 *counter*(카운터) 엔터티 함수는 온라인 게임에서 점수를 유지하는 데 사용할 수 있습니다. 게임의 각 인스턴스에는 `@Counter@Game1`, `@Counter@Game2` 등과 같은 고유한 엔터티 ID가 있습니다. 특정 엔터티를 대상으로 하는 모든 작업에는 엔터티 ID를 매개 변수로 지정해야 합니다.

## <a name="programming-models"></a>프로그래밍 모델

지속성 엔터티는 두 가지 프로그래밍 모델을 지원합니다. 첫 번째 모델은 단일 함수에서 엔터티를 정의하는 동적 "기능" 모델입니다. 두 번째 모델은 클래스와 메서드에서 엔터티를 정의하는 개체 지향 모델입니다. 다음 섹션에서는 엔터티와 상호 작용하는 이러한 모델과 프로그래밍 모델을 설명합니다.

### <a name="defining-entities"></a>엔터티 정의

지속성 엔터티를 작성하는 선택적인 두 가지 프로그래밍 모델이 있습니다. 다음 코드는 표준 함수로 구현된 간단한 *Counter* 엔터티의 예입니다. 이 함수는 각각 `currentValue` 정수 상태 값에서 작동하는 세 개의 *작업*(`add`, `reset` 및 `get`)을 정의합니다.

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

이 모델은 간단한 엔터티 구현 또는 동적 작업 세트가 있는 구현에 가장 적합합니다. 그러나 정적이지만 더 복잡한 구현이 있는 엔터티에 유용한 클래스 기반 프로그래밍 모델을 사용할 수도 있습니다. 다음 예제에서는 클래스와 메서드를 사용하여 `Counter` 엔터티를 동일하게 구현합니다.

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

> [!NOTE]
> 엔터티 클래스를 사용하는 경우 `[FunctionName]` 특성이 있는 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 진입점 메서드를 사용하면 여러 개체가 초기화되고 잠재적으로 정의되지 않은 다른 동작이 발생할 수 있습니다.

클래스 기반 프로그래밍 모델에서 `IDurableEntityContext` 개체는 `Entity.Current` 정적 속성에서 사용할 수 있습니다.

클래스 기반 모델은 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)에서 보급시킨 프로그래밍 모델과 비슷합니다. 이 모델에서 엔터티 형식은 .NET 클래스로 정의됩니다. 클래스의 각 메서드는 외부 클라이언트에서 호출할 수 있는 작업입니다. 그러나 Orleans와 달리 .NET 인터페이스는 선택 사항입니다. 이전 *Counter* 예제에서는 인터페이스를 사용하지 않았지만 다른 함수 또는 HTTP API 호출을 통해 호출할 수 있습니다.

> [!NOTE]
> 엔터티 트리거 함수는 Durable Functions 2.0 이상에서 사용할 수 있습니다. 현재 엔터티 트리거 함수는 .NET 함수 앱에서만 사용할 수 있습니다.

### <a name="accessing-entities-from-clients"></a>클라이언트에서 엔터티에 액세스

[엔터티 클라이언트 출력 바인딩](durable-functions-bindings.md#entity-client)을 사용하여 일반 함수(*클라이언트 함수*라고도 함)에서 지속성 엔터티를 호출하거나 쿼리할 수 있습니다. 다음 예제에서는 이 바인딩을 사용하여 엔터티에 대한 *신호를 보내는* 큐 트리거 함수를 보여 줍니다.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> .NET 함수는 엔터티에 대한 신호를 보내는 느슨하게 형식화된 메서드와 형식이 안전한 메서드를 모두 지원합니다. 자세한 내용은 [엔터티 클라이언트 바인딩](durable-functions-bindings.md#entity-client-usage) 참조 설명서를 참조하세요.

*신호*라는 용어는 엔터티 API 호출이 단방향이며 비동기적임을 의미합니다. *클라이언트 함수*가 엔터티에서 작업을 처리한 시기를 인식할 수 없을 뿐만 아니라, 엔터티 함수가 값을 클라이언트 함수에 반환할 수도 없습니다. 단방향 큐 기반 메시징은 지속성 엔터티에서 성능보다 지속성을 우선적으로 처리하기 위한 의도적인 디자인 선택입니다. 이 디자인 선택은 지속성 엔터티에서 다른 비슷한 기술과 비교할 때 얻을 수 있는 장단점 중 하나입니다. 다음 섹션에서 설명한 대로 현재 오케스트레이션만 엔터티의 반환 값을 처리할 수 있습니다.

또한 클라이언트 함수는 다음 예제와 같이 엔터티의 상태를 쿼리할 수 있습니다.

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

엔터티 상태 쿼리는 지속성 추적 저장소로 보내지고 엔터티의 가장 최근 *persisted*(지속됨) 상태를 반환합니다. 반환된 상태는 엔터티의 메모리 내 상태에 비해 오래된 것일 수 있습니다. 다음 섹션에서 설명한 대로 오케스트레이션만 엔터티의 메모리 내 상태를 읽을 수 있습니다.

### <a name="accessing-entities-from-orchestrations"></a>오케스트레이션에서 엔터티에 액세스

오케스트레이터 함수는 [오케스트레이션 트리거 바인딩](durable-functions-bindings.md#orchestration-trigger)에서 API를 사용하여 엔터티에 액세스할 수 있습니다. 오케스트레이터 함수는 단방향 통신(실행 후 자체 유도, *신호 보내기*라고도 함)과 양방향 통신(요청 및 응답, *호출*이라고도 함) 중에서 선택할 수 있습니다. 다음 예제 코드에서는 *Counter* 엔터티를 *호출*하고 *신호를 보내는* 오케스트레이터 함수를 보여 줍니다.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

오케스트레이션만 엔터티를 호출하고, 반환 값 또는 예외일 수 있는 응답을 받을 수 있습니다. [클라이언트 바인딩](durable-functions-bindings.md#entity-client)을 사용하는 클라이언트 함수는 엔터티에 대한 *신호만 보낼* 수 있습니다.

> [!NOTE]
> 오케스트레이터 함수에서 엔터티를 호출하는 것은 오케스트레이터 함수에서 [활동 함수](durable-functions-types-features-overview.md#activity-functions)를 호출하는 것과 비슷합니다. 주요 차이점은 엔터티 함수가 주소(*엔터티 ID*)를 포함한 지속성 개체이며 작업 이름을 지정할 수 있도록 지원한다는 것입니다. 반면에 활동 함수는 상태 비저장이며 작업의 개념이 없습니다.

### <a name="dependency-injection-in-entity-classes-net"></a>엔터티 클래스의 종속성 주입(.NET)

엔터티 클래스는 [Azure Functions 종속성 주입](../functions-dotnet-dependency-injection.md)을 지원합니다. 다음 예제에서는 `IHttpClientFactory` 서비스를 클래스 기반 엔터티에 등록하는 방법을 보여 줍니다.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

다음 코드 조각에서는 주입된 서비스를 엔터티 클래스에 통합하는 방법을 보여 줍니다.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 일반 .NET Azure Functions에서 생성자 주입을 사용하는 경우와 달리 클래스 기반 엔터티에 대한 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 함수 진입점을 선언하면 일반 Azure Functions 개체 이니셜라이저와 지속성 엔터티 개체 이니셜라이저 간에 충돌이 발생할 수 있습니다.

### <a name="bindings-in-entity-classes-net"></a>엔터티 클래스의 바인딩(.NET)

일반 함수와 달리 엔터티 클래스 메서드는 입력 및 출력 바인딩에 직접 액세스할 수 없습니다. 대신, 진입점 함수 선언에서 바인딩 데이터를 캡처한 다음, `DispatchAsync<T>` 메서드에 전달해야 합니다. `DispatchAsync<T>`에 전달된 모든 개체는 자동으로 엔터티 클래스 생성자에 인수로 전달됩니다.

다음 예제에서는 [Blob 입력 바인딩](../functions-bindings-storage-blob.md#input)의 `CloudBlobContainer` 참조를 클래스 기반 엔터티에 사용할 수 있게 하는 방법을 보여 줍니다.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Azure Functions의 바인딩에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩](../functions-triggers-bindings.md) 설명서를 참조하세요.

## <a name="entity-coordination"></a>엔터티 조정

여러 엔터티 간에 작업을 조정해야 하는 경우가 있을 수 있습니다. 예를 들어 은행 애플리케이션에서 개별 은행 계좌를 나타내는 엔터티가 있을 수 있습니다. 한 계좌에서 다른 계좌로 자금을 이체하는 경우 _원본_ 계좌에 충분한 자금이 있고 _원본_ 및 _대상_ 계좌 모두에 대한 업데이트가 거래 측면에서 일관된 방식으로 수행되는지 확인해야 합니다.

### <a name="transfer-funds-example-in-c"></a>C#의 자금 이체 예제

다음 예제 코드에서는 오케스트레이터 함수를 사용하여 두 _계좌_ 엔터티 간에 자금을 이체합니다. 엔터티 업데이트를 조정하려면 `LockAsync` 메서드를 사용하여 _임계 영역_을 오케스트레이션에 만들어야 합니다.

> [!NOTE]
> 간단히 하기 위해 이 예제에서는 앞에서 정의한 `Counter` 엔터티를 다시 사용합니다. 그러나 실제 애플리케이션에서는 더 자세한 `BankAccount` 엔터티를 대신 정의하는 것이 좋습니다.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

.NET에서 `LockAsync`는 삭제될 때 임계 영역을 종료하는 `IDisposable`을 반환합니다. 이 `IDisposable` 결과를 `using` 블록과 함께 사용하여 임계 영역에 대한 구문 표현을 가져올 수 있습니다.

앞의 예제에서 오케스트레이터 함수는 자금을 _원본_ 엔터티에서 _대상_ 엔터티로 이체했습니다. `LockAsync` 메서드는 _원본_ 및 _대상_ 계좌 엔터티를 모두 잠궜습니다. 이 잠금을 통해 오케스트레이션 논리에서 `using` 문 끝에 있는 _임계 영역_을 종료할 때까지 다른 클라이언트에서 두 계좌의 상태를 쿼리하거나 수정할 수 없습니다. 이로 인해 _원본_ 계좌에서 초과 인출될 가능성이 효과적으로 차단되었습니다.

### <a name="critical-section-behavior"></a>임계 영역 동작

`LockAsync` 메서드는 _임계 영역_을 오케스트레이션에 만듭니다. 이러한 _임계 영역_은 다른 오케스트레이션에서 지정된 엔터티 세트를 중복하여 변경하지 못하도록 방지합니다. `LockAsync` API는 내부적으로 "잠금" 작업을 엔터티에 보내고, 이러한 동일한 엔터티 각각으로부터 "잠금 획득" 응답 메시지를 받으면 반환합니다. *잠금* 및 *잠금 해제* 모두는 모든 엔터티에서 지원하는 기본 제공 작업입니다.

잠긴 상태에 있는 엔터티에는 다른 클라이언트의 작업이 허용되지 않습니다. 이 동작을 통해 한 번에 하나의 오케스트레이션 인스턴스만 엔터티를 잠글 수 있습니다. 호출자가 오케스트레이션에서 잠그고 있는 엔터티에 대한 작업을 호출하려고 하면 해당 작업이 *보류 중인 작업 큐*에 배치됩니다. 보류 중인 작업은 보류 중인 오케스트레이션에서 해당 잠금을 해제할 때까지 처리되지 않습니다.

> [!NOTE] 
> 이는 C#의 `lock` 문과 같이 대부분의 프로그래밍 언어에서 사용되는 동기화 기본 형식과는 약간 다릅니다. 예를 들어 C#에서는 `lock` 문을 모든 스레드에서 사용하여 여러 스레드 간에 올바르게 동기화되도록 보장해야 합니다. 그러나 엔터티는 모든 호출자에서 엔터티를 명시적으로 _잠그도록_ 요구하지는 않습니다. 호출자에서 엔터티를 잠그면 해당 엔터티에 대한 다른 모든 작업이 차단되고 해당 잠금 뒤에서 대기하게 됩니다.

엔터티에 대한 잠금은 지속적이므로 실행 프로세스를 재활용하더라도 계속 유지됩니다. 잠금은 엔터티의 지속성 상태 일부로 내부적으로 유지됩니다.

### <a name="critical-section-restrictions"></a>임계 영역 제한 사항

임계 영역을 사용하는 방법에는 몇 가지 제한 사항이 적용됩니다. 이러한 제한 사항은 교착 상태와 재진입을 방지하는 데 사용됩니다.

* 임계 영역은 중첩할 수 없습니다.
* 임계 영역은 하위 오케스트레이션을 만들 수 없습니다.
* 임계 영역은 잠긴 엔터티만 호출할 수 있습니다.
* 임계 영역은 여러 개의 병렬 호출을 사용하여 동일한 엔터티를 호출할 수 없습니다.
* 임계 영역은 잠기지 않은 엔터티에 대한 신호만 보낼 수 있습니다.

## <a name="comparison-with-virtual-actors"></a>가상 행위자와 비교

대부분의 지속성 엔터티 함수는 [행위자 모델](https://en.wikipedia.org/wiki/Actor_model)로부터 영향을 받습니다. 행위자에 대해 이미 익숙한 경우 이 문서에서 설명하는 여러 가지 개념을 인식할 수 있습니다. 특히 지속성 엔터티는 다음과 같은 다양한 방식에서 [가상 행위자](https://research.microsoft.com/projects/orleans/)와 비슷합니다.

* 지속성 엔터티는 *엔터티 ID*를 통해 주소를 지정할 수 있습니다.
* 지속성 엔터티 작업은 경합 상태를 방지하기 위해 한 번에 하나씩 순차적으로 실행됩니다.
* 지속성 엔터티는 호출되거나 신호를 받을 때 자동으로 만들어집니다.
* 작업을 실행하지 않으면 지속성 엔터티가 메모리에서 자동으로 언로드됩니다.

그러나 다음과 같은 몇 가지 중요한 차이점이 있습니다.

* 지속성 엔터티는 *대기 시간*보다 *지속성*을 우선적으로 처리하므로 엄격한 대기 시간 요구 사항이 있는 애플리케이션에는 적합하지 않을 수 있습니다.
* 엔터티 간에 보내는 메시지는 순서대로 안정적으로 전달됩니다.
* 지속성 엔터티는 지속성 오케스트레이션과 함께 사용할 수 있으며 분산 잠금 메커니즘을 지원합니다.
* 엔터티의 요청/응답 패턴은 오케스트레이션으로 제한됩니다. *클라이언트-엔터티* 및 *엔터티 간* 통신의 경우 원래 행위자 모델에서와 같이 단방향 메시징("신호 보내기"라고도 함)만 허용됩니다. 이 동작은 분산 교착 상태를 방지합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [작업 허브에 대해 알아보기](durable-functions-task-hubs.md)