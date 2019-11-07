---
title: 지속성 함수의 버전 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 버전 관리를 구현하는 방법을 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4b4e82acbd3037c70b87731c0661605041090435
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614522"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>지속성 함수의 버전 관리(Azure Functions)

함수는 애플리케이션의 수명 주기 동안 반드시 추가, 제거 및 변경됩니다. [지속성 함수](durable-functions-overview.md)는 이전에 가능하지 않았던 방식으로 체이닝 함수를 함께 사용할 수 있으며, 이 체이닝은 버전 관리를 처리하는 방법에 영향을 줍니다.

## <a name="how-to-handle-breaking-changes"></a>주요 변경 내용을 처리하는 방법

주요 변경 내용을 인식할 수 있는 몇 가지 예제가 있습니다. 이 문서에서는 가장 일반적인 예제에 대해 설명합니다. 이들 모두의 기본 주제는 함수 코드의 변경으로 인해 새 함수와 기존 함수의 오케스트레이션이 모두 영향을 받는다는 것입니다.

### <a name="changing-activity-or-entity-function-signatures"></a>활동 또는 엔터티 함수 서명 변경

시그니처 변경은 함수의 이름, 입력 또는 출력의 변경을 나타냅니다. 이러한 종류의 변경 내용이 활동 또는 엔터티 함수에 적용 되는 경우이에 종속 된 오 케 스트레이 터 함수를 중단할 수 있습니다. 이 변경에 따라 오케스트레이터 함수를 업데이트하면 진행 중인 기존 인스턴스를 손상시킬 수 있습니다.

예를 들어 다음 오 케 스트레이 터 함수가 있다고 가정 합니다.

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
> 위의 C# 예제는 Durable Functions 2.x를 대상으로 합니다. 1\.x Durable Functions의 경우 `IDurableOrchestrationContext`대신 `DurableOrchestrationContext`를 사용 해야 합니다. 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

이 변경은 오케스트레이터 함수의 새 인스턴스 모두에서 정상적으로 작동하지만 진행 중인 모든 인스턴스를 중단시킵니다. 예를 들어 오케스트레이션 인스턴스가 `Foo`이라는 함수를 호출 하 고, 부울 값을 가져온 다음, 검사점을 반환 하는 경우를 고려해 보겠습니다. 이 시점에서 시그니처 변경이 배포되면 검사점이 설정된 인스턴스는 다시 시작되고 `context.CallActivityAsync<int>("Foo")`에 대한 호출을 재생할 때 즉시 실패합니다. 이 오류는 기록 테이블의 결과가 `bool` 되었지만 새 코드에서 `int`로 deserialize 하려고 시도 하기 때문에 발생 합니다.

이 예제는 서명 변경으로 인해 기존 인스턴스가 중단 될 수 있는 여러 가지 방법 중 하나일 뿐입니다. 일반적으로 오케스트레이터에서 함수를 호출하는 방식을 변경해야 하는 경우 변경이 문제가 될 수 있습니다.

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
> 위의 C# 예제는 Durable Functions 2.x를 대상으로 합니다. 1\.x Durable Functions의 경우 `IDurableOrchestrationContext`대신 `DurableOrchestrationContext`를 사용 해야 합니다. 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

이 변경은 **Foo**와 **Bar** 간의 **SendNotification**에 새 함수 호출을 추가합니다. 시그니처 변경은 없습니다. 문제는 **Bar**에 대한 호출에서 기존 인스턴스가 다시 시작될 때 발생합니다. 재생 하는 동안 **Foo** 에 대 한 원래 호출이 `true`반환 되 면 orchestrator replay는 실행 기록에 없는 **sendnotification**을 호출 합니다. 결과적으로 지속성 작업 프레임워크는 `NonDeterministicOrchestrationException`Bar**에 대한 호출을 예상했지만** SendNotification**에 대한 호출을 발견했기 때문에** 과 함께 실패합니다. `CreateTimer`, `WaitForExternalEvent`등을 포함 하 여 "내구성이 있는" Api에 대 한 호출을 추가할 때 동일한 유형의 문제가 발생할 수 있습니다.

## <a name="mitigation-strategies"></a>완화 전략

버전 관리 문제를 처리하기 위한 몇 가지 전략은 다음과 같습니다.

