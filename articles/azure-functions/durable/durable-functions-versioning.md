---
title: 지속성 함수의 버전 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 버전 관리를 구현하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232751"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>지속성 함수의 버전 관리(Azure Functions)

함수는 애플리케이션의 수명 주기 동안 반드시 추가, 제거 및 변경됩니다. [지속성 함수](durable-functions-overview.md)는 이전에 가능하지 않았던 방식으로 체이닝 함수를 함께 사용할 수 있으며, 이 체이닝은 버전 관리를 처리하는 방법에 영향을 줍니다.

## <a name="how-to-handle-breaking-changes"></a>주요 변경 내용을 처리하는 방법

주요 변경 내용을 인식할 수 있는 몇 가지 예제가 있습니다. 이 문서에서는 가장 일반적인 예제에 대해 설명합니다. 이들 모두의 기본 주제는 함수 코드의 변경으로 인해 새 함수와 기존 함수의 오케스트레이션이 모두 영향을 받는다는 것입니다.

### <a name="changing-activity-or-entity-function-signatures"></a>활동 또는 엔터티 함수 서명 변경

시그니처 변경은 함수의 이름, 입력 또는 출력의 변경을 나타냅니다. 이러한 종류의 변경이 활동 또는 엔터티 함수에 대해 이루어지면 해당 함수에 종속된 모든 오케스트레이터 함수가 중단될 수 있습니다. 이 변경에 따라 오케스트레이터 함수를 업데이트하면 진행 중인 기존 인스턴스를 손상시킬 수 있습니다.

예를 들어 다음과 같은 오케스트레이터 함수가 있다고 가정합니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이 간단한 함수는 **Foo**의 결과를 받아 **Bar**로 전달합니다. 더 다양한 결과 값을 지원하기 위해 **Foo**의 반환 값을 `bool`에서 `int`로 변경할 필요가 있다고 가정해 보겠습니다. 결과는 다음과 같습니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 이전 C# 예제는 내구성 함수를 2.x 대상으로 지정합니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

이 변경은 오케스트레이터 함수의 새 인스턴스 모두에서 정상적으로 작동하지만 진행 중인 모든 인스턴스를 중단시킵니다. 예를 들어 오케스트레이션 인스턴스가 명명된 `Foo`함수를 호출하고 부울 값을 다시 가져옵니다. 이 시점에서 시그니처 변경이 배포되면 검사점이 설정된 인스턴스는 다시 시작되고 `context.CallActivityAsync<int>("Foo")`에 대한 호출을 재생할 때 즉시 실패합니다. 이 오류는 `bool` 기록 테이블의 결과가 있지만 새 코드가 `int`로 역직렬화하려고 시도하기 때문에 발생합니다.

이 예제는 서명 변경으로 기존 인스턴스를 중단시킬 수 있는 여러 가지 방법 중 하나일 뿐입니다. 일반적으로 오케스트레이터에서 함수를 호출하는 방식을 변경해야 하는 경우 변경이 문제가 될 수 있습니다.

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
> 이전 C# 예제는 내구성 함수를 2.x 대상으로 지정합니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

이 변경은 **Foo**와 **Bar** 간의 **SendNotification**에 새 함수 호출을 추가합니다. 시그니처 변경은 없습니다. 문제는 **Bar**에 대한 호출에서 기존 인스턴스가 다시 시작될 때 발생합니다. 재생 중에 **Foo에** 대한 원래 `true`호출이 반환되면 오케스트레이터 재생이 실행 기록에 없는 **SendNotification를**호출합니다. 결과적으로 지속성 작업 프레임워크는 **Bar**에 대한 호출을 예상했지만 **SendNotification**에 대한 호출을 발견했기 때문에 `NonDeterministicOrchestrationException`과 함께 실패합니다. 을 `CreateTimer` `WaitForExternalEvent`포함하여 "지속가능한" API에 호출을 추가할 때 동일한 유형의 문제가 발생할 수 있습니다.

## <a name="mitigation-strategies"></a>완화 전략

버전 관리 문제를 처리하기 위한 몇 가지 전략은 다음과 같습니다.

