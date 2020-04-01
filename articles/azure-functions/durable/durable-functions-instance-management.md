---
title: 지속성 함수의 인스턴스 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 인스턴스를 관리하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476820"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure에서 Durable Functions의 인스턴스 관리

Azure [Functions에](durable-functions-overview.md) 대해 지속형 함수 확장을 사용중이거나 작업을 시작하려면 이 확장을 최대한 활용해야 합니다. 지속형 함수 오케스트레이션 인스턴스를 관리하는 방법에 대해 자세히 알아보면 최적화할 수 있습니다. 이 문서에서는 각 인스턴스 관리 작업에 대해 자세히 설명합니다.

예를 들어 인스턴스를 시작하고 종료할 수 있으며 필터를 사용하여 모든 인스턴스 및 쿼리 인스턴스를 쿼리하는 기능을 포함하여 인스턴스를 쿼리할 수 있습니다. 또한 인스턴스에 이벤트를 보내고, 오케스트레이션 완료를 기다리며, HTTP 관리 웹후크 URL을 검색할 수 있습니다. 이 문서에서는 인스턴스 되감기, 인스턴스 기록 제거 및 작업 허브 삭제를 비롯한 다른 관리 작업도 다룹니다.

지속형 함수에서는 이러한 각 관리 작업을 구현하는 방법에 대한 옵션이 있습니다. 이 문서에서는 .NET(C#) 및 JavaScript 모두에 대해 [Azure 함수 핵심 도구를](../functions-run-local.md) 사용하는 예제를 제공합니다.

## <a name="start-instances"></a>인스턴스 시작

오케스트레이션 인스턴스를 시작할 수 있어야 합니다. 이 작업은 일반적으로 다른 함수의 트리거에서 바인딩되는 지속 함수를 사용할 때 수행됩니다.

`StartNewAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client) (.NET) 또는 `startNew` (JavaScript) 메서드는 새 인스턴스를 시작합니다. 내부적으로 이 메서드는 메시지를 제어 큐에 큐에 넣은 다음 [오케스트레이션 트리거 바인딩을](durable-functions-bindings.md#orchestration-trigger)사용하는 지정된 이름으로 함수의 시작을 트리거합니다.

오케스트레이션 프로세스가 성공적으로 예약되면 이 비동기 작업이 완료됩니다.

새 오케스트레이션 인스턴스를 시작하기 위한 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **Input**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 이 매개 변수를 지정하지 않으면 메서드에서 임의 ID를 사용합니다.

> [!TIP]
> 인스턴스 ID에 임의의 식별자를 사용하세요. 임의 인스턴스 아이디는 여러 VM에서 오케스트레이션 함수를 확장할 때 동일한 부하 분포를 보장합니다. 비무작위 인스턴스 ID를 사용하는 적절한 시간은 ID가 외부 소스에서 제공되어야 하거나 [singleton 오케스트레이터](durable-functions-singletons.md) 패턴을 구현하는 경우입니다.

다음 코드는 새 오케스트레이션 인스턴스를 시작하는 예제 함수입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>달리 명시되지 않는 한 이 페이지의 예제에서는 다음 function.json과 함께 HTTP 트리거를 사용합니다.

**함수.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
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

> [!NOTE]
> 이 예제는 내성 함수 버전 2.x를 대상으로 합니다. 버전 1.x에서는 `orchestrationClient` `durableClient`을 대신 사용합니다.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[ AzureFunctionsCoreTools](../functions-run-local.md) `durable start-new`명령을 사용하여 직접 인스턴스를 시작할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 시작할 함수의 이름입니다. ** `function-name` **
* (선택 사항) : 인라인 또는 JSON 파일을 통해 함수에 입력합니다. ** `input` ** 파일의 경우 와 같은 `@` `@path/to/file.json`을 사용하여 파일의 경로에 접두사를 추가합니다.
* (선택 사항) : 오케스트레이션 인스턴스의 ID입니다. ** `id` ** 이 매개 변수를 지정하지 않으면 명령에서 임의의 GUID를 사용합니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 AzureWebJobsStorage입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 DurableFunctionsHub입니다. 또한 내구형Task:HubName를 사용하여 [host.json에서](durable-functions-bindings.md#host-json) 이 것을 설정할 수도 있습니다.

> [!NOTE]
> 핵심 도구 명령은 함수 앱의 루트 디렉토리에서 실행중인 것으로 가정합니다. 및 `connection-string-setting` `task-hub-name` 매개 변수를 명시적으로 제공하는 경우 모든 디렉터리에서 명령을 실행할 수 있습니다. 함수 앱 호스트가 실행되지 않고 이러한 명령을 실행할 수 있지만 호스트가 실행되지 않는 한 일부 효과를 관찰할 수 없습니다. 예를 들어 `start-new` 명령은 시작 메시지를 대상 작업 허브로 큐에 넣지만 메시지를 처리할 수 있는 함수 앱 호스트 프로세스가 실행되지 않는 한 오케스트레이션은 실제로 실행되지 않습니다.

다음 명령은 HelloWorld라는 함수를 시작하고 파일의 `counter-data.json` 내용을 전달합니다.

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>쿼리 인스턴스

오케스트레이션을 관리하려는 노력의 일환으로 오케스트레이션 인스턴스의 상태(예: 정상적으로 완료되었는지 실패했는지 여부)에 대한 정보를 수집해야 할 가능성이 큽니다.

`GetStatusAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client) `getStatus` (.NET) 또는 (JavaScript) 메서드는 오케스트레이션 인스턴스의 상태를 쿼리합니다.

