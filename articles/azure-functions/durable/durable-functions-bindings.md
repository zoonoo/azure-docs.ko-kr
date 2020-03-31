---
title: 지속성 함수의 바인딩 - Azure
description: Azure Functions의 Durable Functions 확장에 트리거 및 바인딩을 사용하는 방법입니다.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278221"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>지속성 함수의 바인딩(Azure Functions)

[지속성 함수](durable-functions-overview.md) 확장은 오케스트레이터 및 작업 함수의 실행을 제어하는 새로운 두 가지 트리거 바인딩을 도입하고 있습니다. 또한 지속성 함수 런타임에 대한 클라이언트 역할을 하는 출력 바인딩도 도입하고 있습니다.

## <a name="orchestration-trigger"></a>오케스트레이션 트리거

오케스트레이션 트리거를 사용하면 [내구성있는 오케스트레이터 함수를 작성할](durable-functions-types-features-overview.md#orchestrator-functions)수 있습니다. 이 트리거는 새 오케스트레이터 함수 인스턴스를 시작하고 작업을 "대기 중인" 기존의 오케스트레이터 함수 인스턴스를 다시 시작할 수 있도록 지원합니다.

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

* `orchestration`는 클라이언트가 이 오케스트레이터 함수의 새 인스턴스를 시작하려고 할 때 사용해야 하는 오케스트레이션의 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 오케스트레이션 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드** - 단일 디스패처 스레드는 단일 호스트 인스턴스에서 모든 오케스트레이터 함수를 실행하는 데 사용됩니다. 이러한 이유로 오케스트레이터 함수 코드가 효율적이고 모든 I/O를 수행하지 않도록 하는 것이 중요합니다. 또한 이 스레드에서 지속성 함수 관련 작업 종류를 기다리는 경우를 제외하고는 비동기 작업을 수행하지 않도록 하는 것이 중요합니다.
* **포이즌 메시지 처리** - 오케스트레이션 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 오케스트레이션 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다. 이러한 반환 값은 나중에 설명할 오케스트레이션 클라이언트 바인딩을 통해 쿼리할 수 있습니다.

> [!WARNING]
> 오케스트레이터 함수는 오케스트레이션 트리거 바인딩 이외의 입력 또는 출력 바인딩을 절대로 사용하면 안됩니다. 이렇게 하면 그러한 바인딩에서 단일 스레딩 및 I/O 규칙을 따르지 않을 수 있으므로 지속성 작업 확장에서 문제가 발생할 수 있습니다. 다른 바인딩을 사용하려는 경우 Orchestrator 함수에서 호출된 활동 함수에 추가합니다.

> [!WARNING]
> JavaScript 오케스트레이터 함수는 `async`로 선언되지 않아야 합니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

오케스트레이션 트리거 바인딩은 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - .NET 오케스트레이션 `DurableOrchestrationContext` 함수는 매개 변수 유형으로만 지원합니다. 함수 시그니처에서 직접적인 역직렬화 입력은 지원되지 않습니다. 코드는 오케스트레이터 함수 입력을 `GetInput<T>` 가져오기 위해 (.NET) 또는 `getInput` (JavaScript) 메서드를 사용해야 합니다. 이러한 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 오케스트레이션 트리거는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.

### <a name="trigger-sample"></a>트리거 샘플

다음 예제 코드는 가장 간단한 "Hello World" 오케스트레이터 함수의 모양을 보여 주며 다음과 같습니다.

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
> 이전 코드는 지속 기능 2.x용입니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> 자바 `context` 스크립트의 개체는 내성오케스트라컨텍스트를 나타내지 않지만 [함수 컨텍스트를 전체적으로](../functions-reference-node.md#context-object)나타냅니다. `context` 개체의 `df` 속성을 통해 오케스트레이션 메서드에 액세스할 수 있습니다.

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
> 이전 코드는 지속 기능 2.x용입니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>활동 트리거

활동 트리거를 사용하면 활동 함수라고 하는 오케스트레이터 함수에서 호출되는 [함수를 작성할 수 있습니다.](durable-functions-types-features-overview.md#activity-functions)

Visual Studio를 사용하는 경우 활동 트리거는 .NET `ActivityTriggerAttribute` 특성을 사용하여 구성됩니다.

개발을 위해 VS Code 또는 Azure Portal을 사용하는 경우 작업 트리거는 *function.json*의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`은 작업의 이름입니다. 이 값은 오케스트레이터 함수가 이 활동 함수를 호출하는 데 사용하는 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

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

* **입력** - .NET 활동 함수는 `DurableActivityContext` 기본적으로 매개 변수 유형으로 사용합니다. 또는 JSON 직렬화 가능 매개 변수 형식으로 선언될 수 있습니다. 을 사용하면 `DurableActivityContext`호출하여 `GetInput<T>` 활동 함수 입력을 가져오고 역직렬화할 수 있습니다.
* **출력** - 작업 함수는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.
* **메타데이터** - .NET 활동 함수는 `string instanceId` 매개 변수에 바인딩하여 부모 오케스트레이션의 인스턴스 ID를 가져올 수 있습니다.

### <a name="trigger-sample"></a>트리거 샘플

다음 예제 코드는 간단한 "Hello World" 활동 함수의 모양을 보여 주며 다음과 같습니다.

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
> 이전 코드는 지속 기능 2.x용입니다. 지속 기능 1.x의 경우 `DurableActivityContext` `IDurableActivityContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

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


### <a name="using-input-and-output-bindings"></a>입력 및 출력 바인딩 사용

활동 트리거 바인딩 외에 일반 입력 및 출력 바인딩을 사용할 수 있습니다. 예를 들어 활동 바인딩에 입력을 가져 와서 EventHub 출력 바인딩을 사용하여 EventHub에 메시지를 보낼 수 있습니다.

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

오케스트레이션 클라이언트 바인딩을 사용하면 오케스트레이터 함수와 상호 작용하는 함수를 작성할 수 있습니다. 이러한 함수를 [클라이언트 함수라고도 합니다.](durable-functions-types-features-overview.md#client-functions) 예를 들어 오케스트레이션 인스턴스에서 다음과 같은 방법으로 작동할 수 있습니다.

* 인스턴스를 시작합니다.
* 해당 상태를 쿼리합니다.
* 인스턴스를 종료합니다.
* 실행하는 동안 이벤트를 보냅니다.
* 인스턴스 기록을 제거합니다.

Visual Studio를 사용하는 경우 지속 기능 1.0에 `OrchestrationClientAttribute` 대한 .NET 특성을 사용하여 오케스트레이션 클라이언트에 바인딩할 수 있습니다. 지속형 함수 2.0부터 .NET 특성을 사용하여 오케스트레이션 클라이언트에 바인딩할 `DurableClientAttribute` 수 있습니다.

개발을 위해 스크립팅 언어(예: *.csx* 또는 *.js* 파일)를 사용하는 경우 오케스트레이션 트리거는 `bindings` *function.json*배열의 다음 JSON 개체에 의해 정의됩니다.

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

.NET 함수에서 일반적으로 `IDurableOrchestrationClient`에 바인딩하여 내구성 함수에서 지원하는 모든 오케스트레이션 클라이언트 API에 대한 전체 액세스 권한을 부여합니다. 이전 지속 함수 2.x 릴리스에서는 클래스에 `DurableOrchestrationClient` 바인딩합니다. 자바스크립트에서 반환된 개체에 의해 동일한 API가 `getClient`노출됩니다. 클라이언트 개체에 대한 API는 다음과 같습니다.

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

또는 .NET 함수는 있는 `IAsyncCollector<T>` 위치에 `T` `StartOrchestrationArgs` 바인딩할 수 `JObject`있습니다.

이러한 작업에 대한 자세한 내용은 `IDurableOrchestrationClient` API 설명서를 참조하십시오.

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
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

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
> 이전 JSON은 내구성 함수 2.x용입니다. 지속형 함수 1.x의 경우 `orchestrationClient` 트리거 `durableClient` 유형 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

다음은 새 오케스트레이터 함수 인스턴스를 시작하는 언어 관련 샘플입니다.

#### <a name="c-script-sample"></a>C # 스크립트 샘플

다음 샘플에서는 지속적 오케스트레이션 클라이언트 바인딩을 사용하여 큐트리거 C# 함수에서 새 함수 인스턴스를 시작하는 방법을 보여 주며 있습니다.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 이전 코드는 지속 기능 2.x용입니다. 지속 함수 1.x의 경우 `DurableOrchestrationClient` `IDurableOrchestrationClient`매개 변수 형식을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

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

## <a name="entity-trigger"></a>엔터티 트리거

엔터티 트리거를 사용하면 [엔터티 함수를 작성할](durable-functions-entities.md)수 있습니다. 이 트리거는 특정 엔터티 인스턴스에 대한 이벤트 처리를 지원합니다.

Azure Functions에 Visual Studio 도구를 사용하는 경우 엔터티 트리거는 `EntityTriggerAttribute` .NET 특성을 사용하여 구성됩니다.

> [!NOTE]
> 엔터티 트리거는 지속 함수 2.x에서 부터 사용할 수 있습니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

엔터티 트리거에 대한 몇 가지 참고 사항은 다음과 같습니다.

* **단일 스레드**: 단일 디스패처 스레드는 특정 엔터티에 대한 작업을 처리하는 데 사용됩니다. 여러 메시지가 동시에 단일 엔터티로 전송되는 경우 작업은 한 번에 하나씩 처리됩니다.
* **포이즌 메시지 처리** - 엔터티 트리거에 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 엔터티 트리거 메시지는 큐에서 해제되고 구성 가능한 기간 동안 표시되지 않습니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 엔터티 함수는 반환 값을 지원하지 않습니다. 상태를 저장하거나 값을 오케스트레이션으로 다시 전달하는 데 사용할 수 있는 특정 API가 있습니다.

실행 중에 엔터티에 대한 상태 변경 사항은 실행이 완료된 후 자동으로 유지됩니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

모든 엔터티 함수에는 다음 `IDurableEntityContext`멤버가 있는 매개 변수 형식이 있습니다.

* **엔터티Name**: 현재 실행 중인 엔터티의 이름입니다.
* **EntityKey**: 현재 실행 중인 엔터티의 키입니다.
* **EntityId**: 현재 실행 중인 엔터티의 ID입니다.
* **작업 이름**: 현재 작업의 이름입니다.
* **HasState**: 엔터티가 있는지 여부, 즉 일부 상태가 있습니다. 
* **GetState\<TState>()**: 엔터티의 현재 상태를 가져옵니다. 아직 존재하지 않는 경우 `default<TState>`생성되고 에 초기화됩니다. 매개 `TState` 변수는 기본 또는 JSON 직렬화 가능한 형식이어야 합니다. 
* **GetState\<TState>(initfunction)**: 엔터티의 현재 상태를 가져옵니다. 아직 존재하지 않는 경우 제공된 `initfunction` 매개 변수를 호출하여 만들어집니다. 매개 `TState` 변수는 기본 또는 JSON 직렬화 가능한 형식이어야 합니다. 
* **SetState(아르그)**: 엔터티의 상태를 만들거나 업데이트합니다. 매개 `arg` 변수는 JSON 직렬화 가능한 개체 이거나 기본 개체여야 합니다.
* **DeleteState()**: 엔터티의 상태를 삭제합니다. 
* **GetInput\<TInput>()**: 현재 작업에 대한 입력을 가져옵니다. 형식 `TInput` 매개 변수는 기본 또는 JSON 직렬화 가능한 형식이어야 합니다.
* **Return (arg)**: 작업을 호출한 오케스트레이션에 값을 반환합니다. 매개 `arg` 변수는 기본 또는 JSON 직렬화 가능한 개체여야 합니다.
* **SignalEntity(EntityId, 예약된TimeUtc, 작업, 입력)**: 엔터티에 단방향 메시지를 보냅니다. 매개 `operation` 변수는 null이 아닌 문자열이어야 `scheduledTimeUtc` 하며, 선택적 작업은 작업을 호출하는 UTC datetime이어야 하며 `input` 매개 변수는 기본 개체 또는 JSON serializeable 개체여야 합니다.
* **만들기새오케스트레이션(orchestratorFunctionName, 입력)**: 새 오케스트레이션을 시작합니다. 매개 `input` 변수는 기본 또는 JSON 직렬화 가능한 개체여야 합니다.

엔터티 `IDurableEntityContext` 함수에 전달된 개체는 비동기 `Entity.Current` 로컬 속성을 사용하여 액세스할 수 있습니다. 이 방법은 클래스 기반 프로그래밍 모델을 사용할 때 편리합니다.

### <a name="trigger-sample-c-function-based-syntax"></a>트리거 샘플(C# 함수 기반 구문)

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

함수 기반 구문 및 사용 방법에 대한 자세한 내용은 [함수 기반 구문을](durable-functions-dotnet-entities.md#function-based-syntax)참조하십시오.

### <a name="trigger-sample-c-class-based-syntax"></a>트리거 샘플(C# 클래스 기반 구문)

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

엔터티 클래스에는 바인딩 및 .NET 종속성 주입과 상호 작용하기 위한 특별한 메커니즘이 있습니다. 자세한 내용은 [엔터티 구성을](durable-functions-dotnet-entities.md#entity-construction)참조하십시오.

### <a name="trigger-sample-javascript"></a>트리거 샘플(자바스크립트)

다음 코드는 JavaScript로 작성된 지속가능한 함수로 구현된 간단한 *Counter* 엔터티의 예입니다. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

**함수.json**
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

## <a name="entity-client"></a>엔터티 클라이언트

엔터티 클라이언트 바인딩을 사용하면 엔터티 함수를 비동기적으로 [트리거할 수 있습니다.](#entity-trigger) 이러한 함수를 [클라이언트 함수라고도 합니다.](durable-functions-types-features-overview.md#client-functions)

Visual Studio를 사용하는 경우 .NET 특성을 사용하여 엔터티 `DurableClientAttribute` 클라이언트에 바인딩할 수 있습니다.

> [!NOTE]
> 또한 `[DurableClientAttribute]` [오케스트레이션 클라이언트에](#orchestration-client)바인딩하는 데 사용할 수 있습니다.

개발에 스크립팅 언어(예: *.csx* 또는 *.js* 파일)를 사용하는 경우 엔터티 트리거는 `bindings` *function.json*배열의 다음 JSON 개체에 의해 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 엔터티 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열로 표시되는 저장소 계정은 대상 엔터티 함수에서 사용하는 것과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 선택적 속성을 생략하고 기본 동작에 의존하는 것이 좋습니다.

### <a name="entity-client-usage"></a>엔터티 클라이언트 사용

.NET 함수에서 일반적으로 `IDurableEntityClient`에 바인딩하여 지속성 엔터티에서 지원하는 모든 클라이언트 API에 대한 전체 액세스 권한을 부여합니다. 엔터티와 오케스트레이션 모두에 대한 클라이언트 API에 대한 액세스를 제공하는 `IDurableOrchestrationClient` 인터페이스에 바인딩할 수도 있습니다. 클라이언트 개체에 대한 API는 다음과 같습니다.

* **ReadEntityStateAsync\<T>**: 엔티티의 상태를 읽습니다. 대상 엔터티가 있는지 여부와 있는 경우 해당 상태가 무엇인지 나타내는 응답을 반환합니다.
* **SignalEntityAsync**: 엔터티에 단방향 메시지를 보내고 큐에 대기될 때까지 기다립니다.
* **ListEntitiesAsync**: 여러 엔터티의 상태에 대한 쿼리입니다. 엔터티를 *이름과* *마지막 작업 시간으로*쿼리할 수 있습니다.

신호를 보내기 전에 대상 엔터티를 만들 필요가 없습니다 - 엔터티 상태는 신호를 처리하는 엔터티 함수 내에서 만들 수 있습니다.

> [!NOTE]
> 클라이언트에서 보낸 "신호"는 단순히 큐에 대기되어 나중에 비동기적으로 처리된다는 것을 이해하는 것이 중요합니다. 특히 일반적으로 `SignalEntityAsync` 엔터티가 작업을 시작하기 전에 반환되며 반환 값을 반환하거나 예외를 관찰할 수 없습니다. 더 강력한 보장(예: 워크플로의 경우)이 필요한 경우 엔터티 작업이 완료될 때까지 기다릴 수 있고 반환 값을 처리하고 예외를 관찰할 수 있는 *오케스트레이터 함수를* 사용해야 합니다.

### <a name="example-client-signals-entity-directly---c"></a>예: 클라이언트 신호 엔티티 직접 - C #

다음은 "카운터" 엔터티를 호출하는 큐 트리거 함수의 예입니다.

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

### <a name="example-client-signals-entity-via-interface---c"></a>예: 인터페이스를 통한 클라이언트 신호 엔티티 - C #

가능한 경우 더 많은 형식 검사를 제공하므로 [인터페이스를 통해 엔터티에 액세스하는](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) 것이 좋습니다. 예를 들어 앞에서 `Counter` 언급한 엔터티가 `ICounter` 다음과 같이 정의된 인터페이스를 구현했다고 가정합니다.

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

그런 다음 클라이언트 `SignalEntityAsync<ICounter>` 코드를 사용하여 형식 이 안전한 프록시를 생성할 수 있습니다.

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

`proxy` 매개 변수는 내부적으로 호출을 `ICounter`등가(형식이 없는) `Add` 호출로 변환하는 동적으로 `SignalEntityAsync`생성된 인스턴스입니다.

> [!NOTE]
> API는 `SignalEntityAsync` 단방향 작업을 나타냅니다. 엔터티 인터페이스가 반환되는 `Task<T>`경우 매개 `T` 변수값은 항상 `default`null 또는 .

특히 값이 반환되지 않으므로 `Get` 작업을 신호하는 것은 의미가 없습니다. 대신 클라이언트는 카운터 `ReadStateAsync` 상태에 직접 액세스하거나 작업을 호출하는 오케스트레이터 `Get` 함수를 시작할 수 있습니다.

### <a name="example-client-signals-entity---javascript"></a>예: 클라이언트 신호 엔티티 - 자바스크립트

다음은 JavaScript에서 "카운터" 엔터티를 알리는 큐 트리거 함수입니다.

**함수.json**
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
> [인스턴스 관리를 위한 기본 제공 HTTP API 참조](durable-functions-http-api.md)
