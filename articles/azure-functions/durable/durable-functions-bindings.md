---
title: 지속성 함수의 바인딩 - Azure
description: Azure Functions의 Durable Functions 확장에 트리거 및 바인딩을 사용하는 방법입니다.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 40b5f0f17cbb6867a6ef293a485d728141a012ef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233033"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>지속성 함수의 바인딩(Azure Functions)

[지속성 함수](durable-functions-overview.md) 확장은 오케스트레이터 및 작업 함수의 실행을 제어하는 새로운 두 가지 트리거 바인딩을 도입하고 있습니다. 또한 지속성 함수 런타임에 대한 클라이언트 역할을 하는 출력 바인딩도 도입하고 있습니다.

## <a name="orchestration-trigger"></a>Orchestration trigger

The orchestration trigger enables you to author [durable orchestrator functions](durable-functions-types-features-overview.md#orchestrator-functions). 이 트리거는 새 오케스트레이터 함수 인스턴스를 시작하고 작업을 "대기 중인" 기존의 오케스트레이터 함수 인스턴스를 다시 시작할 수 있도록 지원합니다.

Azure Functions에 Visual Studio 도구를 사용하는 경우 오케스트레이션 트리거는 [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET 특성을 사용하여 구성됩니다.

스크립팅 언어(예: JavaScript 또는 C# 스크립팅)에서 오케스트레이터 함수를 작성하는 경우 오케스트레이션 트리거는 *function.json* 파일의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is the name of the orchestration that clients must use when they want to start new instances of this orchestrator function. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 오케스트레이션 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드** - 단일 디스패처 스레드는 단일 호스트 인스턴스에서 모든 오케스트레이터 함수를 실행하는 데 사용됩니다. 이러한 이유로 오케스트레이터 함수 코드가 효율적이고 모든 I/O를 수행하지 않도록 하는 것이 중요합니다. 또한 이 스레드에서 지속성 함수 관련 작업 종류를 기다리는 경우를 제외하고는 비동기 작업을 수행하지 않도록 하는 것이 중요합니다.
* **포이즌 메시지 처리** - 오케스트레이션 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 오케스트레이션 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다. 이러한 반환 값은 나중에 설명할 오케스트레이션 클라이언트 바인딩을 통해 쿼리할 수 있습니다.

> [!WARNING]
> 오케스트레이터 함수는 오케스트레이션 트리거 바인딩 이외의 입력 또는 출력 바인딩을 절대로 사용하면 안됩니다. 이렇게 하면 그러한 바인딩에서 단일 스레딩 및 I/O 규칙을 따르지 않을 수 있으므로 지속성 작업 확장에서 문제가 발생할 수 있습니다. If you'd like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> JavaScript 오케스트레이터 함수는 `async`로 선언되지 않아야 합니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

오케스트레이션 트리거 바인딩은 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **inputs** - .NET orchestration functions support only `DurableOrchestrationContext` as a parameter type. 함수 시그니처에서 직접적인 역직렬화 입력은 지원되지 않습니다. Code must use the `GetInput<T>` (.NET) or `getInput` (JavaScript) method to fetch orchestrator function inputs. 이러한 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 오케스트레이션 트리거는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.

### <a name="trigger-sample"></a>트리거 샘플

The following example code shows what the simplest "Hello World" orchestrator function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> The `context` object in JavaScript does not represent the DurableOrchestrationContext, but the [function context as a whole](../functions-reference-node.md#context-object). `context` 개체의 `df` 속성을 통해 오케스트레이션 메서드에 액세스할 수 있습니다.

> [!NOTE]
> JavaScript 오케스트레이터는 `return`을 사용해야 합니다. `durable-functions` 라이브러리는 `context.done` 메서드 호출을 처리합니다.

대부분의 오케스트레이터 함수에서 작업 함수를 호출하므로 작업 함수를 호출하는 방법을 보여 주는 "Hello World" 예제가 여기에 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activity trigger

The activity trigger enables you to author functions that are called by orchestrator functions, known as [activity functions](durable-functions-types-features-overview.md#activity-functions).

If you're using Visual Studio, the activity trigger is configured using the `ActivityTriggerAttribute` .NET attribute.

개발을 위해 VS Code 또는 Azure Portal을 사용하는 경우 작업 트리거는 *function.json*의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`은 작업의 이름입니다. This value is the name that orchestrator functions use to invoke this activity function. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 큐를 폴링합니다. 이 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 작업 트리거에 대한 몇 가지 참고 사항입니다.

* **스레딩** - 오케스트레이션 트리거와 달리 작업 트리거에는 스레딩 또는 I/O에 대한 제한이 없습니다. 일반 함수처럼 처리될 수 있습니다.
* **포이즌 메시지 처리** - 작업 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 작업 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다.

> [!WARNING]
> 작업 함수에 대한 스토리지 백 엔드는 구현 세부 정보이며, 사용자 코드는 이러한 스토리지 엔터티와 직접 상호 작용하면 안됩니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

작업 트리거 바인딩은 오케스트레이션 트리거와 마찬가지로 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **inputs** - .NET activity functions natively use `DurableActivityContext` as a parameter type. 또는 JSON 직렬화 가능 매개 변수 형식으로 선언될 수 있습니다. When you use `DurableActivityContext`, you can call `GetInput<T>` to fetch and deserialize the activity function input.
* **출력** - 작업 함수는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.
* **메타데이터** - .NET 활동 함수는 `string instanceId` 매개 변수에 바인딩하여 부모 오케스트레이션의 인스턴스 ID를 가져올 수 있습니다.

### <a name="trigger-sample"></a>트리거 샘플

The following example code shows what a simple "Hello World" activity function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

.NET `ActivityTriggerAttribute` 바인딩의 기본 매개 변수 형식은 `IDurableActivityContext`입니다. 그러나 .NET 작업 트리거는 JSON 직렬화 가능 형식(기본 형식 포함)에 대한 직접 바인딩도 지원하므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 바인딩도 추가 매개 변수로 전달될 수 있으므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Using input and output bindings

You can use regular input and output bindings in addition to the activity trigger binding. For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>오케스트레이션 클라이언트

The orchestration client binding enables you to write functions that interact with orchestrator functions. These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions). 예를 들어 오케스트레이션 인스턴스에서 다음과 같은 방법으로 작동할 수 있습니다.

* 인스턴스를 시작합니다.
* 해당 상태를 쿼리합니다.
* 인스턴스를 종료합니다.
* 실행하는 동안 이벤트를 보냅니다.
* 인스턴스 기록을 제거합니다.

If you're using Visual Studio, you can bind to the orchestration client by using the `OrchestrationClientAttribute` .NET attribute for Durable Functions 1.0. Starting in the Durable Functions 2.0, you can bind to the orchestration client by using the `DurableClientAttribute` .NET attribute.

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the orchestration trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 오케스트레이터 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 스토리지 계정은 대상 오케스트레이터 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 이러한 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

### <a name="client-usage"></a>클라이언트 사용

In .NET functions, you typically bind to `IDurableOrchestrationClient`, which gives you full access to all orchestration client APIs supported by Durable Functions. In the older Durable Functions 2.x releases, you instead bind to the `DurableOrchestrationClient` class. In JavaScript, the same APIs are exposed by the object returned from `getClient`. 클라이언트 개체에 대한 API는 다음과 같습니다.

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatively, .NET functions can bind to `IAsyncCollector<T>` where `T` is `StartOrchestrationArgs` or `JObject`.

For more information on these operations, see the `IDurableOrchestrationClient` API documentation.

### <a name="client-sample-visual-studio-development"></a>클라이언트 샘플(Visual Studio 개발)

다음은 "HelloWorld" 오케스트레이션을 시작하는 큐 트리거 함수 예제입니다.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

### <a name="client-sample-not-visual-studio"></a>클라이언트 샘플(Visual Studio 사용 안 함)

개발을 위해 Visual Studio를 사용하지 않는 경우 다음 *function.json* 파일을 만들 수 있습니다. 이 예제에서는 지속성 함수 오케스트레이션 클라이언트 바인딩을 사용하는 큐 트리거 함수를 구성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> The previous JSON is for Durable Functions 2.x. For Durable Functions 1.x, you must use `orchestrationClient` instead of the `durableClient` as the trigger type. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

다음은 새 오케스트레이터 함수 인스턴스를 시작하는 언어 관련 샘플입니다.

#### <a name="c-script-sample"></a>C# Script Sample

The following sample shows how to use the durable orchestration client binding to start a new function instance from a queue-triggered C# function:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-sample"></a>JavaScript 샘플

다음 샘플에서는 지속성 오케스트레이션 클라이언트 바인딩을 사용하여 JavaScript 함수에서 새 함수 인스턴스를 시작하는 방법을 보여줍니다.

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

인스턴스 시작에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)에서 찾을 수 있습니다.

## <a name="entity-trigger"></a>Entity trigger

Entity triggers allow you to author [entity functions](durable-functions-entities.md). This trigger supports processing events for a specific entity instance.

When you use the Visual Studio tools for Azure Functions, the entity trigger is configured using the `EntityTriggerAttribute` .NET attribute.

> [!NOTE]
> Entity triggers are available starting in Durable Functions 2.x.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

Here are some notes about the entity trigger:

* **Single-threaded**: A single dispatcher thread is used to process operations for a particular entity. If multiple messages are sent to a single entity concurrently, the operations will be processed one-at-a-time.
* **Poison-message handling** - There is no poison message support in entity triggers.
* **Message visibility** - Entity trigger messages are dequeued and kept invisible for a configurable duration. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **Return values** - Entity functions do not support return values. There are specific APIs that can be used to save state or pass values back to orchestrations.

Any state changes made to an entity during its execution will be automatically persisted after execution has completed.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

Every entity function has a parameter type of `IDurableEntityContext`, which has the following members:

* **EntityName**: the name of the currently executing entity.
* **EntityKey**: the key of the currently executing entity.
* **EntityId**: the ID of the currently executing entity.
* **OperationName**: the name of the current operation.
* **HasState**: whether the entity exists, that is, has some state. 
* **GetState\<TState>()** : gets the current state of the entity. If it does not already exist, it is created and initialized to `default<TState>`. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **GetState\<TState>(initfunction)** : gets the current state of the entity. If it does not already exist, it is created by calling the provided `initfunction` parameter. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **SetState(arg)** : creates or updates the state of the entity. The `arg` parameter must be a JSON-serializeable object or primitive.
* **DeleteState()** : deletes the state of the entity. 
* **GetInput\<TInput>()** : gets the input for the current operation. The `TInput` type parameter must be a primitive or JSON-serializeable type.
* **Return(arg)** : returns a value to the orchestration that called the operation. The `arg` parameter must be a primitive or JSON-serializeable object.
* **SignalEntity(EntityId, operation, input)** : sends a one-way message to an entity. The `operation` parameter must be a non-null string, and the `input` parameter must be a primitive or JSON-serializeable object.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : starts a new orchestration. The `input` parameter must be a primitive or JSON-serializeable object.

The `IDurableEntityContext` object passed to the entity function can be accessed using the `Entity.Current` async-local property. This approach is convenient when using the class-based programming model.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger sample (C# function-based syntax)

다음 코드는 지속성 함수로 구현된 간단한 *Counter* 엔터티의 예제입니다. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

함수 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [함수 기반 구문](durable-functions-dotnet-entities.md#function-based-syntax)을 참조하세요.

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger sample (C# class-based syntax)

다음 예제에서는 클래스와 메서드를 사용하여 `Counter` 엔터티를 동일하게 구현합니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

이 엔터티의 상태는 카운터의 현재 값을 저장하는 필드가 포함된 `Counter` 형식의 개체입니다. 이 개체를 스토리지에 유지하기 위해 [Json.NET](https://www.newtonsoft.com/json) 라이브러리에서 직렬화 및 역직렬화합니다. 

클래스 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [엔터티 클래스 정의](durable-functions-dotnet-entities.md#defining-entity-classes)를 참조하세요.

> [!NOTE]
> 엔터티 클래스를 사용하는 경우 `[FunctionName]` 특성이 있는 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 진입점 메서드를 사용하면 여러 개체가 초기화되고 잠재적으로 정의되지 않은 다른 동작이 발생할 수 있습니다.

Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. For more information, see [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Trigger sample (JavaScript)

The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> 지속성 엔터티는 `durable-functions` npm 패키지의 버전 **1.3.0**부터 JavaScript에서 사용할 수 있습니다.

## <a name="entity-client"></a>Entity client

The entity client binding enables you to asynchronously trigger [entity functions](#entity-trigger). These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions).

If you're using Visual Studio, you can bind to the entity client by using the `DurableClientAttribute` .NET attribute.

> [!NOTE]
> The `[DurableClientAttribute]` can also be used to bind to the [orchestration client](#orchestration-client).

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the entity trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. This value must match the value used by the target entity functions.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. The storage account represented by this connection string must be the same one used by the target entity functions. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> In most cases, we recommend that you omit the optional properties and rely on the default behavior.

### <a name="entity-client-usage"></a>Entity client usage

In .NET functions, you typically bind to `IDurableEntityClient`, which gives you full access to all client APIs supported by Durable Entities. You can also bind to the `IDurableOrchestrationClient` interface, which provides access to client APIs for both entities and orchestrations. 클라이언트 개체에 대한 API는 다음과 같습니다.

* **ReadEntityStateAsync\<T>** : reads the state of an entity. It returns a response that indicates whether the target entity exists, and if so, what its state is.
* **SignalEntityAsync**: sends a one-way message to an entity, and waits for it to be enqueued.

There is no need to create the target entity before sending a signal - the entity state can be created from within the entity function that handles the signal.

> [!NOTE]
> It's important to understand that the "signals" sent from the client are simply enqueued, to be processed asynchronously at a later time. In particular, the `SignalEntityAsync` usually returns before the entity even starts the operation, and it is not possible to get back the return value or observe exceptions. If stronger guarantees are required (e.g. for workflows), *orchestrator functions* should be used, which can wait for entity operations to complete, and can process return values and observe exceptions.

### <a name="example-client-signals-entity-directly---c"></a>Example: client signals entity directly - C#

Here is an example queue-triggered function that invokes a "Counter" entity.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Example: client signals entity via interface - C#

Where possible, we recommend [accessing entities through interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) because it provides more type checking. For example, suppose the `Counter` entity mentioned earlier implemented an `ICounter` interface, defined as follows:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Client code can then use `SignalEntityAsync<ICounter>` to generate a type-safe proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

The `proxy` parameter is a dynamically generated instance of `ICounter`, which internally translates the call to `Add` into the equivalent (untyped) call to `SignalEntityAsync`.

> [!NOTE]
> The `SignalEntityAsync` APIs represent one-way operations. If an entity interfaces returns `Task<T>`, the value of the `T` parameter will always be null or `default`.

In particular, it does not make sense to signal the `Get` operation, as no value is returned. Instead, clients can use either `ReadStateAsync` to access the counter state directly, or can start an orchestrator function that calls the `Get` operation.

### <a name="example-client-signals-entity---javascript"></a>Example: client signals entity - JavaScript

Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> 지속성 엔터티는 `durable-functions` npm 패키지의 버전 **1.3.0**부터 JavaScript에서 사용할 수 있습니다.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)
