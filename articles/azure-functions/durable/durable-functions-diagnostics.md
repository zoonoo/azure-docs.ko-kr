---
title: 지속성 함수의 진단 - Azure
description: Azure Functions의 지속성 함수 확장을 사용하여 문제를 진단하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231465"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure의 지속성 함수에서 진단

[지속성 함수](durable-functions-overview.md)를 사용하여 문제를 진단하기 위한 몇 가지 옵션이 있습니다. 이러한 옵션 중 일부는 일반 함수와 동일하며 그 중 일부는 지속성 함수에만 고유하게 적용됩니다.

## <a name="application-insights"></a>Application Insights

Azure Functions에서 진단 및 모니터링을 수행하려면 [Application Insights](../../azure-monitor/app/app-insights-overview.md)를 사용하는 것이 좋습니다. 지속성 함수에도 동일하게 적용됩니다. 함수 앱에서 Application Insights를 활용하는 방법에 대한 개요는 [Azure Functions 모니터링](../functions-monitoring.md)을 참조하세요.

또한 Azure Functions 지속성 확장은 오케스트레이션의 엔드투엔드 실행을 추적할 수 있는 *추적 이벤트*를 내보냅니다. These tracking events can be found and queried using the [Application Insights Analytics](../../azure-monitor/app/analytics.md) tool in the Azure portal.

### <a name="tracking-data"></a>추적 데이터

오케스트레이션 인스턴스의 각 수명 주기 이벤트는 추적 이벤트를 Application Insights의 **traces** 컬렉션에 기록합니다. 이 이벤트에는 여러 필드가 있는 **customDimensions** 페이로드가 포함됩니다.  모든 필드 이름 앞에 `prop__`가 추가됩니다.

* **hubName**: 오케스트레이션이 실행 중인 작업 허브의 이름입니다.
* **appName**: 함수 앱의 이름입니다. This field is useful when you have multiple function apps sharing the same Application Insights instance.
* **slotName**: 현재 함수 앱이 실행 중인 [배포 슬롯](../functions-deployment-slots.md)입니다. This field is useful when you leverage deployment slots to version your orchestrations.
* **functionName**: 오케스트레이터 또는 작업 함수의 이름입니다.
* **functionType**: **Orchestrator** 또는 **Activity**와 같은 함수 유형입니다.
* **instanceId**: 오케스트레이션 인스턴스의 고유 ID입니다.
* **state**: 인스턴스의 수명 주기 실행 상태입니다. 유효한 값은 다음과 같습니다.
  * **예약됨**: 함수 실행이 예약되었지만 아직 실행되지 않았습니다.
  * **시작됨**: 함수 실행이 시작되었지만 아직 대기하거나 완료되지 않았습니다.
  * **대기**: 오케스트레이터에서 작업을 예약했고 완료되기를 기다리고 있습니다.
  * **수신 대기 중**: 오케스트레이터에서 외부 이벤트 알림을 수신 대기 중입니다.
  * **완료됨**: 함수가 성공적으로 완료되었습니다.
  * **실패**: 함수가 오류로 인해 실패했습니다.
* **reason**: 추적 이벤트와 관련된 추가 데이터입니다. 예를 들어 인스턴스에서 외부 이벤트 알림을 기다리고 있는 경우 이 필드는 대기 중인 이벤트의 이름을 나타냅니다. If a function has failed, this field will contain the error details.
* **isReplay**: 재생된 실행에 대한 추적 이벤트인지를 나타내는 부울 값입니다.
* **extensionVersion**: 지속성 작업 확장의 버전입니다. The version information is especially important data when reporting possible bugs in the extension. 장기 실행 인스턴스는 실행되는 동안 업데이트가 발생하는 경우 여러 버전을 보고할 수 있습니다.
* **sequenceNumber**: 이벤트에 대한 실행 시퀀스 번호. 타임스탬프와 결합하면 이벤트를 실행 시간 기준으로 정렬하는 데 도움이 됩니다. *인스턴스가 실행 중인 동안 호스트가 다시 시작되면 이 번호가 0으로 다시 설정되므로, 항상 가장 먼저 타임스탬프를 기준으로 정렬한 후 sequenceNumber를 기준으로 정렬해야 합니다.*

