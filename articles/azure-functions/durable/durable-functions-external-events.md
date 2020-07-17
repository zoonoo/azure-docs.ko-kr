---
title: 지속성 함수의 외부 이벤트 처리 - Azure
description: Azure Functions의 지속성 함수 확장에서 외부 이벤트를 처리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165552"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>지속성 함수의 외부 이벤트 처리(Azure Functions)

오케스트레이터 함수에는 외부 이벤트를 대기 및 수신 대기하는 기능이 있습니다. [지속성 함수](durable-functions-overview.md)의 이 기능은 종종 인간 상호 작용 또는 다른 외부 트리거를 처리하는 데 유용합니다.

> [!NOTE]
> 외부 이벤트는 단방향 비동기 작업입니다. 이 이벤트는 이벤트를 보내는 클라이언트가 오케스트레이터 기능의 동기 응답을 요구하는 상황에서는 적합하지 않습니다.

## <a name="wait-for-events"></a>이벤트 대기

오케스트레이션 트리거 바인딩의 [Waitforexternalevent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.net) 및 (JavaScript) 메서드를 사용 하면 오 케 스트레이 터 `waitForExternalEvent` 함수가 외부 이벤트를 비동기적으로 대기 하 고 수신할 수 있습니다. [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) 수신 오케스트레이터는 이벤트의 *이름*과 수신할 것으로 예상되는 *데이터의 셰이프*를 선언합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

---

앞의 예제에서는 특정 단일 이벤트를 수신 대기하고, 이 이벤트가 수신되면 작업을 수행합니다.

수신 가능한 세 가지 이벤트 알림 중 하나를 기다리는 다음 예제와 같이 여러 이벤트를 동시에 수신 대기할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

---

앞의 예제에서는 여러 이벤트 중 *하나*를 수신 대기합니다. *모든* 이벤트를 기다릴 수도 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

.NET에서 이벤트 페이로드를 필요한 `T` 형식으로 변환할 수 없으면 예외가 throw됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

---

`WaitForExternalEvent`일부 입력에 대해 무기한 대기 합니다.  함수 앱은 기다리는 동안 안전하게 언로드될 수 있습니다. 이 오케스트레이션 인스턴스에 이벤트가 도착하면 인스턴스가 자동으로 활성화하고 해당 이벤트를 즉시 처리합니다.

> [!NOTE]
> 함수 앱이 소비 계획을 사용하는 경우 지연 시간에 관계 없이 오케스트레이터 함수에서 `WaitForExternalEvent`(.NET) 또는 `waitForExternalEvent`(JavaScript)의 작업을 기다리는 동안에 대한 요금은 청구되지 않습니다.

## <a name="send-events"></a>이벤트 보내기

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) 또는 `raiseEventAsync` (JavaScript) 메서드를 사용 하 여 오케스트레이션에 외부 이벤트를 보낼 수 있습니다. 이러한 메서드는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 바인딩에 의해 노출 됩니다. 기본 제공 [이벤트 발생 HTTP API](durable-functions-http-api.md#raise-event) 를 사용 하 여 오케스트레이션에 외부 이벤트를 보낼 수도 있습니다.

발생 한 이벤트에는 *인스턴스 ID*, *eventName*및 *eventData* 가 매개 변수로 포함 됩니다. Orchestrator 함수는 `WaitForExternalEvent` (.net) 또는 (JavaScript) api를 사용 하 여 이러한 이벤트를 처리 `waitForExternalEvent` 합니다. 이벤트를 처리 하려면 *eventName* 이 송신 및 수신 끝 모두에서 일치 해야 합니다. 또한 이벤트 데이터는 JSON serializable 이어야 합니다.

내부적으로 "이벤트 발생" 메커니즘은 대기 오 케 스트레이 터 함수에 의해 선택 되는 메시지를 큐에 삽입 합니다. 인스턴스가 지정된 *이벤트 이름*에서 대기하고 있지 않으면 이벤트 메시지가 메모리 내 큐에 추가됩니다. 오케스트레이션 인스턴스에서 나중에 해당 *이벤트 이름*에 대한 수신 대기를 시작하는 경우 이벤트 메시지가 큐에 있는지 확인합니다.

> [!NOTE]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없으면 이벤트 메시지가 삭제됩니다.

다음은 오케스트레이터 함수 인스턴스에 "승인" 이벤트를 보내는 큐 트리거 함수 예제입니다. 오케스트레이션 인스턴스 ID는 큐 메시지 본문에서 가져옵니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> 이전 c # 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

`RaiseEventAsync`(.NET) 또는 `raiseEvent`(JavaScript)는 내부적으로 대기 중인 오케스트레이터 함수에서 선택된 메시지를 큐에 넣습니다. 인스턴스가 지정된 *이벤트 이름*에서 대기하고 있지 않으면 이벤트 메시지가 메모리 내 큐에 추가됩니다. 오케스트레이션 인스턴스에서 나중에 해당 *이벤트 이름*에 대한 수신 대기를 시작하는 경우 이벤트 메시지가 큐에 있는지 확인합니다.

> [!NOTE]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없으면 이벤트 메시지가 삭제됩니다.

### <a name="http"></a>HTTP

다음은 오케스트레이션 인스턴스에 "승인" 이벤트를 발생 시키는 HTTP 요청의 예입니다. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

이 경우 인스턴스 ID는 *Myinstanceid*로 하드 코딩 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오류 처리를 구현 하는 방법 알아보기](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [인간 상호 작용을 기다리는 샘플 실행](durable-functions-phone-verification.md)