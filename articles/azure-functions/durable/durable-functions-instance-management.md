---
title: 지속성 함수의 인스턴스 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 인스턴스를 관리하는 방법을 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 19abd905bbc5e8ab724f7a2f25cb6da90db82841
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262645"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure에서 Durable Functions의 인스턴스 관리

[지속성 함수](durable-functions-overview.md) 오케스트레이션 인스턴스는 알림 이벤트를 시작, 종료, 쿼리 및 전송할 수 있습니다. 모든 인스턴스 관리는 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md)을 사용하여 수행됩니다. 이 문서에서는 각 인스턴스 관리 작업에 대해 자세히 설명합니다.

## <a name="starting-instances"></a>인스턴스 시작

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)(.NET)의 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 메서드 또는 `DurableOrchestrationClient`(JavaScript)의 `startNew`는 오케스트레이터 함수의 새 인스턴스를 시작합니다. 이 클래스의 인스턴스는 `orchestrationClient` 바인딩을 사용하여 얻을 수 있습니다. 내부적으로 이 메서드는 메시지를 제어 큐에 넣고 `orchestrationTrigger` 트리거 바인딩을 사용하는 지정된 이름의 함수 시작을 트리거합니다.

오케스트레이션 프로세스가 성공적으로 예약되면 이 비동기 작업이 완료됩니다. 오케스트레이션 프로세스는 30초 이내에 시작해야 합니다. 더 오래 걸리는 경우 `TimeoutException`이 throw됩니다.

> [!WARNING]
> JavaScript에서 로컬로 개발하는 경우 환경 변수 `WEBSITE_HOSTNAME`을 `localhost:<port>`(예: `localhost:7071`)로 설정해야 `DurableOrchestrationClient`에서 메서드를 사용할 수 있습니다. 이 요구 사항에 대한 자세한 내용은 [GitHub 문제](https://github.com/Azure/azure-functions-durable-js/issues/28)를 참조하세요.

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)에 대한 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **입력**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 지정하지 않으면 임의의 인스턴스 ID가 생성됩니다.

다음은 간단한 C# 예제입니다.

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

`startNew`에 대한 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 지정하지 않으면 임의의 인스턴스 ID가 생성됩니다.
* **입력**: (선택 사항) 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.

다음은 간단한 JavaScript 예제입니다.

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> 인스턴스 ID에 임의의 식별자를 사용하세요. 이렇게 하면 여러 VM에서 오케스트레이터 함수를 크기 조정할 때 균등한 부하 분산을 보장하는 데 도움이 됩니다. D가 외부 원본에서 제공되어야 하거나 [단일 항목 오케스트레이터](durable-functions-singletons.md) 패턴을 구현하는 경우에는 임의가 아닌 인스턴스 ID를 사용하는 것이 좋습니다.

### <a name="using-core-tools"></a>Core Tools 사용

[Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 명령을 통해 인스턴스를 직접 시작할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`function-name`(필수)**: 시작할 함수의 이름입니다.
* **`input`(선택 사항)**: 인라인 또는 JSON 파일을 통한 함수 입력입니다. 파일의 경우 `@`을 파일 경로에 앞에 추가합니다(예: `@path/to/file.json`).
* **`id`(선택 사항)**: 오케스트레이션 인스턴스의 ID입니다. 제공하지 않는 경우 임의 GUID가 생성됩니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 함수 앱의 루트 디렉터리에서 실행되는 것으로 가정합니다. `connection-string-setting` 및 `task-hub-name`이 명시적으로 제공되면 모든 디렉터리에서 명령을 실행할 수 있습니다. 함수 앱 호스트가 실행되지 않은 상태에서 이 명령을 실행할 수 있지만, 호스트가 실행되지 않는 경우 일부 효과가 관찰되지 않을 수 있습니다. 예를 들어, `start-new` 명령은 시작 메시지를 대상 작업 허브의 큐에 넣지만 메시지를 처리할 수 있는 함수 앱 호스트 프로세스가 실행 중이지 않은 경우에는 오케스트레이션이 실제로 실행되지 않습니다.

다음 명령은 HelloWorld라는 함수를 시작하고 ‘counter-data.json’ 파일의 콘텐츠를 이 함수에 전달합니다.

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>인스턴스 쿼리

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스(.NET)의 [ GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 메서드 또는 `DurableOrchestrationClient` 클래스(JavaScript)의 `getStatus` 메서드는 오케스트레이션 인스턴스의 상태를 쿼리합니다.

`instanceId`(필수), `showHistory`(선택 사항), `showHistoryOutput`(선택 사항) 및 `showInput`(선택 사항, .NET만 해당)을 매개 변수로 사용합니다.

* **`showHistory`**: `true`로 설정하는 경우 응답에는 실행 기록이 포함됩니다.
* **`showHistoryOutput`**: `true`로 설정하는 경우 실행 기록에 작업 출력이 포함됩니다.
* **`showInput`**: `false`로 설정하는 경우 응답에 함수의 입력이 포함되지 않습니다. 기본값은 `true`입니다. (.NET만 해당)

메서드는 다음과 같은 속성이 있는 JSON 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **입력**: JSON 값의 함수 입력입니다. `showInput`이 false인 경우 이 필드는 채워지지 않습니다.
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다.
* **출력**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오케스트레이터 함수가 실패하면 이 속성에 오류 세부 정보가 포함됩니다. 오케스트레이터 함수가 종료되면 이 속성에 제공된 종료 이유가 포함됩니다(있는 경우).
* **RuntimeStatus**: 다음 값 중 하나입니다.
  * **Pending**: 인스턴스는 일정이 있지만 아직 실행을 시작하지 않았습니다.
  * **Running**: 인스턴스가 실행되기 시작했습니다.
  * **Completed**: 인스턴스가 정상적으로 완료되었습니다.
  * **ContinuedAsNew**: 인스턴스가 새 기록으로 다시 시작되었습니다. 일시적인 상태입니다.
  * **Failed**: 인스턴스가 오류로 인해 실패했습니다.
  * **Terminated**: 인스턴스가 갑자기 중지되었습니다.
* **History**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.

인스턴스가 존재하지 않거나 아직 실행을 시작하지 않은 경우 이 메서드는 `null`을 반환합니다.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Core Tools 사용

[Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` 명령을 통해 오케스트레이션 인스턴스의 상태를 직접 가져올 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)**: 오케스트레이션 인스턴스의 ID입니다.
* **`show-input`(선택 사항)**: `true`로 설정하는 경우 응답에 함수의 입력이 포함됩니다. 기본값은 `false`입니다.
* **`show-output`(선택 사항)**: `true`로 설정하는 경우 응답에 함수의 출력이 포함됩니다. 기본값은 `false`입니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령은 오케스트레이션 인스턴스 ID가 0ab8c55a66644d68a3a8b220b12d209c인 인스턴스의 상태(입력 및 출력 포함)를 검색합니다. 여기서는 `func` 명령이 함수 앱의 루트 디렉터리에서 실행 중인 것으로 가정합니다.

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` 명령을 사용하여 오케스트레이션 인스턴스의 기록을 검색할 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)**: 오케스트레이션 인스턴스의 ID입니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 host.json에서 설정할 수도 있습니다.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>모든 인스턴스 쿼리

`GetStatusAsync`(.NET) 또는 `getStatusAll`(JavaScript) 메서드를 사용하여 모든 오케스트레이션 인스턴스의 상태를 쿼리할 수 있습니다. .NET에서는 매개 변수를 취소하고 싶으면 `CancellationToken` 개체를 전달하면 됩니다. 이 메서드는 매개 변수가 있는 `GetStatusAsync` 메서드와 속성이 동일한 개체를 반환합니다.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

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

### <a name="using-core-tools"></a>Core Tools 사용

[Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` 명령을 통해 인스턴스를 직접 쿼리할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`top`(선택 사항)**: 이 명령은 페이징을 지원합니다. 이 매개 변수는 요청당 검색된 인스턴스의 수에 해당합니다. 기본값은 10입니다.
* **`continuation-token`(선택 사항)**: 검색할 인스턴스의 페이지/섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>필터로 인스턴스 쿼리

`GetStatusAsync`(.NET) 또는 `getStatusBy`(JavaScript) 메서드를 사용하여 미리 정의된 필터 세트와 일치하는 오케스트레이션 인스턴스 목록을 가져올 수도 있습니다. 가능한 필터 옵션에는 오케스트레이션 생성 시간과 오케스트레이션 런타임 상태가 포함됩니다.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

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

### <a name="using-the-functions-core-tools"></a>Functions Core Tools 사용

`durable get-instances` 명령은 필터와 함께 사용할 수도 있습니다. 앞에서 언급한 `top`, `continuation-token`, `connection-string-setting`, `task-hub-name` 매개 변수와 함께 세 개의 필터 매개 변수(`created-after`, `created-before`, `runtime-status`)를 사용할 수 있습니다.

* **`created-after`(선택 사항)**: 이 날짜/시간(UTC) 이후에 생성된 인스턴스를 검색합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`created-before`(선택 사항)**: 이 날짜/시간(UTC) 전에 생성된 인스턴스를 검색합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`runtime-status`(선택 사항)**: 이와 일치하는 상태(‘실행 중’, ‘완료됨’)의 인스턴스를 검색합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* **`top`(선택 사항)**: 요청당 검색된 인스턴스 수입니다. 기본값은 10입니다.
* **`continuation-token`(선택 사항)**: 검색할 인스턴스의 페이지/섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

필터(`created-after`, `created-before` 또는 `runtime-status`)가 제공되지 않은 경우 런타임 상태 또는 생성 시간과 관계없이 `top` 인스턴스가 검색됩니다.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스는 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스(.NET)의 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 메서드 또는 `DurableOrchestrationClient` 클래스(JavaScript)의 `terminate` 메서드를 사용하여 종료할 수 있습니다. 두 가지 매개 변수는 `instanceId` 및 `reason` 문자열이며, 로그 및 인스턴스 상태에 기록됩니다. 종료된 인스턴스는 다음 `await`(.NET) 또는 `yield`(JavaScript) 지점에 도달하는 즉시 실행을 중지하거나 이미 `await`(.NET) 또는 `yield`(JavaScript)에 있는 경우 즉시 종료됩니다.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> 현재 인스턴스 종료는 전파되지 않았습니다. 활동 함수 및 하위 오케스트레이션은 호출된 오케스트레이션 인스턴스가 종료되었는지 여부에 관계 없이 완료될 때까지 실행됩니다.

### <a name="using-core-tools"></a>Core Tools 사용

[Core Tools](../functions-run-local.md) `durable terminate` 명령을 통해 오케스트레이션 인스턴스를 직접 종료할 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)**: 종료할 오케스트레이션 인스턴스의 ID입니다.
* **`reason`(선택 사항)**: 종료 이유입니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령은 ID가 0ab8c55a66644d68a3a8b220b12d209c인 오케스트레이션 인스턴스를 종료합니다.

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>인스턴스로 이벤트 보내기

이벤트 알림은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스(.NET)의 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 메서드 또는 `DurableOrchestrationClient` 클래스(JavaScript)의 `raiseEvent` 메서드를 사용하여 실행 중인 인스턴스로 보낼 수 있습니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_)(.NET) 또는 `waitForExternalEvent`(JavaScript)에 대한 호출을 기다리는 인스턴스입니다.

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)(.NET) 및 `raiseEvent`(JavaScript)에 대한 매개 변수는 다음과 같습니다.

* **InstanceId**: 인스턴스의 고유 ID입니다.
* **EventName**: 보낼 이벤트의 이름입니다.
* **EventData**: 인스턴스에 보낼 JSON 직렬화 가능 페이로드입니다.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없거나 인스턴스에서 지정된 *이벤트 이름*을 기다리지 않는 경우 해당 이벤트 메시지는 버려집니다. 이 동작에 대한 자세한 내용은 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/29)를 참조하세요.

### <a name="using-core-tools"></a>Core Tools 사용

[Core Tools](../functions-run-local.md) `durable raise-event` 명령을 통해 오케스트레이션 인스턴스에 대한 이벤트를 직접 발생시킬 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)**: 오케스트레이션 인스턴스의 ID입니다.
* **`event-name`(선택 사항)**: 발생할 이벤트의 이름입니다. 기본값은 `$"Event_{RandomGUID}"`입니다.
* **`event-data`(선택 사항)**: 오케스트레이션 인스턴스로 전송할 데이터입니다. JSON 파일의 경로일 수도 있고 명령줄에서 직접 데이터를 제공할 수도 있습니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스는 오케스트레이션 인스턴스에서 실제 출력을 동기적으로 가져오는 데 사용할 수 있는 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API를 .NET에 공개합니다. JavaScript에서 `DurableOrchestrationClient` 클래스는 동일한 목적으로 `waitForCompletionOrCreateCheckStatusResponse` API를 공개합니다. 메서드가 설정되지 않은 경우 `timeout`에서 10초를 기본값으로 사용하고 `retryInterval`에서 1초를 기본값으로 사용합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

2초 제한 시간 및 0.5초 다시 시도 간격을 사용하여 다음 행에서 함수를 호출할 수 있습니다.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

* 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료됩니다. 응답은 동기적으로 배달된 실제 오케스트레이션 인스턴스 출력입니다.

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료될 수 없습니다. 응답은 **HTTP API URL 검색**에 설명된 기본값입니다.

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 앞의 예제는 Azure Functions 2.x 호스트에 대한 것입니다.

## <a name="retrieving-http-management-webhook-urls"></a>HTTP 관리 Webhook URL 검색

외부 시스템은 [HTTP API URL 검색](durable-functions-http-api.md)에 설명된 기본 응답의 일부인 webhook URL을 통해 지속형 함수와 통신할 수 있습니다. 하지만 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스(.NET)의 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 메서드 또는 `DurableOrchestrationClient` 클래스(JavaScript)의 `createHttpManagementPayload` 메서드를 통해 오케스트레이션 클라이언트에서 또는 작업 함수에서 프로그래밍 방식으로 웹후크 URL에 액세스할 수도 있습니다.

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 및 `createHttpManagementPayload`에는 하나의 매개 변수가 있습니다.

* **instanceId**: 인스턴스의 고유 ID입니다.

이 메서드는 다음 문자열 속성이 있는 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_)(.NET) 또는 개체(JavaScript)의 인스턴스를 반환합니다.

* **Id**: 오케스트레이션의 인스턴스 ID입니다(`InstanceId` 입력과 동일해야 함).
* **StatusQueryGetUri**: 오케스트레이션 인스턴스의 상태 URL입니다.
* **SendEventPostUri**: 오케스트레이션 인스턴스의 "이벤트 발생" URL입니다.
* **TerminatePostUri**: 오케스트레이션 인스턴스의 "종료" URL입니다.
* **RewindPostUri**: 오케스트레이션 인스턴스의 "rewind" URL입니다.

작업 함수는 외부 시스템에 이러한 개체의 인스턴스를 보내 오케스트레이션을 모니터링하거나 이벤트를 발생시킬 수 있습니다.

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

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

## <a name="rewinding-instances-preview"></a>되감기 인스턴스(미리 보기)

실패한 오케스트레이션 인스턴스는 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_)(.NET) 또는 `rewindAsync`(JavaScript) API를 사용하여 이전의 정상 상태로 ‘되감기’될 수 있습니다. 오케스트레이션을 *실행* 상태로 되돌리고 오케스트레이션 실패를 유발한 작업 및/또는 하위 오케스트레이션 실행 실패를 다시 실행하여 수행됩니다.

> [!NOTE]
> 이 API로 적절한 오류 처리 및 재시도 정책을 대체할 수 없습니다. 예기치 않은 이유로 오케스트레이션 인스턴스가 실패하는 경우에만 사용할 수 있습니다. 오류 처리 및 재시도 정책에 대한 자세한 내용은 [오류 처리](durable-functions-error-handling.md) 문서를 참조하세요.

*되감기*에 대한 한 가지 사용 사례는 [사람의 승인](durable-functions-concepts.md#human)이 포함된 워크플로입니다. 누군가에게 승인이 필요하다고 알리고 실시간 응답을 기다리는 일련의 작업 함수가 있다고 가정하겠습니다. 모든 승인 작업이 응답을 수신하거나 시간이 초과된 후에는 애플리케이션의 잘못된 구성(예: 잘못된 데이터베이스 연결 문자열)으로 인해 다른 작업이 실패합니다. 결과적으로 워크플로에 대한 오케스트레이션 실패가 발생합니다. `RewindAsync`(.NET) 또는 `rewindAsync`(JavaScript) API를 사용하면 애플리케이션 관리자가 구성 오류를 수정하고 실패한 오케스트레이션을 실패 직전의 상태로 ‘되감기’할 수 있습니다. 인간 상호 작용 단계를 다시 승인할 필요가 없기 때문에 오케스트레이션을 성공적으로 완료할 수 있습니다.

> [!NOTE]
> *되감기* 기능은 지속형 타이머를 사용하는 오케스트레이션 인스턴스 되감기를 지원하지 않습니다.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Core Tools 사용

[Core Tools](../functions-run-local.md) `durable rewind` 명령을 통해 오케스트레이션 인스턴스를 직접 되감을 수도 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)**: 오케스트레이션 인스턴스의 ID입니다.
* **`reason`(선택 사항)**: 오케스트레이션 인스턴스를 되감는 이유입니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>인스턴스 기록 제거

> [!NOTE]
> `PurgeInstanceHistoryAsync` API는 현재 C#에만 사용 가능합니다. 이는 향후 릴리스에서 JavaScript에 추가될 예정입니다.

[PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)를 사용하여 오케스트레이션 기록을 제거할 수 있습니다. 이 기능은 오케스트레이션과 연관된 모든 데이터를 제거하며 존재하는 경우 Azure 테이블 행과 대규모 메시지 Blob을 제거합니다. 이 메서드에는 두 가지 오버로드가 있습니다. 첫 번째는 오케스트레이션 인스턴스의 ID를 기준으로 기록을 제거합니다.

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

두 번째 예는 지정된 시간 간격 이후에 완료된 모든 오케스트레이션 인스턴스에 대한 기록을 제거하는 타이머 트리거 함수를 보여 줍니다. 이 예에서는 30일 이상 전에 완료된 모든 인스턴스의 데이터를 제거합니다. 하루에 한 번, 오전 12시에 실행되도록 예약되어 있습니다.

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> 매개 변수로 기간을 수락하는 *PurgeInstanceHistory* 오버로드는 런타임 상태가 완료됨, 종료됨, 실패 중 하나인 오케스트레이션 인스턴스만 처리합니다.

### <a name="using-core-tools"></a>Core Tools 사용

[Core Tools](../functions-run-local.md) `durable purge-history` 명령을 사용하여 오케스트레이션 인스턴스의 기록을 제거할 수도 있습니다. 위의 두 번째 C# 예제와 마찬가지로, 지정된 시간 간격 동안 생성된 모든 오케스트레이션 인스턴스에 대한 기록이 제거됩니다. 제거되는 인스턴스는 런타임 상태에 따라 추가로 필터링할 수 있습니다. 명령에는 다음과 같은 매개 변수가 있습니다.

* **`created-after`(선택 사항)**: 이 날짜/시간(UTC) 이후에 생성된 인스턴스의 기록을 제거합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`created-before`(선택 사항)**: 이 날짜/시간(UTC) 전에 생성된 인스턴스의 기록을 제거합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`runtime-status`(선택 사항)**: 이와 일치하는 상태(‘실행 중’, ‘완료됨’)의 인스턴스에 대한 기록을 제거합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령은 2018년 11월 14일 오후 7:35(UTC) 전에 작성된 모든 실패한 인스턴스의 기록을 삭제합니다.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>작업 허브 삭제

[Core Tools](../functions-run-local.md) `durable delete-task-hub` 명령을 사용하여 특정 작업 허브와 연관된 모든 스토리지 아티팩트를 삭제할 수 있습니다. 여기에는 Azure Storage 테이블, 큐 및 Blob이 포함됩니다. 명령에는 다음 두 개의 매개 변수가 있습니다.

* **`connection-string-setting`(선택 사항)**: 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)**: 사용할 지속형 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 통해 [host.json](durable-functions-bindings.md#host-json)에서 설정할 수도 있습니다.

다음 명령은 ‘UserTest’ 작업 허브와 연관된 모든 Azure Storage 데이터를 삭제합니다.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리에 HTTP API 사용](durable-functions-http-api.md)