The verbosity of tracking data emitted to Application Insights can be configured in the `logger` (Functions 1.x) or `logging` (Functions 2.0) section of the `host.json` file.

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

기본적으로 모든 재생되지 않는 추적 이벤트를 내보냅니다. 예외 상황에서만 추적 이벤트를 내보내는 경우 `Host.Triggers.DurableTask`를 `"Warning"` 또는 `"Error"`로 설정하여 데이터의 양을 줄일 수 있습니다.

자세한 오케스트레이션 재생 이벤트를 내보내도록 설정하려면 다음에 표시된 대로 `durableTask` 아래의 `host.json` 파일에서 `LogReplayEvents`를 `true`로 설정할 수 있습니다.

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 기본적으로 데이터를 너무 자주 내보내지 않도록 방지하기 위해 Azure Functions 런타임에서 Application Insights 원격 분석을 샘플링합니다. 이로 인해 짧은 기간 동안 많은 수명 주기 이벤트가 발생하면 추적 정보가 손실될 수 있습니다. [Azure Functions 모니터링 문서](../functions-monitoring.md#configure-sampling)에서는 이 동작을 구성하는 방법에 대해 설명합니다.

### <a name="single-instance-query"></a>단일 인스턴스 쿼리

다음 쿼리에서는 [Hello 시퀀스](durable-functions-sequence.md) 함수 오케스트레이션의 단일 인스턴스에 대한 추적 기록 데이터를 보여 줍니다. [AIQL(Application Insights Query Language)](https://aka.ms/LogAnalyticsLanguageReference)을 사용하여 작성되었습니다. *논리* 실행 경로만 표시되도록 재생 실행을 필터링합니다. 아래 쿼리처럼 `timestamp` 및 `sequenceNumber`를 기준으로 이벤트를 정렬할 수 있습니다.

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

그 결과는 실행 시간 오름차순으로 정렬된 작업 함수를 포함하여 오케스트레이션의 실행 경로를 보여주는 추적 이벤트 목록입니다.

![Application Insights 쿼리](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>인스턴스 요약 쿼리

다음 쿼리에서는 지정된 시간 범위에서 실행된 모든 오케스트레이션 인스턴스의 상태를 표시합니다.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

결과는 인스턴스 ID 및 현재 런타임 상태의 목록입니다.

![Application Insights 쿼리](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>로깅

오케스트레이터 함수에서 로그를 직접 작성할 때 오케스트레이터 재생 동작을 고려하는 것이 중요합니다. 예를 들어 다음 오케스트레이터 함수를 살펴보세요.

### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

The resulting log data is going to look something like the following example output:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 로그는 F1, F2 및 F3을 호출한다고 주장하지만 이러한 함수는 처음 발생할 때만 *실제로* 호출됩니다. 재생 중에 발생하는 후속 호출은 건너뛰고, 출력은 오케스트레이터 논리로 재생됩니다.

재생되지 않는 실행에만 로그온하려면 `IsReplaying`이 `false`인 경우에만 기록할 조건식을 작성할 수 있습니다. 위의 예제를 살펴보았지만 이번에는 재생 확인을 사용합니다.

#### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Starting in Durable Functions 2.0, .NET orchestrator functions also have the option to create an `ILogger` that automatically filters out log statements during replay. This automatic filtering is done using the `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

With the previously mentioned changes, the log output is as follows:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="custom-status"></a>사용자 지정 상태

사용자 지정 오케스트레이션 상태를 사용하면 오케스트레이터 함수의 사용자 지정 상태 값을 설정할 수 있습니다. 이 상태는 HTTP 상태 쿼리 API 또는 `IDurableOrchestrationClient.GetStatusAsync` API를 통해 제공됩니다. 사용자 지정 오케스트레이션 상태를 통해 오케스트레이터 함수를 보다 자세히 모니터링할 수 있습니다. 예를 들어, 오케스트레이터 함수 코드는 장기 실행 작업에 대한 진행 상태를 업데이트하기 위한 `IDurableOrchestrationContext.SetCustomStatus` 호출을 포함할 수 있습니다. 따라서 웹 페이지 또는 다른 외부 시스템과 같은 클라이언트가 HTTP 상태 쿼리 API를 주기적으로 쿼리하여 보다 다양한 진행률 정보를 얻을 수 있습니다. `IDurableOrchestrationContext.SetCustomStatus` 사용 샘플은 아래에 제공됩니다.

### <a name="precompiled-c"></a>미리 컴파일된 C#

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

오케스트레이션이 실행되는 동안 외부 클라이언트가 이 사용자 지정 상태를 가져올 수 있습니다.

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

클라이언트는 다음과 같은 응답을 받습니다.

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 사용자 지정 상태 페이로드는 Azure Table Storage 열에 맞아야 하므로 16KB의 UTF-16 JSON 텍스트로 제한됩니다. 더 큰 페이로드가 필요한 경우 외부 스토리지를 사용하면 됩니다.

## <a name="debugging"></a>디버깅

Azure Functions는 디버깅 함수 코드를 직접 지원하며, Azure 또는 로컬에서 실행하는지 여부와 관계 없이 동일한 지원이 지속성 함수에 전달됩니다. 그러나 디버그할 때 고려해야 할 몇 가지 동작이 있습니다.

* **Replay**: Orchestrator functions regularly [replay](durable-functions-orchestrations.md#reliability) when new inputs are received. This behavior means a single *logical* execution of an orchestrator function can result in hitting the same breakpoint multiple times, especially if it is set early in the function code.
* **Await**: Whenever an `await` is encountered in an orchestrator function, it yields control back to the Durable Task Framework dispatcher. If it is the first time a particular `await` has been encountered, the associated task is *never* resumed. Because the task never resumes, stepping *over* the await (F10 in Visual Studio) is not possible. 작업이 재생되는 경우에만 단계별 실행이 작동합니다.
* **Messaging timeouts**: Durable Functions internally uses queue messages to drive execution of orchestrator, activity, and entity functions. 다중 VM 환경에서 오랜 시간 동안 디버그하는 경우 다른 VM에서 메시지를 선택하여 중복 실행이 발생할 수 있습니다. 이 동작은 일반 큐 트리거 함수에도 적용되지만, 큐가 구현 세부 정보이므로 이 컨텍스트에서 지시하는 것이 중요합니다.
* **Stopping and starting**: Messages in Durable functions persist between debug sessions. If you stop debugging and terminate the local host process while a durable function is executing, that function may re-execute automatically in a future debug session. This behavior can be confusing when not expected. Clearing all messages from the [internal storage queues](durable-functions-perf-and-scale.md#internal-queue-triggers) between debug sessions is one technique to avoid this behavior.

> [!TIP]
> When setting breakpoints in orchestrator functions, if you want to only break on non-replay execution, you can set a conditional breakpoint that breaks only if `IsReplaying` is `false`.

## <a name="storage"></a>스토리지

기본적으로 지속성 함수는 Azure Storage에 상태를 저장합니다. This behavior means you can inspect the state of your orchestrations using tools such as [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer screenshot](./media/durable-functions-diagnostics/storage-explorer.png)

이 도구는 오케스트레이션의 상태를 정확히 표시하기 때문에 디버깅에 유용합니다. 또한 큐의 메시지를 검사하여 보류 중인 작업(경우에 따라 중지)을 파악할 수도 있습니다.

> [!WARNING]
> Table Storage의 실행 기록을 확인하는 것이 편리하지만 이 테이블에 대한 종속성은 사용하지 마세요. 지속성 함수 확장이 진화함에 따라 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Learn more about monitoring in Azure Functions](../functions-monitoring.md)
