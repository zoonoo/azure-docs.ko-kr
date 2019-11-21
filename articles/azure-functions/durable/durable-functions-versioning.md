---
title: 지속성 함수의 버전 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 버전 관리를 구현하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232751"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>지속성 함수의 버전 관리(Azure Functions)

함수는 애플리케이션의 수명 주기 동안 반드시 추가, 제거 및 변경됩니다. [지속성 함수](durable-functions-overview.md)는 이전에 가능하지 않았던 방식으로 체이닝 함수를 함께 사용할 수 있으며, 이 체이닝은 버전 관리를 처리하는 방법에 영향을 줍니다.

## <a name="how-to-handle-breaking-changes"></a>주요 변경 내용을 처리하는 방법

주요 변경 내용을 인식할 수 있는 몇 가지 예제가 있습니다. 이 문서에서는 가장 일반적인 예제에 대해 설명합니다. 이들 모두의 기본 주제는 함수 코드의 변경으로 인해 새 함수와 기존 함수의 오케스트레이션이 모두 영향을 받는다는 것입니다.

### <a name="changing-activity-or-entity-function-signatures"></a>Changing activity or entity function signatures

시그니처 변경은 함수의 이름, 입력 또는 출력의 변경을 나타냅니다. If this kind of change is made to an activity or entity function, it could break any orchestrator function that depends on it. 이 변경에 따라 오케스트레이터 함수를 업데이트하면 진행 중인 기존 인스턴스를 손상시킬 수 있습니다.

As an example, suppose we have the following orchestrator function.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이 간단한 함수는 **Foo**의 결과를 받아 **Bar**로 전달합니다. 더 다양한 결과 값을 지원하기 위해 **Foo**의 반환 값을 `bool`에서 `int`로 변경할 필요가 있다고 가정해 보겠습니다. 결과는 다음과 유사하게 표시됩니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

이 변경은 오케스트레이터 함수의 새 인스턴스 모두에서 정상적으로 작동하지만 진행 중인 모든 인스턴스를 중단시킵니다. For example, consider the case where an orchestration instance calls a function named `Foo`, gets back a boolean value, and then checkpoints. 이 시점에서 시그니처 변경이 배포되면 검사점이 설정된 인스턴스는 다시 시작되고 `context.CallActivityAsync<int>("Foo")`에 대한 호출을 재생할 때 즉시 실패합니다. This failure happens because the result in the history table is `bool` but the new code tries to deserialize it into `int`.

This example is just one of many different ways that a signature change can break existing instances. 일반적으로 오케스트레이터에서 함수를 호출하는 방식을 변경해야 하는 경우 변경이 문제가 될 수 있습니다.

### <a name="changing-orchestrator-logic"></a>오케스트레이터 논리 변경

다른 종류의 버전 관리 문제는 진행 중인 인스턴스의 재생 논리에 혼란을 주는 방식으로 오케스트레이터 함수 코드를 변경하는 것입니다.

다음 오케스트레이터 함수를 살펴보세요.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이제 다른 함수 호출을 추가하기 위해 겉으로는 문제가 없는 변경을 수행하려고 한다고 가정해 보겠습니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

이 변경은 **Foo**와 **Bar** 간의 **SendNotification**에 새 함수 호출을 추가합니다. 시그니처 변경은 없습니다. 문제는 **Bar**에 대한 호출에서 기존 인스턴스가 다시 시작될 때 발생합니다. During replay, if the original call to **Foo** returned `true`, then the orchestrator replay will call into **SendNotification**, which is not in its execution history. 결과적으로 지속성 작업 프레임워크는 **Bar**에 대한 호출을 예상했지만 **SendNotification**에 대한 호출을 발견했기 때문에 `NonDeterministicOrchestrationException`과 함께 실패합니다. The same type of problem can occur when adding any calls to "durable" APIs, including `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>완화 전략

버전 관리 문제를 처리하기 위한 몇 가지 전략은 다음과 같습니다.

* 아무 작업도 수행하지 않음
* 진행 중인 모든 인스턴스 중지
* 병렬 배포

### <a name="do-nothing"></a>아무 작업도 수행하지 않음

주요 변경을 처리하는 가장 쉬운 방법은 진행 중인 오케스트레이션 인스턴스를 실패하게 만드는 것입니다. 새 인스턴스가 변경된 코드를 성공적으로 실행합니다.

Whether this kind of failure is a problem depends on the importance of your in-flight instances. 현재 개발 중에 있고 진행 중인 인스턴스가 중요하지 않으면 이 정도로 충분할 수 있습니다. However, you'll need to deal with exceptions and errors in your diagnostics pipeline. 이러한 문제를 방지하려면 다른 버전 관리 옵션을 사용하는 것이 좋습니다.

### <a name="stop-all-in-flight-instances"></a>진행 중인 모든 인스턴스 중지

다른 옵션으로 진행 중인 모든 인스턴스를 중지하는 것입니다. Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. The instances will be forever stuck where they are, but they will not clutter your logs with failure messages. This approach is ideal in rapid prototype development.

> [!WARNING]
> 이러한 큐의 세부 정보는 시간이 지남에 따라 변경될 수 있으므로 프로덕션 작업에 이 기술을 사용하지 마세요.

### <a name="side-by-side-deployments"></a>병렬 배포

주요 변경 내용을 안전하게 배포하는 가장 확실한 방법은 이전 버전과 함께 나란히 배포하는 것입니다. 이렇게 하려면 다음 방법 중 하나를 사용하면 됩니다.

* Deploy all the updates as entirely new functions, leaving existing functions as-is. This can be tricky because the callers of the new function versions must be updated as well following the same guidelines.
* 다른 스토리지 계정을 사용하는 새 함수 앱으로 모든 업데이트를 배포합니다.
* Deploy a new copy of the function app with the same storage account but with an updated `taskHub` name. Side-by-side deployments is the recommended technique.

### <a name="how-to-change-task-hub-name"></a>작업 허브 이름을 변경하는 방법

작업 허브는 *host.json* 파일에서 다음과 같이 구성할 수 있습니다.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

The default value for Durable Functions v1.x is `DurableFunctionsHub`. Starting in Durable Functions v2.0, the default task hub name is the same as the function app name in Azure, or `TestHubName` if running outside of Azure.

Azure Storage 엔터티의 이름은 모두 `hubName` 구성 값에 따라 지정됩니다. 작업 허브에 새 이름을 지정하면 새 버전의 애플리케이션에 대한 별도의 큐와 기록 테이블이 만들어졌는지 확인합니다. The function app, however, will stop processing events for orchestrations or entities created under the previous task hub name.

새 버전의 함수 앱을 새 [배포 슬롯](../functions-deployment-slots.md)에 배포하는 것이 좋습니다. 배포 슬롯을 사용하면 슬롯 중 하나만 활성 *프로덕션* 슬롯으로 사용하여 함수 앱의 여러 복사본을 병렬로 실행할 수 있습니다. 새 오케스트레이션 논리를 기존 인프라에 노출할 준비가 되면 새 버전을 프로덕션 슬롯으로 교환하는 것처럼 간단할 수 있습니다.

> [!NOTE]
> 이 전략은 오케스트레이터 함수에 HTTP와 웹후크 트리거를 사용할 때 가장 적합합니다. For non-HTTP triggers, such as queues or Event Hubs, the trigger definition should [derive from an app setting](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) that gets updated as part of the swap operation.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 및 크기 조정 문제 처리](durable-functions-perf-and-scale.md)
