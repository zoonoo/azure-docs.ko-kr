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
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: bcb87b3030eb673dd3c9a8b93a045c75fae964a9
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087035"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>지속성 함수의 인스턴스 관리(Azure Functions)

[지속성 함수](durable-functions-overview.md) 오케스트레이션 인스턴스는 알림 이벤트를 시작, 종료, 쿼리 및 전송할 수 있습니다. 모든 인스턴스 관리는 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md)을 사용하여 수행됩니다. 이 문서에서는 각 인스턴스 관리 작업에 대해 자세히 설명합니다.

## <a name="starting-instances"></a>인스턴스 시작

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)의 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 메서드는 오케스트레이터 함수의 새 인스턴스를 시작합니다. 이 클래스의 인스턴스는 `orchestrationClient` 바인딩을 사용하여 얻을 수 있습니다. 내부적으로 이 메서드는 메시지를 제어 큐에 넣고 `orchestrationTrigger` 트리거 바인딩을 사용하는 지정된 이름의 함수 시작을 트리거합니다. 

오케스트레이션 프로세스를 시작할 때 작업을 완료합니다. 오케스트레이션 프로세스는 30초 이내에 시작해야 합니다. 더 오래 걸리는 경우 `TimeoutException`이 throw됩니다. 

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)에 대한 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **Input**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
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

