---
title: 지속성 함수의 오류 처리 - Azure
description: Azure Functions의 지속성 함수 확장에서 오류를 처리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0900e3f3b76f4a82e06fe3c0e6d9bbe63b545f56
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231416"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>지속성 함수의 오류 처리(Azure Functions)

Durable Function orchestrations are implemented in code and can use the programming language's built-in error-handling features. There really aren't any new concepts you need to learn to add error handling and compensation into your orchestrations. 그러나 알고 있어야 하는 몇 가지 동작이 있습니다.

## <a name="errors-in-activity-functions"></a>작업 함수의 오류

Any exception that is thrown in an activity function is marshaled back to the orchestrator function and thrown as a `FunctionFailedException`. 오케스트레이터 함수에서 요구 사항에 적합한 오류 처리 및 보정 코드를 작성할 수 있습니다.

예를 들어 한 계정에서 다른 계정으로 자금을 이체하는 다음 오케스트레이터 함수를 살펴보세요.

### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="c-script"></a>C# 스크립트

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

If the first **CreditAccount** function call fails, the orchestrator function compensates by crediting the funds back to the source account.

## <a name="automatic-retry-on-failure"></a>실패 시 자동 다시 시도

작업 함수 또는 하위 오케스트레이션 함수를 호출할 때 자동으로 다시 시도하는 정책을 지정할 수 있습니다. 다음 예제에서는 함수를 최대 3회 호출하려고 하고 각각의 다시 시도 간에 5초 동안 기다립니다.

### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync`(.NET) 또는 `callActivityWithRetry`(JavaScript) API는 `RetryOptions` 매개 변수를 사용합니다. Sub-orchestration calls using the `CallSubOrchestratorWithRetryAsync` (.NET) or `callSubOrchestratorWithRetry` (JavaScript) API can use these same retry policies.

There are several options for customizing the automatic retry policy:

* **최대 시도 횟수**: 최대 다시 시도 횟수입니다.
* **첫 번째 다시 시도 간격**: 첫 번째 다시 시도를 수행할 때까지 기다리는 시간입니다.
* **백오프 계수**: 백오프의 증가율을 결정하는 데 사용되는 계수입니다. 기본값은 1입니다.
* **최대 다시 시도 간격**: 다시 시도 간에 기다리는 최대 시간입니다.
* **다시 시도 시간 제한**: 다시 시도하는 데 소요되는 최대 시간입니다. 기본 동작은 무기한으로 다시 시도하는 것입니다.
* **Handle**: A user-defined callback can be specified to determine whether a function should be retried.

## <a name="function-timeouts"></a>함수 시간 제한

You might want to abandon a function call within an orchestrator function if it's taking too long to complete. 현재 이 작업을 제대로 수행하는 방법은 다음 예제와 같이 `Task.WhenAny`(.NET) 또는 `context.df.Task.any`(JavaScript)와 함께 `context.CreateTimer`(.NET) 또는 `context.df.createTimer`(JavaScript)를 사용하여 [지속성 타이머](durable-functions-timers.md)를 만드는 것입니다.

### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task<bool> Run(IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> 이 메커니즘은 실제로 진행 중인 작업 함수 실행을 종료하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 자세한 내용은 [타이머](durable-functions-timers.md#usage-for-timeout) 설명서를 참조하세요.

## <a name="unhandled-exceptions"></a>처리되지 않은 예외

처리되지 않은 예외로 인해 오케스트레이터 함수가 실패하면 예외 정보가 자세히 기록되고 인스턴스가 `Failed` 상태로 완료됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Learn about eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [문제 진단](durable-functions-diagnostics.md)