`instanceId`(필수), `showHistory`(선택 사항), `showHistoryOutput`(선택 사항) 및 `showInput`(선택 사항)을 매개 변수로 사용합니다.

* **`showHistory`**: `true`로 설정된 경우 응답에는 실행 기록이 포함됩니다.
* **`showHistoryOutput`**: `true`로 설정된 경우 실행 기록에 활동 출력이 포함됩니다.
* **`showInput`**: `false`로 설정하면 응답에 함수의 입력이 포함되지 않습니다. 기본값은 `true`입니다.

메서드는 다음과 같은 속성이 있는 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **Input**: JSON 값의 함수 입력입니다. 이 필드는 false인 경우 `showInput` 채워지지 않습니다.
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다.
* **Output**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오케스트레이터 함수가 실패한 경우 이 속성에는 실패 세부 정보가 포함됩니다. 오케스트레이터 함수가 종료된 경우 이 속성에는 종료 이유(있는 경우)가 포함됩니다.
* **RuntimeStatus**: 다음 값 중 하나입니다.
  * **보류 중**: 인스턴스는 일정이 있지만 아직 실행을 시작하지 않았습니다.
  * **실행 중**: 인스턴스가 실행되기 시작했습니다.
  * **완료됨**: 인스턴스가 정상적으로 완료되었습니다.
  * **ContinuedAsNew(새 기록으로 계속됨)**: 인스턴스가 새 기록으로 다시 시작되었습니다. 이 상태는 일시적인 상태입니다.
  * **실패**: 인스턴스가 오류로 인해 실패했습니다.
  * **종료됨**: 인스턴스가 갑자기 중지되었습니다.
* **기록**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.

