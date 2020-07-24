---
title: 지속성 함수의 타이머 - Azure
description: Azure Functions의 지속성 함수 확장에서 지속성 타이머를 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 0226e5141b100aa3fcf89dd1a5cade8f3cd6cf1c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056221"
---
# <a name="timers-in-durable-functions-azure-functions"></a>지속성 함수의 타이머(Azure Functions)

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머*를 제공합니다. 지 속성 타이머는 `Thread.Sleep` 및 `Task.Delay` (c #) 또는 `setTimeout()` 및 `setInterval()` (JavaScript) 또는 `time.sleep()` (Python) 대신 orchestrator 함수에서 사용 해야 합니다.

`CreateTimer` `createTimer` [오케스트레이션 트리거 바인딩의](durable-functions-bindings.md#orchestration-trigger)(.net) 메서드 또는 (JavaScript) 메서드를 호출 하 여 지 속성 타이머를 만듭니다. 메서드는 지정 된 날짜와 시간에 완료 되는 작업을 반환 합니다.

## <a name="timer-limitations"></a>타이머 제한 사항

오후 4:30 시에 만료 되는 타이머를 만들 때 기본 지 속성 작업 프레임 워크는 4:30 pm 에서만 표시 되는 메시지를 큐 합니다. Azure Functions 소비 계획에서 실행 하는 경우 새로 표시 되는 타이머 메시지는 함수 앱이 적절 한 VM에서 활성화 되도록 합니다.

> [!NOTE]
> * 지 속성 타이머는 현재 7 일로 제한 됩니다. 지연 시간이 더 길면 루프의 타이머 Api를 사용 하 여 시뮬레이션할 수 있습니다 `while` .
> * `CurrentUtcDateTime` `DateTime.UtcNow` `currentUtcDateTime` `Date.now` `Date.UTC` 영 속 타이머의 화재 시간을 계산할 때 .net 또는 대신 또는 JavaScript에서 대신를 사용 합니다. 자세한 내용은 [orchestrator 함수 코드 제약 조건](durable-functions-code-constraints.md) 문서를 참조 하세요.

## <a name="usage-for-delay"></a>지연 사용

다음 예제에서는 실행 지연에 지속성 타이머를 사용하는 방법을 보여 줍니다. 예는 매일 10 일 동안 청구 알림을 실행 하는 것입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 이전 c # 예제는 Durable Functions 2.x를 대상으로 합니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 무한 루프 시나리오를 안전하고 효율적으로 구현하는 방법에 대한 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="usage-for-timeout"></a>시간 제한 사용

이 예제에서는 지속성 타이머를 사용하여 시간 제한을 구현하는 방법을 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 이전 c # 예제는 Durable Functions 2.x를 대상으로 합니다. 1.x Durable Functions의 경우 대신를 사용 해야 합니다 `DurableOrchestrationContext` `IDurableOrchestrationContext` . 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> `CancellationTokenSource` `cancel()` `TimerTask` 코드가 완료 될 때까지 기다리지 않는 경우 (.net) 또는 반환 된 (JavaScript)에 대 한 호출을 사용 하 여 지 속성 타이머를 취소 합니다. 지 속성 작업 프레임 워크는 처리 중인 모든 작업이 완료 되거나 취소 될 때까지 오케스트레이션의 상태를 "완료 됨"으로 변경 하지 않습니다.

이 취소 메커니즘은 진행 중인 작업 함수 또는 하위 오케스트레이션 실행을 종료 하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 함수 앱이 소비 계획을 사용 하는 경우 중단 된 작업 함수에서 사용 하는 시간 및 메모리에 대 한 요금이 계속 청구 됩니다. 기본적으로 소비 계획에서 실행되는 함수의 시간 제한은 5분입니다. 이 제한을 초과하면 모든 실행을 중지하고 런어웨이 청구 상황을 방지하기 위해 Azure Functions 호스트가 재활용됩니다. [함수 시간 제한은 구성 가능](../functions-host-json.md#functiontimeout)합니다.

Orchestrator 함수에서 시간 제한을 구현 하는 방법에 대 한 자세한 예제는 [사용자 상호 작용 & 시간 제한-전화 확인](durable-functions-phone-verification.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [외부 이벤트 발생 및 처리](durable-functions-external-events.md)
