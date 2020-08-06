---
title: 지속성 함수의 인스턴스 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 인스턴스를 관리하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 2b99d032b953caecfca2b34d5eadafe94f45f307
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809377"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure에서 Durable Functions의 인스턴스 관리

Azure Functions에 대 한 [Durable Functions](durable-functions-overview.md) 확장을 사용 하 고 있거나 작업을 시작 하려는 경우 해당 확장을 사용 하는 것이 좋습니다. Durable Functions 오케스트레이션 인스턴스를 관리 하는 방법에 대해 자세히 알아보는 방법으로 최적화할 수 있습니다. 이 문서에서는 각 인스턴스 관리 작업에 대해 자세히 설명합니다.

예를 들어 인스턴스를 시작 및 종료 하 고 필터를 사용 하 여 모든 인스턴스 및 쿼리 인스턴스를 쿼리 하는 기능을 포함 하 여 인스턴스를 쿼리할 수 있습니다. 또한 이벤트를 인스턴스로 보내고, 오케스트레이션이 완료 될 때까지 대기 하 고, HTTP management webhook Url을 검색할 수 있습니다. 이 문서에서는 인스턴스 되감기, 인스턴스 기록 제거 및 작업 허브 삭제를 포함 하 여 다른 관리 작업에 대해서도 설명 합니다.