이 메서드는 인스턴스가 `undefined` 없는 경우 (.NET) 또는 (자바스크립트)를 반환합니다. `null`

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable get-runtime-status` 명령을 사용하여 오케스트레이션 인스턴스의 상태를 직접 가져올 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 오케스트레이션 인스턴스의 ID입니다. ** `id` **
* (선택 사항) : `true`로 설정하면 응답에 함수의 입력이 포함되어 있습니다. ** `show-input` ** 기본값은 `false`입니다.
* (선택 사항) : `true`로 설정하면 응답에 함수의 출력이 포함됩니다. ** `show-output` ** 기본값은 `false`입니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구형Task:HubName을 사용하여 [host.json에서](durable-functions-bindings.md#host-json)설정할 수도 있습니다.

다음 명령은 0ab8c5a6644d68a3a8b220b12d209c의 오케스트레이션 인스턴스 ID가 있는 인스턴스의 상태(입력 및 출력 포함)를 검색합니다. 함수 앱의 `func` 루트 디렉터리에서 명령을 실행 한다고 가정 합니다.

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

명령을 `durable get-history` 사용하여 오케스트레이션 인스턴스의 기록을 검색할 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 오케스트레이션 인스턴스의 ID입니다. ** `id` **
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구성 태스크:HubName을 사용하여 host.json에서 설정할 수도 있습니다.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>모든 인스턴스 쿼리

한 번에 하나의 인스턴스를 쿼리하는 대신 한 번에 모든 인스턴스를 쿼리하는 것이 더 효율적일 수 있습니다.

`GetStatusAsync`(.NET) 또는 `getStatusAll`(JavaScript) 메서드를 사용하여 모든 오케스트레이션 인스턴스의 상태를 쿼리할 수 있습니다. .NET에서 취소하려는 경우 `CancellationToken` 객체를 전달할 수 있습니다. 이 메서드는 매개 변수가 있는 `GetStatusAsync` 메서드와 속성이 동일한 개체를 반환합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable get-instances` 명령을 사용하여 인스턴스를 직접 쿼리할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (선택 사항) : 이 명령은 페이징을 지원합니다. ** `top` ** 이 매개 변수는 요청당 검색된 인스턴스의 수에 해당합니다. 기본값은 10입니다.
* (선택 사항) : 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. ** `continuation-token` ** `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구형Task:HubName을 사용하여 [host.json에서](durable-functions-bindings.md#host-json)설정할 수도 있습니다.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>필터가 있는 쿼리 인스턴스

표준 인스턴스 쿼리가 제공할 수 있는 모든 정보가 실제로 필요하지 않으면 어떻게 해야 할까요? 예를 들어 오케스트레이션 생성 시간 또는 오케스트레이션 런타임 상태를 찾고 있다면 어떨까요? 필터를 적용하여 쿼리 범위를 좁힐 수 있습니다.

`GetStatusAsync` (.NET) 또는 `getStatusBy` (JavaScript) 메서드를 사용하여 미리 정의된 필터 집합과 일치하는 오케스트레이션 인스턴스 목록을 가져옵니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Azure 함수 핵심 도구에서 필터와 `durable get-instances` 함께 명령을 사용할 수도 있습니다. 전술한 `top` `continuation-token`, 및 `connection-string-setting` `task-hub-name` 매개 변수 외에도 세 가지 필터 매개 변수`created-after`(, `created-before` `runtime-status`및 )를 사용할 수 있습니다.

* (선택 사항) : 이 날짜/시간(UTC) 이후에 생성된 인스턴스를 검색합니다. ** `created-after` ** ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* (선택 사항) : 이 날짜/시간(UTC) 이전에 생성된 인스턴스를 검색합니다. ** `created-before` ** ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* (선택 사항) : 특정 상태(예: 실행 중 또는 완료됨)가 있는 인스턴스를 검색합니다. ** `runtime-status` ** 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* (선택 사항) : 요청당 검색된 인스턴스 수입니다. ** `top` ** 기본값은 10입니다.
* (선택 사항) : 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. ** `continuation-token` ** `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구형Task:HubName을 사용하여 [host.json에서](durable-functions-bindings.md#host-json)설정할 수도 있습니다.

필터`created-after`(또는) `created-before` `runtime-status`명령을 제공하지 않으면 런타임 상태 또는 `top` 생성 시간과 관계없이 인스턴스를 검색합니다.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>인스턴스 종료

실행하는 데 너무 오래 걸리는 오케스트레이션 인스턴스가 있거나 어떤 이유로든 오케스트레이션 인스턴스를 완료하기 전에 중지해야 하는 경우 오케스트레이션 인스턴스를 종료할 수 있습니다.

`TerminateAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client) `terminate` (.NET) 또는 (JavaScript) 메서드를 사용하여 인스턴스를 종료할 수 있습니다. 두 매개 변수는 `instanceId` 로그와 인스턴스 상태에 기록되는 `reason` 문자열과 문자열입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

종료된 인스턴스는 결국 `Terminated` 상태로 전환됩니다. 그러나 이 전환은 즉시 발생하지 않습니다. 대신 종료 작업은 해당 인스턴스의 다른 작업과 함께 작업 허브에 큐에 대기됩니다. [인스턴스 쿼리](#query-instances) API를 사용하여 종료된 인스턴스가 실제로 상태에 `Terminated` 도달한 시기를 알 수 있습니다.

> [!NOTE]
> 인스턴스 종료는 현재 전파되지 않습니다. 활동 함수 및 하위 오케스트레이션은 해당 함수를 호출한 오케스트레이션 인스턴스를 종료했는지 여부에 관계없이 완료될 때까지 실행됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[ AzureFunctionsCoreTools](../functions-run-local.md) `durable terminate` 명령을 사용하여 오케스트레이션 인스턴스를 직접 종료할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 종료할 오케스트레이션 인스턴스의 ID입니다. ** `id` **
* (선택 사항) : 종료 사유. ** `reason` **
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구형Task:HubName을 사용하여 [host.json에서](durable-functions-bindings.md#host-json)설정할 수도 있습니다.

다음 명령은 0ab8c55a66644d68a3a8b20b12d209c의 ID로 오케스트레이션 인스턴스를 종료합니다.

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>인스턴스에 이벤트 보내기

일부 시나리오에서는 오케스트레이터 함수가 외부 이벤트를 기다렸다가 들을 수 있어야 합니다. 여기에는 [인간의 상호 작용을](durable-functions-overview.md#human)기다리는 모니터 [기능](durable-functions-overview.md#monitoring) 및 기능이 포함됩니다.

`RaiseEventAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client)(.NET) 메서드 `raiseEvent` 또는 (JavaScript) 메서드를 사용 하 여 실행 중인 인스턴스에 이벤트 알림을 보냅니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 `WaitForExternalEvent` (.NET) 호출을 기다리거나 `waitForExternalEvent` (JavaScript) 호출에 양보하는 인스턴스입니다.

