---
title: Azure Functions Durable Functions 확장의 함수 형식 및 기능
description: Azure Functions의 Durable Functions 오케스트레이션에 서 함수 간 통신을 지 원하는 함수 및 역할 형식에 대해 알아봅니다.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097751"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions 형식 및 기능 (Azure Functions)

Durable Functions은 [Azure Functions](../functions-overview.md)의 확장입니다. 함수 실행의 상태 저장 오케스트레이션에 Durable Functions를 사용할 수 있습니다. 지 속성 함수는 다양 한 Azure 함수로 구성 된 솔루션입니다. 함수는 지 속성 함수 오케스트레이션에 서 다른 역할을 수행할 수 있습니다. 

이 문서에서는 Durable Functions 오케스트레이션에 서 사용할 수 있는 함수 유형에 대 한 개요를 제공 합니다. 이 문서에는 함수를 연결 하는 데 사용할 수 있는 몇 가지 일반적인 패턴이 포함 되어 있습니다. Durable Functions 하 여 앱 개발 문제를 해결 하는 방법을 알아봅니다.

![지 속성 함수의 유형을 표시 하는 이미지입니다.][1]  

## <a name="types-of-durable-functions"></a>지속형 함수 유형

작업, orchestrator, entity 및 client의 Azure Functions 지 속성 함수 형식을 네 개 사용할 수 있습니다.

### <a name="activity-functions"></a>활동 함수

작업 함수는 영 속 함수 오케스트레이션의 기본 작업 단위입니다. 작업 함수는 프로세스에서 오케스트레이션 되는 함수 및 작업입니다. 예를 들어, 지 속성 함수를 만들어 주문을 처리할 수 있습니다. 작업에는 인벤토리 확인, 고객 요금 청구 및 배송 생성이 포함 됩니다. 각 작업은 작업 함수입니다. 