* 아무 작업도 수행하지 않음
* 진행 중인 모든 인스턴스 중지
* 병렬 배포

### <a name="do-nothing"></a>아무 작업도 수행하지 않음

주요 변경을 처리하는 가장 쉬운 방법은 진행 중인 오케스트레이션 인스턴스를 실패하게 만드는 것입니다. 새 인스턴스가 변경된 코드를 성공적으로 실행합니다.

이러한 종류의 실패가 문제인지 여부는 기내 인스턴스의 중요성에 따라 달라집니다. 현재 개발 중에 있고 진행 중인 인스턴스가 중요하지 않으면 이 정도로 충분할 수 있습니다. 그러나 진단 파이프라인의 예외 및 오류를 처리해야 합니다. 이러한 문제를 방지하려면 다른 버전 관리 옵션을 사용하는 것이 좋습니다.

### <a name="stop-all-in-flight-instances"></a>진행 중인 모든 인스턴스 중지

다른 옵션으로 진행 중인 모든 인스턴스를 중지하는 것입니다. 내부 **제어 큐** 및 **작업 항목 큐** 큐의 내용을 지우면 모든 인스턴스를 중지할 수 있습니다. 인스턴스는 현재 위치에 영원히 붙어 있지만 실패 메시지로 로그를 복잡하게 하지는 않습니다. 이 접근 방식은 신속한 프로토타입 개발에 이상적입니다.

> [!WARNING]
> 이러한 큐의 세부 정보는 시간이 지남에 따라 변경될 수 있으므로 프로덕션 작업에 이 기술을 사용하지 마세요.

### <a name="side-by-side-deployments"></a>병렬 배포

주요 변경 내용을 안전하게 배포하는 가장 확실한 방법은 이전 버전과 함께 나란히 배포하는 것입니다. 이렇게 하려면 다음 방법 중 하나를 사용하면 됩니다.

* 모든 업데이트를 완전히 새로운 함수로 배포하여 기존 기능을 있는 그대로 유지합니다. 새 함수 버전의 호출자는 동일한 지침에 따라 업데이트해야 하기 때문에 이 문제는 까다로울 수 있습니다.
* 다른 스토리지 계정을 사용하는 새 함수 앱으로 모든 업데이트를 배포합니다.
* 동일한 저장소 계정이지만 업데이트된 `taskHub` 이름으로 함수 앱의 새 복사본을 배포합니다. 나란히 배포하는 것이 권장되는 기술입니다.

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

지속 함수 v1.x의 기본값은 `DurableFunctionsHub`. 지속적 함수 v2.0에서 시작하는 기본 작업 허브 이름은 Azure의 함수 앱 `TestHubName` 이름과 동일하거나 Azure 외부에서 실행되는 경우입니다.

Azure Storage 엔터티의 이름은 모두 `hubName` 구성 값에 따라 지정됩니다. 작업 허브에 새 이름을 지정하면 새 버전의 애플리케이션에 대한 별도의 큐와 기록 테이블이 만들어졌는지 확인합니다. 그러나 함수 앱은 이전 작업 허브 이름으로 만든 오케스트레이션 또는 엔터티에 대한 이벤트 처리를 중지합니다.

새 버전의 함수 앱을 새 [배포 슬롯](../functions-deployment-slots.md)에 배포하는 것이 좋습니다. 배포 슬롯을 사용하면 슬롯 중 하나만 활성 *프로덕션* 슬롯으로 사용하여 함수 앱의 여러 복사본을 병렬로 실행할 수 있습니다. 새 오케스트레이션 논리를 기존 인프라에 노출할 준비가 되면 새 버전을 프로덕션 슬롯으로 교환하는 것처럼 간단할 수 있습니다.

> [!NOTE]
> 이 전략은 오케스트레이터 함수에 HTTP와 웹후크 트리거를 사용할 때 가장 적합합니다. 큐 또는 이벤트 허브와 같은 비 HTTP 트리거의 경우 트리거 정의는 스왑 작업의 일부로 업데이트되는 [앱 설정에서 파생되어야](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 및 크기 조정 문제 처리](durable-functions-perf-and-scale.md)
