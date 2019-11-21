---
title: 지속성 함수의 타이머 - Azure
description: Azure Functions의 지속성 함수 확장에서 지속성 타이머를 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: c477a81801c1345d5be5f5f45419bb4776c875e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231277"
---
# <a name="timers-in-durable-functions-azure-functions"></a>지속성 함수의 타이머(Azure Functions)

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머*를 제공합니다. 지속성 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#) 또는 `setTimeout()` 및 `setInterval()`(JavaScript) 대신, 오케스트레이터 함수에 사용해야 합니다.

You create a durable timer by calling the `CreateTimer` (.NET) method or the `createTimer` (JavaScript) method of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). The method returns a task that completes on a specified date and time.

## <a name="timer-limitations"></a>타이머 제한 사항

When you create a timer that expires at 4:30 pm, the underlying Durable Task Framework enqueues a message that becomes visible only at 4:30 pm. When running in the Azure Functions Consumption plan, the newly visible timer message will ensure that the function app gets activated on an appropriate VM.

> [!NOTE]
> * Durable timers are currently limited to 7 days. If longer delays are needed, they can be simulated using the timer APIs in a `while` loop.
> * Always use `CurrentUtcDateTime` instead of `DateTime.UtcNow` in .NET or `currentUtcDateTime` instead of `Date.now` or `Date.UTC` in JavaScript when computing the fire time for durable timers. For more information, see the [orchestrator function code constraints](durable-functions-code-constraints.md) article.

## <a name="usage-for-delay"></a>지연 사용

다음 예제에서는 실행 지연에 지속성 타이머를 사용하는 방법을 보여 줍니다. The example is issuing a billing notification every day for 10 days.

### <a name="c"></a>C#

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
> The previous C# example targets Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 무한 루프 시나리오를 안전하고 효율적으로 구현하는 방법에 대한 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="usage-for-timeout"></a>시간 제한 사용

이 예제에서는 지속성 타이머를 사용하여 시간 제한을 구현하는 방법을 보여 줍니다.

### <a name="c"></a>C#

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
> The previous C# example targets Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

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

> [!WARNING]
> Use a `CancellationTokenSource` to cancel a durable timer (.NET) or call `cancel()` on the returned `TimerTask` (JavaScript) if your code will not wait for it to complete. The Durable Task Framework will not change an orchestration's status to "completed" until all outstanding tasks are completed or canceled.

This cancellation mechanism doesn't terminate in-progress activity function or sub-orchestration executions. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. If your function app uses the Consumption plan, you'll still be billed for any time and memory consumed by the abandoned activity function. 기본적으로 소비 계획에서 실행되는 함수의 시간 제한은 5분입니다. 이 제한을 초과하면 모든 실행을 중지하고 런어웨이 청구 상황을 방지하기 위해 Azure Functions 호스트가 재활용됩니다. [함수 시간 제한은 구성 가능](../functions-host-json.md#functiontimeout)합니다.

For a more in-depth example of how to implement timeouts in orchestrator functions, see the [Human Interaction & Timeouts - Phone Verification](durable-functions-phone-verification.md) article.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [외부 이벤트 발생 및 처리](durable-functions-external-events.md)