작업 함수는 작업에서 수행할 수 있는 작업 형식으로 제한 되지 않습니다. [지원 Durable Functions 하는 모든 언어로](durable-functions-overview.md#language-support)작업 함수를 작성할 수 있습니다. 지속형 작업 프레임워크는 호출된 각 활동 함수가 오케스트레이션 중에 한 번 이상 실행되도록 합니다.

활동 [트리거](durable-functions-bindings.md#activity-triggers) 를 사용 하 여 활동 함수를 트리거합니다. .NET 함수는 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 을 매개 변수로 받습니다. 트리거를 다른 개체에 바인딩하여 함수에 입력을 전달할 수도 있습니다. JavaScript에서 `<activity trigger binding name>` [ 개체`context.bindings` ](../functions-reference-node.md#bindings)의 속성을 통해 입력에 액세스할 수 있습니다.

작업 함수는 orchestrator에 값을 반환할 수도 있습니다. 작업 함수에서 많은 수의 값을 보내거나 반환 하는 경우에는 [튜플 또는 배열을](durable-functions-bindings.md#passing-multiple-parameters)사용할 수 있습니다. 오케스트레이션 인스턴스에서 작업 함수만 트리거할 수 있습니다. 작업 함수와 다른 함수 (예: HTTP 트리거 함수)가 일부 코드를 공유할 수 있지만 각 함수에는 트리거가 하나만 있을 수 있습니다.

자세한 내용 및 예제는 [활동 함수](durable-functions-bindings.md#activity-triggers)를 참조 하세요.

### <a name="orchestrator-functions"></a>오케스트레이터 함수

오 케 스트레이 터 함수는 작업이 실행 되는 방법 및 작업이 실행 되는 순서를 설명 합니다. Orchestrator 함수는C# [Durable Functions 패턴 및 기술 개념과](durable-functions-concepts.md)같이 코드 (또는 JavaScript)에서 오케스트레이션을 설명 합니다. 오케스트레이션은 [활동 함수](#activity-functions), [하위 오케스트레이션](#sub-orchestrations), [외부 이벤트 대기](#external-events)및 [타이머](#durable-timers)를 비롯 한 다양 한 유형의 작업을 포함할 수 있습니다. Orchestrator 함수도 [엔터티 함수와](#entity-functions)상호 작용할 수도 있습니다.

오케스트레이터 함수는 [오케스트레이션 트리거](durable-functions-bindings.md#orchestration-triggers)로 트리거되어야 합니다.

Orchestrator는 [orchestrator 클라이언트](#client-functions)에 의해 시작 됩니다. 모든 원본 (HTTP, 큐, 이벤트 스트림)에서 orchestrator를 트리거할 수 있습니다. 오케스트레이션의 각 인스턴스에는 인스턴스 식별자가 있습니다. 인스턴스 식별자는 자동 생성 (권장) 하거나 사용자가 생성할 수 있습니다. 인스턴스 식별자를 사용 하 여 오케스트레이션의 [인스턴스를 관리할](durable-functions-instance-management.md) 수 있습니다.

자세한 내용 및 예제는 [오케스트레이션 트리거](durable-functions-bindings.md#orchestration-triggers)를 참조 하세요.

###  <a name="entity-functions"></a>엔터티 함수 (미리 보기)

엔터티 함수는 *영 속 엔터티*라고 하는 작은 상태를 읽고 업데이트 하기 위한 작업을 정의 합니다. 오 케 스트레이 터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용 하는 함수입니다. Orchestrator 함수와 달리 entity 함수는 특정 코드 제약 조건을 포함 하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 표시 하지 않고 명시적으로 상태를 관리 합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 Durable Functions 2.0 이상 에서만 사용할 수 있습니다.

엔터티 함수에 대 한 자세한 내용은 [Entity 함수](durable-functions-preview.md#entity-functions) 미리 보기 기능 설명서를 참조 하세요.

### <a name="client-functions"></a>클라이언트 함수

클라이언트 함수는 오케스트레이션 및 엔터티 인스턴스를 만들고 관리 하는 트리거되는 함수입니다. 실제로 Durable Functions와 상호 작용 하는 진입점입니다. 모든 소스 (HTTP, 큐, 이벤트 스트림 등)에서 클라이언트 함수를 트리거할 수 있습니다. 클라이언트 함수는 [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client) 사용 하 여 내구성이 있는 오케스트레이션 및 엔터티를 만들고 관리 합니다.

클라이언트 함수의 가장 기본적인 예는 오 케 스트레이 터 함수를 시작 하는 HTTP 트리거 함수 이며 상태 확인 응답을 반환 합니다. 예제는 [HTTP API URL 검색](durable-functions-http-api.md#http-api-url-discovery)을 참조 하세요.

자세한 내용 및 예제는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client)를 참조 하세요.

## <a name="features-and-patterns"></a>기능 및 패턴

다음 섹션에서는 Durable Functions 형식의 기능과 패턴에 대해 설명 합니다.

### <a name="sub-orchestrations"></a>하위 오케스트레이션

오 케 스트레이 터 함수는 작업 함수를 호출할 수 있지만 다른 orchestrator 함수를 호출할 수도 있습니다. 예를 들어 오케스트레이터 함수 라이브러리에서 더 큰 오케스트레이션을 작성할 수 있습니다. 또는 오 케 스트레이 터 함수의 여러 인스턴스를 병렬로 실행할 수 있습니다.

자세한 내용 및 예제는 [하위 오케스트레이션](durable-functions-sub-orchestrations.md)을 참조 하세요.

### <a name="durable-timers"></a>지속형 타이머

[Durable Functions](durable-functions-overview.md) 는 오 케 스트레이 터 함수에서 지연을 구현 하거나 비동기 작업에 시간 제한을 설정 하는 데 사용할 수 있는 지 *속성 타이머* 를 제공 합니다. `Thread.Sleep` C#및 ()`setTimeout()` 또는 및`setInterval()` (JavaScript) 대신 orchestrator 함수에서 지 속성 타이머를 사용 합니다. `Task.Delay`

자세한 내용 및 예제는 지 [속성 타이머](durable-functions-timers.md)를 참조 하세요.

### <a name="external-events"></a>외부 이벤트

오케스트레이터 함수는 외부 이벤트가 오케스트레이션 인스턴스를 업데이트할 때까지 대기할 수 있습니다. 이 Durable Functions 기능은 종종 인간 상호 작용 또는 다른 외부 콜백을 처리 하는 데 유용 합니다.

자세한 내용 및 예제는 [외부 이벤트](durable-functions-external-events.md)를 참조 하세요.

### <a name="error-handling"></a>오류 처리

코드를 사용 하 여 Durable Functions 오케스트레이션을 구현 합니다. 프로그래밍 언어의 오류 처리 기능을 사용할 수 있습니다. 오케스트레이션의 작업과 `try` 같은 / 패턴`catch` . 

Durable Functions는 기본 제공 재시도 정책도 제공 합니다. 작업은 예외가 발생 하는 경우 작업을 지연 하 고 자동으로 다시 시도할 수 있습니다. 오케스트레이션을 포기 하지 않고 다시 시도를 사용 하 여 일시적인 예외를 처리할 수 있습니다.

자세한 내용 및 예제는 [오류 처리](durable-functions-error-handling.md)를 참조 하세요.

### <a name="cross-function-app-communication"></a>함수 앱 간 통신

내구성이 있는 오케스트레이션은 단일 함수 앱의 컨텍스트에서 실행 되지만 여러 함수 앱에서 오케스트레이션을 조정 하는 패턴을 사용할 수 있습니다. 응용 프로그램 간 통신은 HTTP를 통해 발생할 수 있지만 각 작업에 지 속성 프레임 워크를 사용 하는 것은 여전히 두 개의 앱에서 지속 되는 프로세스를 유지 관리할 수 있음을 의미 합니다.

다음 예제에서는 및 JavaScript의 C# 함수 간 앱 오케스트레이션을 보여 줍니다. 각 예제에서 작업 하나는 외부 오케스트레이션을 시작 합니다. 다른 작업은 상태를 검색 하 고 반환 합니다. Orchestrator는 상태 `Complete` 를 계속 하기 전에 대기 합니다.

함수 간 앱 오케스트레이션의 몇 가지 예는 다음과 같습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>다음 단계

시작 하려면 또는 [C#](durable-functions-create-first-csharp.md) [JavaScript](quickstart-js-vscode.md)에서 첫 번째 내구성이 있는 함수를 만드세요.

> [!div class="nextstepaction"]
> [자세한 내용은 Durable Functions를 참조 하세요.](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