(.NET) `RaiseEventAsync` 및 `raiseEvent` (자바 스크립트)에 대한 매개 변수는 다음과 같습니다 .

* **인스턴스 Id**: 인스턴스의 고유 ID입니다.
* **EventName**: 보낼 이벤트의 이름입니다.
* **EventData**: 인스턴스에 보낼 JSON 직렬화 가능 페이로드입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

> [!NOTE]
> 지정된 인스턴스 ID가 있는 오케스트레이션 인스턴스가 없으면 이벤트 메시지가 삭제됩니다. 인스턴스가 있지만 아직 이벤트를 기다리지 않는 경우 이벤트를 수신 및 처리할 준비가 될 때까지 인스턴스 상태에 저장됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable raise-event` 명령을 사용하여 오케스트레이션 인스턴스에 직접 이벤트를 발생 시킬 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 오케스트레이션 인스턴스의 ID입니다. ** `id` **
* **`event-name`**: 제기할 이벤트 이름입니다.
* (선택 사항) : 오케스트레이션 인스턴스로 보낼 데이터입니다. ** `event-data` ** JSON 파일의 경로이거나 명령줄에서 직접 데이터를 제공할 수 있습니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본값은 `DurableFunctionsHub`입니다. 또한 내구형Task:HubName을 사용하여 [host.json에서](durable-functions-bindings.md#host-json)설정할 수도 있습니다.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

장기 실행 오케스트레이션에서는 오케스트레이션의 결과를 기다렸다가 얻을 수 있습니다. 이러한 경우 오케스트레이션에서 시간 시간 시간(시간)을 정의할 수도 있습니다. 시간 초과가 초과되면 결과 대신 오케스트레이션 상태를 반환해야 합니다.

`WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) 또는 `waitForCompletionOrCreateCheckStatusResponse` (자바 스크립트) 메서드는 동기 적으로 오케스트레이션 인스턴스에서 실제 출력을 가져옵니다. 기본적으로 이러한 메서드는 에 대해 10초, 에 `timeout`대해 `retryInterval`1초의 기본값을 사용합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

다음 줄로 함수를 호출합니다. 시간 시간에는 2초, 재시도 간격은 0.5초 사용하십시오.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

* 오케스트레이션 인스턴스는 정의된 시간 시간(이 경우 2초) 내에 완료되고 응답은 동기적으로 전달되는 실제 오케스트레이션 인스턴스 출력입니다.

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 오케스트레이션 인스턴스는 정의된 시간 시간 내에 완료할 수 없으며 응답은 [HTTP API URL 검색에](durable-functions-http-api.md)설명된 기본 인스턴스입니다.

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> 실행 중인 Azure Functions 호스트 버전에 따라 웹후크 URL의 형식이 다를 수 있습니다. 앞의 예제는 Azure Functions 2.0 호스트에 대한 것입니다.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP 관리 웹후크 URL 검색

