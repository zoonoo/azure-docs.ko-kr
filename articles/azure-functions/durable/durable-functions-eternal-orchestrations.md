---
title: 지속성 함수의 영구 오케스트레이션 - Azure
description: Azure Functions의 지속성 함수 확장을 사용하여 영구 오케스트레이션을 구현하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 34c70f4305ebb2c45757d982ab558aea6450003f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506369"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>지속성 함수의 영구 오케스트레이션(Azure Functions)

*영구 오케스트레이션*은 절대로 종료하지 않는 오케스트레이터 함수이며, 집계기 및 무한 루프가 필요한 모든 시나리오에 [지속성 함수](durable-functions-overview.md)를 사용하려는 경우에 유용합니다.

## <a name="orchestration-history"></a>오케스트레이션 기록

[오케스트레이션 기록](durable-functions-orchestrations.md#orchestration-history) 항목에서 설명한 대로 지 속성 작업 프레임 워크는 각 함수 오케스트레이션의 기록을 추적 합니다. 이 기록은 오케스트레이터 함수에서 새 작업을 계속 예약하는 한 지속적으로 증가합니다. 오케스트레이터 함수가 무한 루프에 들어가 지속적으로 작업을 예약하는 경우 이 기록이 매우 크게 증가할 수 있으며 심각한 성능 문제가 발생할 수 있습니다. *영구 오케스트레이션* 개념은 무한 루프가 필요한 애플리케이션에서 이러한 종류의 문제를 완화하도록 설계되었습니다.

## <a name="resetting-and-restarting"></a>다시 설정 및 다시 시작

무한 루프를 사용 하는 대신 orchestrator 함수는 `ContinueAsNew` 오케스트레이션 트리거 바인딩의 (.net), `continueAsNew` (JavaScript) 또는 `continue_as_new` (Python) 메서드 [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger)를 호출 하 여 해당 상태를 다시 설정 합니다. 이 메서드는 다음 오케스트레이터 함수 생성을 위한 새 입력이 되는 단일 JSON 직렬화 가능 매개 변수를 사용합니다.

`ContinueAsNew`가 호출되면 인스턴스에서 종료되기 전에 자체의 큐에 메시지를 넣습니다. 이 메시지는 새 입력 값으로 인스턴스를 다시 시작합니다. 동일한 인스턴스 ID를 유지하지만 오케스트레이터 함수의 기록이 효과적으로 잘립니다.

> [!NOTE]
> 지속성 작업 프레임워크는 동일한 인스턴스 ID를 유지하지만, `ContinueAsNew`로 다시 설정되는 오케스트레이터 함수에 대해 내부적으로 새 *실행 ID*를 만듭니다. 일반적으로 이 실행 ID는 외부적으로 노출되지 않지만 오케스트레이션 실행을 디버그할 때 알고 있으면 유용할 수 있습니다.

## <a name="periodic-work-example"></a>정기 작업 예제

영구 오케스트레이션을 위한 한 가지 사용 사례는 무한정 주기적으로 작업해야 하는 코드입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> 이전 c # 예제는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

이 예제와 타이머 트리거 함수 간의 차이점은 여기서 정리 타이머 시간이 일정에 기반하지 않는다는 것입니다. 예를 들어 매시간 함수를 실행하는 CRON 일정은 1시, 2시, 3시 등에 실행되며 잠재적으로 겹침 문제가 발생할 수 있습니다. 그러나 이 예제에서 정리에 30분이 걸리면 1시, 2시 30분, 4시 등으로 예약되며 겹쳐질 가능성이 없습니다.

## <a name="starting-an-eternal-orchestration"></a>영구 오케스트레이션 시작

`StartNewAsync`(.Net), ( `startNew` JavaScript), `start_new` (Python) 메서드를 사용 하 여 다른 오케스트레이션 함수와 마찬가지로 영구 오케스트레이션을 시작 합니다.  

> [!NOTE]
> Singleton 영구 오케스트레이션을 실행 하 고 있는지 확인 해야 하는 경우 오케스트레이션을 시작할 때 동일한 인스턴스를 유지 관리 하는 것이 중요 `id` 합니다. 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)를 참조하세요.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 `OrchestrationClient` 특성 대신 특성을 사용 해야 `DurableClient` 하며 `DurableOrchestrationClient` 대신 매개 변수 형식을 사용 해야 합니다 `IDurableOrchestrationClient` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>영구 오케스트레이션 종료

오케스트레이터 함수가 결국 완료되어야 하는 경우에는 `ContinueAsNew`를 *호출하지 않고* 함수가 종료되도록 해야 합니다.

오 케 스트레이 터 함수가 무한 루프에 있고 중지 되어야 하는 경우 `TerminateAsync` 오케스트레이션 클라이언트 바인딩의 (.net), `terminate` (JavaScript) 또는 `terminate` (Python) 메서드를 사용 하 [orchestration client binding](durable-functions-bindings.md#orchestration-client) 여 중지 합니다. 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 항목 오케스트레이션 구현](durable-functions-singletons.md)