비.NET 언어의 경우 함수 출력 바인딩을 사용하여 새 인스턴스를 시작할 수도 있습니다. 이 경우 위의 세 매개 변수를 필드로 사용하는 JSON 직렬화 가능 개체를 사용할 수 있습니다. 예를 들어 다음 JavaScript 함수를 살펴보세요.

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```
위의 코드는 function.json 파일에서 이름이 "starter"이고 형식이 "orchestrationClient"인 아웃 바인딩을 정의했다고 가정합니다. 바인딩이 정의되어 있지 않으면 지속성 함수 인스턴스가 만들어지지 않습니다.

지속성 함수를 호출하려면 아래 설명된 대로 오케스트레이션 클라이언트에 대한 바인딩을 포함하도록 function.json을 수정해야 합니다.

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> 인스턴스 ID에 대해 임의의 식별자를 사용하는 것이 좋습니다. 이렇게 하면 여러 VM에서 오케스트레이터 함수를 크기 조정할 때 균등한 부하 분산을 보장하는 데 도움이 됩니다. D가 외부 원본에서 제공되어야 하거나 [단일 항목 오케스트레이터](durable-functions-singletons.md) 패턴을 구현하는 경우에는 임의가 아닌 인스턴스 ID를 사용하는 것이 좋습니다.

## <a name="querying-instances"></a>인스턴스 쿼리

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [ GetStatusAsync ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 메서드는 오케스트레이션 인스턴스의 상태를 쿼리합니다. `instanceId`(필수), `showHistory`(선택 사항) 및 `showHistoryOutput`(선택 사항)을 매개 변수로 사용합니다. `showHistory`를 `true`로 설정한 경우 응답에는 실행 기록이 포함됩니다. `showHistoryOutput`을 `true`로 설정한 경우 실행 기록에는 작업 출력이 포함됩니다. 메서드는 다음과 같은 속성이 있는 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **Input**: JSON 값의 함수 입력입니다.
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다. 
* **Output**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오케스트레이터 함수가 실패하면 이 속성에 오류 세부 정보가 포함됩니다. 오케스트레이터 함수가 종료되면 이 속성에 제공된 종료 이유가 포함됩니다(있는 경우).
* **RuntimeStatus**: 다음 값 중 하나입니다.
    * **보류 중**: 인스턴스는 일정이 있지만 아직 실행을 시작하지 않았습니다.
    * **실행 중**: 인스턴스가 실행되기 시작했습니다.
    * **완료됨**: 인스턴스가 정상적으로 완료되었습니다.
    * **ContinuedAsNew(새 기록으로 계속됨)**: 인스턴스가 새 기록으로 다시 시작되었습니다. 일시적인 상태입니다.
    * **실패**: 인스턴스가 오류로 인해 실패했습니다.
    * **종료됨**: 인스턴스가 갑자기 종료되었습니다.
* **기록**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.
    
인스턴스가 존재하지 않거나 아직 실행을 시작하지 않은 경우 이 메서드는 `null`을 반환합니다.

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
## <a name="querying-all-instances"></a>모든 인스턴스 쿼리

`GetStatusAsync` 메서드를 사용하여 모든 오케스트레이션 인스턴스의 상태를 쿼리할 수 있습니다. 매개 변수가 필요하지 않으며, 취소하고 싶으면 `CancellationToken` 개체를 전달하면 됩니다. 이 메서드는 기록을 반환하지 않는다는 점을 제외하면 매개 변수가 있는 `GetStatusAsync` 메서드와 속성이 동일한 개체를 반환합니다. 

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
## <a name="querying-instances-with-filters"></a>필터로 인스턴스 쿼리

`GetStatusAsync` 메서드를 사용하여 미리 정의된 필터 집합과 일치하는 오케스트레이션 인스턴스 목록을 가져올 수도 있습니다. 가능한 필터 옵션에는 오케스트레이션 생성 시간과 오케스트레이션 런타임 상태가 포함됩니다.

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

## <a name="terminating-instances"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스는 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 메서드를 사용하여 종료할 수 있습니다. 두 가지 매개 변수는 `instanceId` 및 `reason` 문자열이며, 로그 및 인스턴스 상태에 기록됩니다. 종료된 인스턴스는 다음 `await` 지점에 도달하는 즉시 실행을 중지하거나 이미 `await`에 있는 경우 즉시 종료됩니다. 

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

> [!NOTE]
> 현재 인스턴스 종료는 전파되지 않았습니다. 활동 함수 및 하위 오케스트레이션은 호출된 오케스트레이션 인스턴스가 종료되었는지 여부에 관계 없이 완료될 때까지 실행됩니다.

## <a name="sending-events-to-instances"></a>인스턴스로 이벤트 보내기

이벤트 알림은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 메서드를 사용하여 실행 중인 인스턴스로 보낼 수 있습니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_)에 대한 호출을 기다리는 인스턴스입니다. 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)에 대한 매개 변수는 다음과 같습니다.

* **InstanceId**: 인스턴스의 고유 ID입니다.
* **EventName**: 보낼 이벤트의 이름입니다.
* **EventData**: 인스턴스에 보낼 JSON 직렬화 가능 페이로드입니다.

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

> [!WARNING]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없거나 인스턴스에서 지정된 *이벤트 이름*을 기다리지 않는 경우 해당 이벤트 메시지는 버려집니다. 이 동작에 대한 자세한 내용은 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/29)를 참조하세요.

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스는 오케스트레이션 인스턴스에서 실제 출력을 동기적으로 가져오는 데 사용할 수 있는 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API를 노출합니다. 메서드가 설정되지 않은 경우 `timeout`에서 10초를 기본값으로 사용하고 `retryInterval`에서 1초를 기본값으로 사용합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

2초 제한 시간 및 0.5초 다시 시도 간격을 사용하여 다음 행에서 함수를 호출할 수 있습니다.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

1. 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료됩니다. 응답은 동기적으로 배달된 실제 오케스트레이션 인스턴스 출력입니다.

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

2. 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료될 수 없습니다. 응답은 **HTTP API URL 검색**에 설명된 기본값입니다.

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
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 앞의 예제는 Azure Functions 2.0 호스트에 대한 것입니다.

## <a name="retrieving-http-management-webhook-urls"></a>HTTP 관리 Webhook URL 검색

외부 시스템은 [HTTP API URL 검색](durable-functions-http-api.md)에 설명된 기본 응답의 일부인 webhook URL을 통해 지속형 함수와 통신할 수 있습니다. 하지만 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 메서드를 통해 오케스트레이션 클라이언트에서 또는 작업 함수에서 프로그래밍 방식으로 webhook URL에 액세스할 수도 있습니다. 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)에는 매개 변수가 하나 있습니다.

* **instanceId**: 인스턴스의 고유 ID입니다.

이 메서드는 다음 문자열 속성이 있는 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 인스턴스를 반환합니다.

* **Id**: 오케스트레이션의 인스턴스 ID입니다(`InstanceId` 입력과 동일해야 함).
* **StatusQueryGetUri**: 오케스트레이션 인스턴스의 상태 URL입니다.
* **SendEventPostUri**: 오케스트레이션 인스턴스의 "이벤트 발생" URL입니다.
* **TerminatePostUri**: 오케스트레이션 인스턴스의 "종료" URL입니다.
* **RewindPostUri**: 오케스트레이션 인스턴스의 "rewind" URL입니다.

작업 함수는 외부 시스템에 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 인스턴스를 보내 오케스트레이션을 모니터링하거나 이벤트를 발생시킬 수 있습니다.

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

## <a name="rewinding-instances-preview"></a>되감기 인스턴스(미리 보기)

실패한 오케스트레이션 인스턴스는 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) API를 사용하여 이전의 정상 상태로 *되감기*될 수 있습니다. 오케스트레이션을 *실행* 상태로 되돌리고 오케스트레이션 실패를 유발한 작업 및/또는 하위 오케스트레이션 실행 실패를 다시 실행하여 수행됩니다.

> [!NOTE]
> 이 API로 적절한 오류 처리 및 재시도 정책을 대체할 수 없습니다. 예기치 않은 이유로 오케스트레이션 인스턴스가 실패하는 경우에만 사용할 수 있습니다. 오류 처리 및 재시도 정책에 대한 자세한 내용은 [오류 처리](durable-functions-error-handling.md) 문서를 참조하세요.

*되감기*에 대한 한 가지 사용 사례는 [사람의 승인](durable-functions-overview.md#pattern-5-human-interaction)이 포함된 워크플로입니다. 누군가에게 승인이 필요하다고 알리고 실시간 응답을 기다리는 일련의 작업 함수가 있다고 가정하겠습니다. 모든 승인 작업이 응답을 수신하거나 시간이 초과된 후에는 응용 프로그램의 잘못된 구성(예: 잘못된 데이터베이스 연결 문자열)으로 인해 다른 작업이 실패합니다. 결과적으로 워크플로에 대한 오케스트레이션 실패가 발생합니다. `RewindAsync` API를 사용하면 응용 프로그램 관리자가 구성 오류를 수정하고 실패한 오케스트레이션을 실패 직전의 상태로 *되감기*할 수 있습니다. 인간 상호 작용 단계를 다시 승인할 필요가 없기 때문에 오케스트레이션을 성공적으로 완료할 수 있습니다.

> [!NOTE]
> *되감기* 기능은 지속형 타이머를 사용하는 오케스트레이션 인스턴스 되감기를 지원하지 않습니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리에 HTTP API 사용](durable-functions-http-api.md)