외부 시스템을 사용하여 이벤트를 모니터링하거나 오케스트레이션에 올릴 수 있습니다. 외부 시스템은 [HTTP API URL 검색에](durable-functions-http-features.md#http-api-url-discovery)설명된 기본 응답의 일부인 웹후크 URL을 통해 내구성 있는 함수와 통신할 수 있습니다. 웹후크 URL은 [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client)사용하여 프로그래밍 방식으로 액세스할 수 있습니다. `CreateHttpManagementPayload` (.NET) 또는 `createHttpManagementPayload` (자바 스크립트) 메서드는 이러한 웹 후크 URL을 포함 하는 직렬화 가능한 개체를 얻을 수 있습니다.

`CreateHttpManagementPayload` (.NET) 및 `createHttpManagementPayload` (자바 스크립트) 메서드에는 하나의 매개 변수가 있습니다.

* **instanceId**: 인스턴스의 고유 ID입니다.

메서드는 다음 문자열 속성을 사용 하 고 개체를 반환 합니다.

* **Id**: 오케스트레이션의 인스턴스 ID입니다(`InstanceId` 입력과 동일해야 함).
* **StatusQueryGetUri**: 오케스트레이션 인스턴스의 상태 URL입니다.
* **SendEventPostUri**: 오케스트레이션 인스턴스의 "이벤트 발생" URL입니다.
* **TerminatePostUri**: 오케스트레이션 인스턴스의 "종료" URL입니다.
* **퍼지역사삭제**: 오케스트레이션 인스턴스의 "지우기 기록" URL입니다.

함수는 다음 예제와 같이 이러한 개체의 인스턴스를 외부 시스템으로 보내 해당 오케스트레이션의 이벤트를 모니터링하거나 발생시킬 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 `DurableActivityContext` 함수 1.x의 `IDurableActivityContext`경우 은 대신 속성을 사용해야 `OrchestrationClient` `DurableClient` `DurableOrchestrationClient` `IDurableOrchestrationClient`하며. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

## <a name="rewind-instances-preview"></a>인스턴스 되감기(미리 보기)

예기치 않은 이유로 오케스트레이션 오류가 있는 경우 해당 목적을 위해 빌드된 API를 사용하여 인스턴스를 이전 정상 상태로 *되감기할* 수 있습니다.

> [!NOTE]
> 이 API로 적절한 오류 처리 및 재시도 정책을 대체할 수 없습니다. 예기치 않은 이유로 오케스트레이션 인스턴스가 실패하는 경우에만 사용할 수 있습니다. 오류 처리 및 다시 시도 정책에 대한 자세한 내용은 [오류 처리](durable-functions-error-handling.md) 문서를 참조하십시오.

`RewindAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client) (.NET) 또는 `rewind` (JavaScript) 메서드를 사용하여 오케스트레이션을 *실행 상태로* 다시 넣습니다. 또한 이 메서드는 오케스트레이션 실패를 일으킨 활동 또는 하위 오케스트레이션 실행 실패를 다시 실행합니다.

예를 들어 일련의 [사용자 승인과](durable-functions-overview.md#human)관련된 워크플로가 있다고 가정해 보겠습니다. 승인이 필요하다고 사용자에게 알리고 실시간 응답을 기다리는 일련의 활동 함수가 있다고 가정합니다. 모든 승인 활동이 응답을 받거나 시간 초과된 후 잘못된 데이터베이스 연결 문자열과 같은 응용 프로그램 잘못된 구성으로 인해 다른 활동이 실패한다고 가정합니다. 결과적으로 워크플로에 대한 오케스트레이션 실패가 발생합니다. (.NET) 또는 `RewindAsync` `rewind` (JavaScript) API를 사용하면 응용 프로그램 관리자는 구성 오류를 수정하고 실패한 오케스트레이션을 오류 직전 상태로 되돌릴 수 있습니다. 인간 상호 작용 단계를 다시 승인할 필요가 없으며 이제 오케스트레이션을 성공적으로 완료할 수 있습니다.

> [!NOTE]
> *되감기* 기능은 지속형 타이머를 사용하는 오케스트레이션 인스턴스되감기를 지원하지 않습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable rewind` 명령을 사용하여 오케스트레이션 인스턴스를 직접 되감을 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* (필수) : 오케스트레이션 인스턴스의 ID입니다. ** `id` **
* (선택 사항) : 오케스트레이션 인스턴스를 되감는 이유입니다. ** `reason` **
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본적으로 [host.json](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>인스턴스 기록 지우기

오케스트레이션과 관련된 모든 데이터를 제거하려면 인스턴스 기록을 제거할 수 있습니다. 예를 들어 완료된 인스턴스와 연결된 Azure Table 행 및 큰 메시지 Blob을 삭제할 수 있습니다. 이렇게 하려면 `PurgeInstanceHistoryAsync` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client)(.NET) 또는 `purgeInstanceHistory` (JavaScript) 메서드를 사용 합니다.

이 메서드에는 두 개의 오버로드가 있습니다. 첫 번째 오버로드는 오케스트레이션 인스턴스의 ID에 의해 기록을 제거합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

function.json 구성에 대한 [인스턴스 시작을](#javascript-function-json) 참조하십시오.

---

다음 예제에서는 지정된 시간 간격 이후에 완료된 모든 오케스트레이션 인스턴스의 기록을 지우는 타이머 트리거 함수를 보여 주며, 이 함수는 타이머 트리거함수입니다. 이 경우 30일 이상 전에 완료된 모든 인스턴스에 대한 데이터가 제거됩니다. 오전 12시에 하루에 한 번 실행될 예정입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 이전 C# 코드는 내구성 함수 2.x용입니다. 지속 함수 1.x의 경우 `OrchestrationClient` 특성 대신 `DurableClient` 특성을 사용해야 `IDurableOrchestrationClient`하며. `DurableOrchestrationClient` 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

메서드는 `purgeInstanceHistoryBy` 여러 인스턴스에 대한 인스턴스 기록을 조건부로 제거하는 데 사용할 수 있습니다.

**함수.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
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

> [!NOTE]
> 이 예제는 내성 함수 버전 2.x를 대상으로 합니다. 버전 1.x에서는 `orchestrationClient` `durableClient`을 대신 사용합니다.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> 제거 기록 작업이 성공하려면 대상 인스턴스의 런타임 상태가 **완료,** **종료**또는 **실패해야**합니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable purge-history` 명령을 사용하여 오케스트레이션 인스턴스의 기록을 제거할 수 있습니다. 이전 섹션의 두 번째 C# 예제와 마찬가지로 지정된 시간 간격 동안 생성된 모든 오케스트레이션 인스턴스에 대한 기록을 제거합니다. 런타임 상태별로 제거된 인스턴스를 추가로 필터링할 수 있습니다. 명령에는 다음과 같은 매개 변수가 있습니다.

* (선택 사항) : 이 날짜/시간(UTC) 이후에 생성된 인스턴스의 기록을 제거합니다. ** `created-after` ** ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* (선택 사항) : 이 날짜/시간(UTC) 이전에 생성된 인스턴스의 기록을 제거합니다. ** `created-before` ** ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* (선택 사항) : 특정 상태(예: 실행 또는 완료됨)로 인스턴스 기록을 제거합니다. ** `runtime-status` ** 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본적으로 [host.json](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

다음 명령은 2018년 11월 14일 오후 7:35(UTC) 이전에 생성된 모든 실패한 인스턴스의 기록을 삭제합니다.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>작업 허브 삭제

Azure [Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` 명령을 사용하여 Azure 저장소 테이블, 큐 및 Blob을 포함하여 특정 작업 허브와 연결된 모든 저장소 아티팩트를 삭제할 수 있습니다. 명령에는 다음 두 개의 매개 변수가 있습니다.

* (선택 사항) : 사용할 저장소 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. ** `connection-string-setting` ** 기본값은 `AzureWebJobsStorage`입니다.
* (선택 사항) : 사용할 지속 기능 작업 허브의 이름입니다. ** `task-hub-name` ** 기본적으로 [host.json](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

다음 명령은 `UserTest` 작업 허브와 연결된 모든 Azure 저장소 데이터를 삭제합니다.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [인스턴스 관리를 위한 기본 제공 HTTP API 참조](durable-functions-http-api.md)