Durable Functions에는 이러한 각 관리 작업을 구현 하는 방법에 대 한 옵션이 있습니다. 이 문서에서는 .NET (c #), JavaScript 및 Python에 대 한 [Azure Functions Core Tools](../functions-run-local.md) 를 사용 하는 예제를 제공 합니다.

## <a name="start-instances"></a>시작 인스턴스

오케스트레이션 인스턴스를 시작할 수 있는 것이 중요 합니다. 이는 다른 함수의 트리거에서 Durable Functions 바인딩을 사용 하는 경우에 일반적으로 수행 됩니다.

`StartNewAsync`오케스트레이션 클라이언트 바인딩의 (.net), `startNew` (JavaScript) 또는 `start_new` (Python) 메서드는 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 새 인스턴스를 시작 합니다. 내부적으로이 메서드는 메시지를 제어 큐에 큐 [오케스트레이션 트리거 바인딩을](durable-functions-bindings.md#orchestration-trigger)사용 하는 지정 된 이름을 사용 하 여 함수의 시작을 트리거합니다.

오케스트레이션 프로세스가 성공적으로 예약되면 이 비동기 작업이 완료됩니다.

새 오케스트레이션 인스턴스를 시작 하는 데 사용할 수 있는 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **Input**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 이 매개 변수를 지정 하지 않으면 메서드는 임의의 ID를 사용 합니다.

> [!TIP]
> 인스턴스 ID에 임의의 식별자를 사용하세요. 임의 인스턴스 Id는 여러 Vm에서 오 케 스트레이 터 함수를 확장할 때 동일한 부하 분산을 보장 하는 데 도움이 됩니다. 해당 ID를 외부 원본에서 가져와야 하거나 [singleton orchestrator](durable-functions-singletons.md) 패턴을 구현 하는 경우에는 임의의 인스턴스 id를 적절 하 게 사용할 수 있습니다.

다음 코드는 새 오케스트레이션 인스턴스를 시작 하는 예제 함수입니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>별도로 지정 하지 않는 한이 페이지의 예제에서는에서 다음 function.jsHTTP 트리거를 사용 합니다.

**function.json**

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
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 대신을 사용 `orchestrationClient` `durableClient` 합니다.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>별도로 지정 하지 않는 한이 페이지의 예제에서는에서 다음 function.jsHTTP 트리거를 사용 합니다.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
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
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 대신을 사용 `orchestrationClient` `durableClient` 합니다.

**__init__py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[ AzureFunctionsCoreTools](../functions-run-local.md) `durable start-new`명령을 사용하여 직접 인스턴스를 시작할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `function-name` (필수)**: 시작할 함수의 이름입니다.
* ** `input` (선택 사항)**: 함수에 대 한 입력 (인라인 또는 JSON 파일)입니다. 파일의 경우와 같이 파일의 경로에 접두사를 추가 `@` `@path/to/file.json` 합니다.
* ** `id` (선택 사항)**: 오케스트레이션 인스턴스의 ID입니다. 이 매개 변수를 지정 하지 않으면 명령에서 임의 GUID를 사용 합니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.js](durable-functions-bindings.md#host-json) 에서이를 설정할 수도 있습니다.

> [!NOTE]
> 핵심 도구 명령은 함수 앱의 루트 디렉터리에서 실행 하는 것으로 가정 합니다. 및 매개 변수를 명시적으로 제공 하는 경우 `connection-string-setting` `task-hub-name` 모든 디렉터리에서 명령을 실행할 수 있습니다. 함수 앱 호스트를 실행 하지 않고 이러한 명령을 실행할 수 있지만 호스트가 실행 되 고 있지 않으면 일부 효과를 확인할 수 없을 수 있습니다. 예를 들어 `start-new` 명령은 시작 메시지를 대상 작업 허브에 큐, 메시지를 처리할 수 있는 함수 앱 호스트 프로세스가 실행 되 고 있지 않으면 오케스트레이션이 실제로 실행 되지 않습니다.

다음 명령은 HelloWorld 라는 함수를 시작 하 고 파일의 콘텐츠를 전달 합니다 `counter-data.json` .

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>쿼리 인스턴스

오케스트레이션을 관리 하는 작업의 일환으로 오케스트레이션 인스턴스 상태에 대 한 정보 (예: 정상적으로 완료 되었는지 아니면 실패 했는지)를 수집 해야 하는 경우가 많습니다.

`GetStatusAsync`오케스트레이션 클라이언트 바인딩의 (.net), `getStatus` (JavaScript) 또는 `get_status` (Python) 메서드는 오케스트레이션 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 인스턴스의 상태를 쿼리 합니다.

`instanceId`(필수), `showHistory`(선택 사항), `showHistoryOutput`(선택 사항) 및 `showInput`(선택 사항)을 매개 변수로 사용합니다.

* **`showHistory`**:로 설정 하면 `true` 응답에 실행 기록이 포함 됩니다.
* **`showHistoryOutput`**:로 설정 하면 `true` 실행 기록에 활동 출력이 포함 됩니다.
* **`showInput`**:로 설정 된 경우 `false` 응답은 함수 입력을 포함 하지 않습니다. 기본값은 `true`입니다.

메서드는 다음과 같은 속성이 있는 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **Input**: JSON 값의 함수 입력입니다. 이 false 인 경우이 필드는 채워지지 않습니다 `showInput` .
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다.
* **Output**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오 케 스트레이 터 함수가 실패 한 경우이 속성에는 오류 세부 정보가 포함 됩니다. 오 케 스트레이 터 함수가 종료 되 면이 속성에 종료 이유가 포함 됩니다 (있는 경우).
* **RuntimeStatus**: 다음 값 중 하나입니다.
  * **보류 중**: 인스턴스는 일정이 있지만 아직 실행을 시작하지 않았습니다.
  * **실행 중**: 인스턴스가 실행되기 시작했습니다.
  * **완료됨**: 인스턴스가 정상적으로 완료되었습니다.
  * **ContinuedAsNew(새 기록으로 계속됨)**: 인스턴스가 새 기록으로 다시 시작되었습니다. 이 상태는 일시적인 상태입니다.
  * **실패**: 인스턴스가 오류로 인해 실패했습니다.
  * **종료됨**: 인스턴스가 갑자기 중지되었습니다.
* **기록**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.

이 메서드 `null` `undefined` 는 인스턴스가 존재 하지 않는 경우 (.net), (JavaScript) 또는 `None` (Python)를 반환 합니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable get-runtime-status` 명령을 사용하여 오케스트레이션 인스턴스의 상태를 직접 가져올 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `id` (필수)**: 오케스트레이션 인스턴스의 ID입니다.
* ** `show-input` (선택 사항)**:로 설정 된 경우 `true` 응답에는 함수에 대 한 입력이 포함 됩니다. 기본값은 `false`입니다.
* ** `show-output` (선택 사항)**:로 설정 하면 `true` 응답에 함수의 출력이 포함 됩니다. 기본값은 `false`입니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령을 사용 하 여 오케스트레이션 인스턴스 ID가 0ab8c55a66644d68a3a8b220b12d209c 인 인스턴스의 상태 (입력 및 출력 포함)를 검색 합니다. `func`함수 앱의 루트 디렉터리에서 명령을 실행 하는 것으로 가정 합니다.

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

명령을 사용 하 여 `durable get-history` 오케스트레이션 인스턴스의 기록을 검색할 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `id` (필수)**: 오케스트레이션 인스턴스의 ID입니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 host.json에서 설정할 수도 있습니다.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>모든 인스턴스 쿼리

오케스트레이션에 서 한 번에 하나의 인스턴스를 쿼리 하는 대신 모든 항목을 한 번에 쿼리 하는 것이 더 효율적일 수 있습니다.

[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net), [getstatusall](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusall--) (JavaScript) 또는 `get_status_all` (Python) 메서드를 사용 하 여 모든 오케스트레이션 인스턴스의 상태를 쿼리할 수 있습니다. .NET에서는 `CancellationToken` 개체를 취소 하려는 경우에 개체를 전달할 수 있습니다. 메서드는 쿼리 매개 변수와 일치 하는 오케스트레이션 인스턴스를 나타내는 개체 목록을 반환 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable get-instances` 명령을 사용하여 인스턴스를 직접 쿼리할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `top` (선택 사항)**:이 명령은 페이징을 지원 합니다. 이 매개 변수는 요청당 검색된 인스턴스의 수에 해당합니다. 기본값은 10입니다.
* ** `continuation-token` (선택 사항)**: 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>필터를 사용 하 여 인스턴스 쿼리

표준 인스턴스 쿼리에서 제공할 수 있는 모든 정보가 필요 하지 않은 경우 어떻게 되나요? 예를 들어 오케스트레이션 만든 시간 또는 오케스트레이션 런타임 상태를 검색 하는 경우는 어떻게 되나요? 필터를 적용 하 여 쿼리 범위를 좁힐 수 있습니다.

[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net) 또는 [getstatusby](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) 메서드를 사용 하 여 미리 정의 된 필터 집합과 일치 하는 오케스트레이션 인스턴스 목록을 가져옵니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

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

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Azure Functions Core Tools에서 `durable get-instances` 명령을 필터와 함께 사용할 수도 있습니다. 앞의,, 및 `top` 매개 변수 외에도 `continuation-token` `connection-string-setting` `task-hub-name` 세 개의 필터 매개 변수 ( `created-after` , 및)를 사용할 수 있습니다 `created-before` `runtime-status` .

* ** `created-after` (선택 사항)**:이 날짜/시간 (UTC) 이후에 생성 된 인스턴스를 검색 합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* ** `created-before` (선택 사항)**:이 날짜/시간 (UTC) 이전에 만든 인스턴스를 검색 합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* ** `runtime-status` (선택 사항)**: 특정 상태 (예: 실행 중 또는 완료 됨)를 사용 하 여 인스턴스를 검색 합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* ** `top` (선택 사항)**: 요청당 검색 된 인스턴스 수입니다. 기본값은 10입니다.
* ** `continuation-token` (선택 사항)**: 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

필터 (, 또는)를 제공 하지 않는 경우 `created-after` `created-before` `runtime-status` 명령은 `top` 런타임 상태나 생성 시간에 관계 없이 단순히 인스턴스를 검색 합니다.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>인스턴스 종료

오케스트레이션 인스턴스를 실행 하는 데 시간이 너무 오래 걸리거나 어떤 이유로 든 완료 되기 전에 중지 해야 하는 경우에는 종료 하는 옵션이 있습니다.

`TerminateAsync`오케스트레이션 클라이언트 바인딩의 (.net), `terminate` (JavaScript) 또는 `terminate` (Python) 메서드를 사용 하 여 인스턴스 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 를 종료할 수 있습니다. 두 매개 변수는 `instanceId` `reason` 로그 및 인스턴스 상태에 기록 되는 및 문자열입니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

종료 된 인스턴스는 결국 상태로 전환 됩니다 `Terminated` . 그러나이 전환은 즉시 발생 하지 않습니다. 대신 종료 작업은 해당 인스턴스에 대 한 다른 작업과 함께 작업 허브에서 큐에 대기 됩니다. [인스턴스 쿼리](#query-instances) api를 사용 하 여 종료 된 인스턴스가 실제로 상태에 도달 했을 때를 알 수 있습니다 `Terminated` .

> [!NOTE]
> 인스턴스 종료가 현재 전파 되지 않습니다. 작업 함수 및 하위 오케스트레이션은이를 호출한 오케스트레이션 인스턴스를 종료 했는지 여부에 관계 없이 완료 될 때까지 실행 됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[ AzureFunctionsCoreTools](../functions-run-local.md) `durable terminate` 명령을 사용하여 오케스트레이션 인스턴스를 직접 종료할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `id` (필수)**: 종료할 오케스트레이션 인스턴스의 ID입니다.
* ** `reason` (선택 사항)**: 종료 이유입니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령은 ID가 0ab8c55a66644d68a3a8b220b12d209c 인 오케스트레이션 인스턴스를 종료 합니다.

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>인스턴스에 이벤트 보내기

일부 시나리오에서는 오 케 스트레이 터 함수가 외부 이벤트를 대기 하 고 수신할 수 있는 것이 중요 합니다. 여기에는 [사용자 상호 작용](durable-functions-overview.md#human)을 기다리는 [모니터 함수](durable-functions-overview.md#monitoring) 및 함수가 포함 됩니다.

`RaiseEventAsync` `raiseEvent` [오케스트레이션 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client)(.net) 메서드 또는 (JavaScript) 메서드를 사용 하 여 실행 중인 인스턴스에 이벤트 알림을 보냅니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 (.Net)에 대 한 호출을 대기 `WaitForExternalEvent` 하거나 `waitForExternalEvent` (JavaScript) 호출로 생성 되는 인스턴스입니다.

`RaiseEventAsync`(.Net) 및 (JavaScript)에 대 한 매개 변수는 다음과 같습니다 `raiseEvent` .

* **InstanceId**: 인스턴스의 고유 ID입니다.
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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> 지정된 인스턴스 ID가 있는 오케스트레이션 인스턴스가 없으면 이벤트 메시지가 삭제됩니다. 인스턴스가 있지만 이벤트를 아직 기다리지 않는 경우 이벤트를 수신 하 고 처리할 준비가 될 때까지 인스턴스 상태에 저장 됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable raise-event` 명령을 사용하여 오케스트레이션 인스턴스에 직접 이벤트를 발생 시킬 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `id` (필수)**: 오케스트레이션 인스턴스의 ID입니다.
* **`event-name`**: 발생 시킬 이벤트의 이름입니다.
* ** `event-data` (선택 사항)**: 오케스트레이션 인스턴스로 보낼 데이터입니다. JSON 파일의 경로 이거나 명령줄에서 직접 데이터를 제공할 수 있습니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. Microsoft.azure.webjobs.extensions.durabletask: HubName를 사용 하 여 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

장기 실행 오케스트레이션에 서 오케스트레이션의 결과를 대기 하 고 가져올 수 있습니다. 이 경우 오케스트레이션에 서 제한 시간을 정의 하는 것도 유용 합니다. 제한 시간을 초과 하는 경우 오케스트레이션의 상태가 결과 대신 반환 되어야 합니다.

`WaitForCompletionOrCreateCheckStatusResponseAsync`(.Net) 또는 `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) 메서드를 사용 하 여 오케스트레이션 인스턴스에서 실제 출력을 동기적으로 가져올 수 있습니다. 기본적으로 이러한 메서드는에 대해 기본값인 10 초, `timeout` 에 1 초를 사용 `retryInterval` 합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

다음 줄을 사용 하 여 함수를 호출 합니다. 재시도 간격으로 시간 제한 및 0.5 초에 2 초를 사용 합니다.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

* 오케스트레이션 인스턴스는 정의 된 제한 시간 (이 경우 2 초) 내에 완료 되 고 응답은 동기식으로 배달 되는 실제 오케스트레이션 인스턴스 출력입니다.

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

* 오케스트레이션 인스턴스는 정의 된 시간 제한 내에 완료할 수 없으며, 응답은 [HTTP API URL 검색](durable-functions-http-api.md)에 설명 된 기본 응답입니다.

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
> 웹 후크 Url의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 다를 수 있습니다. 앞의 예제는 Azure Functions 2.0 호스트에 대한 것입니다.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP 관리 webhook Url 검색

외부 시스템을 사용 하 여 이벤트를 모니터링 하거나 오케스트레이션에 서 이벤트를 발생 시킬 수 있습니다. 외부 시스템은 [HTTP API url 검색](durable-functions-http-features.md#http-api-url-discovery)에 설명 된 기본 응답의 일부인 웹 후크 url을 통해 Durable Functions와 통신할 수 있습니다. [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client)사용 하 여 프로그래밍 방식으로 webhook url에 액세스할 수도 있습니다. `CreateHttpManagementPayload`(.Net) 또는 `createHttpManagementPayload` (JavaScript) 메서드를 사용 하 여 이러한 webhook url이 포함 된 serializable 개체를 가져올 수 있습니다.

`CreateHttpManagementPayload`(.Net) 및 `createHttpManagementPayload` (JavaScript) 메서드에는 매개 변수가 하나 있습니다.

* **instanceId**: 인스턴스의 고유 ID입니다.

메서드는 다음과 같은 문자열 속성을 포함 하는 개체를 반환 합니다.

* **Id**: 오케스트레이션의 인스턴스 ID입니다(`InstanceId` 입력과 동일해야 함).
* **StatusQueryGetUri**: 오케스트레이션 인스턴스의 상태 URL입니다.
* **SendEventPostUri**: 오케스트레이션 인스턴스의 "이벤트 발생" URL입니다.
* **TerminatePostUri**: 오케스트레이션 인스턴스의 "종료" URL입니다.
* **PurgeHistoryDeleteUri**: 오케스트레이션 인스턴스의 "제거 기록" URL입니다.

다음 예제와 같이 함수는 이러한 개체의 인스턴스를 외부 시스템으로 보내 해당 오케스트레이션에 대 한 이벤트를 모니터링 하거나 발생 시킬 수 있습니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 해야 `DurableActivityContext` `IDurableActivityContext` `OrchestrationClient` 하며 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 `IDurableOrchestrationClient` 해야 합니다. 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

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

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>인스턴스 되감기 (미리 보기)

예기치 않은 이유로 인해 오케스트레이션 오류가 발생 한 경우 해당 목적으로 작성 된 API를 사용 하 여 인스턴스를 이전에 정상 상태로 *되감을* 수 있습니다.

> [!NOTE]
> 이 API로 적절한 오류 처리 및 재시도 정책을 대체할 수 없습니다. 예기치 않은 이유로 오케스트레이션 인스턴스가 실패하는 경우에만 사용할 수 있습니다. 오류 처리 및 다시 시도 정책에 대 한 자세한 내용은 [오류 처리](durable-functions-error-handling.md) 문서를 참조 하세요.

오케스트레이션 `RewindAsync` 클라이언트 바인딩의 (.net) 또는 `rewind` (JavaScript) 메서드를 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 사용 하 여 오케스트레이션을 다시 *실행 중* 상태로 전환 합니다. 또한이 메서드는 오케스트레이션 실패를 일으킨 작업 또는 하위 오케스트레이션 실행 오류를 다시 실행 합니다.

예를 들어 일련의 [인적 승인을](durable-functions-overview.md#human)포함 하는 워크플로가 있다고 가정해 보겠습니다. 사용자에 게 승인이 필요 함을 알리는 일련의 작업 함수가 있다고 가정 하 고 실시간 응답을 기다립니다. 모든 승인 작업에서 응답을 받았거나 시간이 초과 되 면 잘못 된 데이터베이스 연결 문자열과 같은 응용 프로그램의 잘못 된 구성으로 인해 다른 작업이 실패 하는 것으로 가정 합니다. 결과적으로 워크플로에 대한 오케스트레이션 실패가 발생합니다. `RewindAsync`(.Net) 또는 `rewind` (JAVASCRIPT) API를 사용 하 여 응용 프로그램 관리자는 구성 오류를 해결 하 고 실패 한 오케스트레이션을 오류 직전의 상태로 다시 되감을 수 있습니다. 사용자 상호 작용 단계를 다시 승인 하지 않아도 되 고 오케스트레이션이 성공적으로 완료 될 수 있습니다.

> [!NOTE]
> *되감기* 기능은 내구성이 있는 타이머를 사용 하는 되감기 오케스트레이션 인스턴스를 지원 하지 않습니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> 이 기능은 현재 Python에서 지원 되지 않습니다.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable rewind` 명령을 사용하여 오케스트레이션 인스턴스를 직접 되감을 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* ** `id` (필수)**: 오케스트레이션 인스턴스의 ID입니다.
* ** `reason` (선택 사항)**: 오케스트레이션 인스턴스를 되감기 하는 이유입니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용 됩니다.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>인스턴스 기록 제거

오케스트레이션과 연결 된 모든 데이터를 제거 하려면 인스턴스 기록을 제거 하면 됩니다. 예를 들어 완료 된 인스턴스와 연결 된 모든 Azure Table 행 및 large message blob을 삭제할 수 있습니다. 이렇게 하려면 `PurgeInstanceHistoryAsync` `purgeInstanceHistory` [orchestration 클라이언트 바인딩의](durable-functions-bindings.md#orchestration-client)(.Net) 또는 (JavaScript) 메서드를 사용 합니다.

이 메서드에는 두 개의 오버 로드가 있습니다. 첫 번째 오버 로드는 오케스트레이션 인스턴스의 ID로 기록을 제거 합니다.

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

구성에 대 한 function.js[시작 인스턴스](#javascript-function-json) 를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

다음 예에서는 지정 된 시간 간격 후 완료 된 모든 오케스트레이션 인스턴스에 대 한 기록을 제거 하는 타이머 트리거 함수를 보여 줍니다. 이 경우 30 일 전에 완료 된 모든 인스턴스에 대 한 데이터를 제거 합니다. 하루에 한 번, 오전 12 시에 실행 되도록 예약 되어 있습니다.

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
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy`메서드를 사용 하 여 여러 인스턴스에 대 한 인스턴스 기록을 조건부로 제거할 수 있습니다.

**function.json**

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
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 대신을 사용 `orchestrationClient` `durableClient` 합니다.

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.datetime()
    created_time_to = datetime.datetime.today + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> 제거 기록 작업이 성공 하려면 대상 인스턴스의 런타임 상태를 **완료**하거나 **종료**하거나 **실패**해야 합니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[AzureFunctionsCoreTools](../functions-run-local.md) `durable purge-history` 명령을 사용하여 오케스트레이션 인스턴스의 기록을 제거할 수 있습니다. 이전 섹션에서 두 번째 c # 예제와 마찬가지로 지정 된 시간 간격 동안 생성 된 모든 오케스트레이션 인스턴스에 대 한 기록을 제거 합니다. 런타임 상태별로 제거 된 인스턴스를 추가로 필터링 할 수 있습니다. 명령에는 다음과 같은 매개 변수가 있습니다.

* ** `created-after` (선택 사항)**:이 날짜/시간 (UTC) 이후에 생성 된 인스턴스의 기록을 제거 합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* ** `created-before` (선택 사항)**:이 날짜/시간 (UTC) 이전에 만든 인스턴스의 기록을 제거 합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* ** `runtime-status` (선택 사항)**: 특정 상태 (예: 실행 중 또는 완료 됨)의 인스턴스 기록을 제거 합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용 됩니다.

다음 명령은 11 월 14 일 (UTC) 7:35에 2018 년 11 월 14 일 이전에 생성 된 모든 실패 한 인스턴스의 기록을 삭제 합니다.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>작업 허브 삭제

[Azure Functions Core Tools](../functions-run-local.md) 명령을 사용 하 여 `durable delete-task-hub` Azure storage 테이블, 큐 및 blob을 비롯 하 여 특정 작업 허브와 연결 된 모든 저장소 아티팩트를 삭제할 수 있습니다. 명령에는 다음 두 개의 매개 변수가 있습니다.

* ** `connection-string-setting` (선택 사항)**: 사용할 저장소 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* ** `task-hub-name` (선택 사항)**: 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용 됩니다.

다음 명령을 사용 하 여 작업 허브와 연결 된 모든 Azure storage 데이터를 삭제 합니다 `UserTest` .

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [인스턴스 관리에 대 한 기본 제공 HTTP API 참조](durable-functions-http-api.md)