* 아무 작업도 수행하지 않음
* 진행 중인 모든 인스턴스 중지
* 병렬 배포

### <a name="do-nothing"></a>아무 작업도 수행하지 않음

주요 변경을 처리하는 가장 쉬운 방법은 진행 중인 오케스트레이션 인스턴스를 실패하게 만드는 것입니다. 새 인스턴스가 변경된 코드를 성공적으로 실행합니다.

이러한 종류의 실패가 문제 인지 여부는 진행 중인 인스턴스의 중요도에 따라 달라 집니다. 현재 개발 중에 있고 진행 중인 인스턴스가 중요하지 않으면 이 정도로 충분할 수 있습니다. 그러나 진단 파이프라인에서 예외 및 오류를 처리 해야 합니다. 이러한 문제를 방지하려면 다른 버전 관리 옵션을 사용하는 것이 좋습니다.

### <a name="stop-all-in-flight-instances"></a>진행 중인 모든 인스턴스 중지

다른 옵션으로 진행 중인 모든 인스턴스를 중지하는 것입니다. 내부 **제어 큐** 및 작업 **항목 큐** 큐의 콘텐츠를 지워 모든 인스턴스를 중지할 수 있습니다. 인스턴스는 계속 해 서 중단 되지만 오류 메시지를 사용 하 여 로그를 복잡 하 게 만들지 않습니다. 이 접근 방식은 신속한 프로토타입 개발에 적합 합니다.

> [!WARNING]
> 이러한 큐의 세부 정보는 시간이 지남에 따라 변경될 수 있으므로 프로덕션 작업에 이 기술을 사용하지 마세요.

### <a name="side-by-side-deployments"></a>병렬 배포

주요 변경 내용을 안전하게 배포하는 가장 확실한 방법은 이전 버전과 함께 나란히 배포하는 것입니다. 이렇게 하려면 다음 방법 중 하나를 사용하면 됩니다.

* 기존 함수를 그대로 두고 완전히 새로운 함수로 모든 업데이트를 배포 합니다. 새 함수 버전의 호출자는 동일한 지침에 따라 업데이트 해야 하기 때문에 복잡할 수 있습니다.
* 다른 스토리지 계정을 사용하는 새 함수 앱으로 모든 업데이트를 배포합니다.
* 업데이트 된 `taskHub` 이름을 가진 동일한 저장소 계정을 사용 하 여 함수 앱의 새 복사본을 배포 합니다. 병렬 배포는 권장 되는 방법입니다.

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

#### <a name="functions-20"></a>함수 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Durable Functions v1. x의 기본값은 `DurableFunctionsHub`입니다. Durable Functions v2.0부터 기본 작업 허브 이름은 Azure의 함수 앱 이름과 동일 하거나 Azure 외부에서 실행 되는 경우 `TestHubName` 합니다.

Azure Storage 엔터티의 이름은 모두 `hubName` 구성 값에 따라 지정됩니다. 작업 허브에 새 이름을 지정하면 새 버전의 애플리케이션에 대한 별도의 큐와 기록 테이블이 만들어졌는지 확인합니다. 그러나 함수 앱은 이전 작업 허브 이름으로 만든 오케스트레이션 또는 엔터티에 대 한 이벤트 처리를 중지 합니다.

새 버전의 함수 앱을 새 [배포 슬롯](../functions-deployment-slots.md)에 배포하는 것이 좋습니다. 배포 슬롯을 사용하면 슬롯 중 하나만 활성 *프로덕션* 슬롯으로 사용하여 함수 앱의 여러 복사본을 병렬로 실행할 수 있습니다. 새 오케스트레이션 논리를 기존 인프라에 노출할 준비가 되면 새 버전을 프로덕션 슬롯으로 교환하는 것처럼 간단할 수 있습니다.

> [!NOTE]
> 이 전략은 오케스트레이터 함수에 HTTP와 웹후크 트리거를 사용할 때 가장 적합합니다. 큐 또는 Event Hubs와 같은 HTTP가 아닌 트리거의 경우 트리거 정의는 교환 작업의 일부로 업데이트 된 [앱 설정에서 파생](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) 되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 및 크기 조정 문제 처리](durable-functions-perf-and-scale.md)
