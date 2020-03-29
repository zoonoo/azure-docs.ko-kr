---
title: 지속성 함수의 타이머 - Azure
description: Azure Functions의 지속성 함수 확장에서 지속성 타이머를 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261486"
---
# <a name="timers-in-durable-functions-azure-functions"></a>지속성 함수의 타이머(Azure Functions)

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머*를 제공합니다. 지속성 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#) 또는 `setTimeout()` 및 `setInterval()`(JavaScript) 대신, 오케스트레이터 함수에 사용해야 합니다.

`CreateTimer` [오케스트레이션 트리거 바인딩의](durable-functions-bindings.md#orchestration-trigger)(.NET) 메서드 `createTimer` 또는 (JavaScript) 메서드를 호출 하여 내구성 있는 타이머를 만듭니다. 메서드는 지정된 날짜 및 시간에 완료 되는 작업을 반환 합니다.

## <a name="timer-limitations"></a>타이머 제한 사항

오후 4시 30분에 만료되는 타이머를 만들면 기본 지속적 작업 프레임워크는 오후 4시 30분에만 표시되는 메시지를 큐에 대기합니다. Azure Functions 소비 계획에서 실행 될 때 새로 보이는 타이머 메시지 함수 응용 프로그램 적절 한 VM에서 활성화 되 면 확인 합니다.

> [!NOTE]
> * 내구성이 뛰어난 타이머는 현재 7일로 제한됩니다. 더 긴 지연이 필요한 경우 루프의 타이머 API를 `while` 사용하여 시뮬레이션할 수 있습니다.
> * 내구성이 `CurrentUtcDateTime` 뛰어난 `DateTime.UtcNow` 타이머의 `currentUtcDateTime` 가동 `Date.now` 중지 `Date.UTC` 시간을 계산할 때는 항상 .NET 대신 사용하거나 JavaScript 대신 사용하십시오. 자세한 내용은 [오케스트레이터 함수 코드 제약 조건 문서를 참조하세요.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>지연 사용

다음 예제에서는 실행 지연에 지속성 타이머를 사용하는 방법을 보여 줍니다. 이 예제는 10일 동안 매일 청구 알림을 발행하는 것입니다.

# <a name="c"></a>[C #](#tab/csharp)

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
> 이전 C# 예제는 내구성 함수 2.x를 대상으로 합니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

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

---

> [!WARNING]
> 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 무한 루프 시나리오를 안전하고 효율적으로 구현하는 방법에 대한 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="usage-for-timeout"></a>시간 제한 사용

이 예제에서는 지속성 타이머를 사용하여 시간 제한을 구현하는 방법을 보여 줍니다.

# <a name="c"></a>[C #](#tab/csharp)

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
> 이전 C# 예제는 내구성 함수 2.x를 대상으로 합니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

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

---

> [!WARNING]
> 코드가 `CancellationTokenSource` 완료될 때까지 기다리지 않을 `TimerTask` 경우 반환된(JavaScript)을 호출하여 `cancel()` 지속가능한 타이머를 취소합니다. 지속형 작업 프레임워크는 모든 미해결 작업이 완료되거나 취소될 때까지 오케스트레이션의 상태를 "완료"로 변경하지 않습니다.

이 취소 메커니즘은 진행 중인 활동 함수 또는 하위 오케스트레이션 실행을 종료하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 함수 앱에서 소비 계획을 사용하는 경우 중단된 활동 함수에서 소비하는 모든 시간과 메모리에 대한 요금이 계속 청구됩니다. 기본적으로 소비 계획에서 실행되는 함수의 시간 제한은 5분입니다. 이 제한을 초과하면 모든 실행을 중지하고 런어웨이 청구 상황을 방지하기 위해 Azure Functions 호스트가 재활용됩니다. [함수 시간 제한은 구성 가능](../functions-host-json.md#functiontimeout)합니다.

오케스트레이터 함수에서 시간 시간을 구현하는 방법에 대한 자세한 예제는 [휴먼 상호 작용 & 시간 설정 - 전화 확인](durable-functions-phone-verification.md) 문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [외부 이벤트 발생 및 처리](durable-functions-external-events.md)
