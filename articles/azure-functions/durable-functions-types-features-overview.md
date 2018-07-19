---
title: 지속형 함수의 함수 유형 및 기능에 대한 개요 - Azure
description: 지속형 함수 오케스트레이션의 일부로 함수 간 통신을 허용하는 함수 및 역할 유형을 알아봅니다.
services: functions
author: jeffhollan
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: dfcf52c6d8d5c04e15d653376f52107fc4776d02
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973155"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>지속형 함수의 함수 유형 및 기능에 대한 개요(Azure Functions)

Azure 지속형 함수는 함수 실행의 상태 저장 오케스트레이션을 제공합니다.  지속형 함수는 다양한 Azure Functions로 구성된 솔루션입니다.  이러한 각 함수는 오케스트레이션의 일부로 서로 다른 역할을 수행할 수 있습니다.  다음 문서에서는 지속형 함수 오케스트레이션에 관련된 함수 유형에 대한 개요를 제공합니다.  함수 연결의 몇 가지 일반적인 패턴도 포함되어 있습니다.  

![지속형 함수 유형][1]  

## <a name="types-of-functions"></a>함수 유형

### <a name="activity-functions"></a>활동 함수

활동 함수는 지속적인 오케스트레이션의 기본 작업 단위입니다.  활동 함수는 프로세스에서 오케스트레이션되는 함수 및 작업입니다.  예를 들어, 재고 확인, 고객 청구, 배송 생성 등 주문을 처리하는 지속형 함수를 만들 수 있습니다.  이러한 각 작업이 활동 함수입니다.  활동 함수에서 수행할 수 있는 작업 유형에는 제한이 없습니다.  Azure Functions에서 지원하는 모든 언어로 작성할 수 있습니다.  지속형 작업 프레임워크는 호출된 각 활동 함수가 오케스트레이션 중에 한 번 이상 실행되도록 합니다.

활동 함수는 [작업 트리거](durable-functions-bindings.md#activity-triggers)에 의해 트리거되어야 합니다.  이 함수는 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)를 매개 변수로 받습니다. 트리거를 다른 개체에 바인딩하여 함수에 입력을 전달할 수도 있습니다.  활동 함수가 오케스트레이터에게 값을 반환할 수도 있습니다.  활동 함수에서 많은 값을 보내거나 반환하는 경우, [튜플 또는 배열을 활용](durable-functions-bindings.md#passing-multiple-parameters)할 수 있습니다.  활동 함수는 오케스트레이션 인스턴스에서만 트리거할 수 있습니다.  일부 코드가 활동 함수와 다른 함수(예: HTTP 트리거 함수) 간에 공유될 수도 있지만, 각 함수에 트리거는 한 개만 사용할 수 있습니다.

자세한 정보 및 예제는 [지속형 함수 바인딩 문서](durable-functions-bindings.md#activity-triggers)에서 확인할 수 있습니다.

### <a name="orchestrator-functions"></a>오케스트레이터 함수

오케스트레이터 함수는 지속형 함수의 핵심입니다.  오케스트레이터 함수는 작업이 실행되는 방식과 순서를 설명합니다.  오케스트레이터 함수는 [지속형 함수 개요](durable-functions-overview.md)에 표시된 대로, 코드(C# 또는 JavaScript)에서 오케스트레이션을 설명합니다.  오케스트레이션에는 [활동 함수](#activity-functions), [하위 오케스트레이션](#sub-orchestrations), [외부 이벤트 대기](#external-events) 및 [타이머](#durable-timers)와 같은 다양한 유형의 작업이 포함될 수 있습니다.  

오케스트레이터 함수는 [오케스트레이션 트리거](durable-functions-bindings.md#orchestration-triggers)로 트리거되어야 합니다.

오케스트레이터는 임의 소스(HTTP, 큐, 이벤트 스트림)에서 트리거될 수 있는 [오케스트레이터 클라이언트](#client-functions)에 의해 시작됩니다.  오케스트레이션의 각 인스턴스에는 자동 생성(권장)되거나 사용자가 생성할 수 있는 인스턴스 식별자가 있습니다.  이 식별자를 사용하여 오케스트레이션 [인스턴스를 관리](durable-functions-instance-management.md)할 수 있습니다.

자세한 정보 및 예제는 [지속형 함수 바인딩 문서](durable-functions-bindings.md#orchestration-triggers)에서 확인할 수 있습니다.

### <a name="client-functions"></a>클라이언트 함수

클라이언트 함수는 오케스트레이션의 새 인스턴스를 만드는 트리거된 함수입니다.  지속형 오케스트레이션 인스턴스를 만들기 위한 진입점입니다.  클라이언트 함수는 임의 트리거(HTTP, 큐, 이벤트 스트림 등)에 의해 트리거될 수 있으며, 앱에서 지원하는 모든 언어로 작성할 수 있습니다.  트리거 외에도 클라이언트 함수에는 지속형 오케스트레이션을 만들고 관리할 수 있게 해주는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 바인딩이 있습니다.  클라이언트 함수의 가장 기본적인 예로, [다음 예제와 같이](durable-functions-http-api.md#http-api-url-discovery) 오케스트레이터 함수를 시작하고 검사 상태 응답을 반환하는 HTTP 트리거 함수가 있습니다.

자세한 정보 및 예제는 [지속형 함수 바인딩 문서](durable-functions-bindings.md#orchestration-client)에서 확인할 수 있습니다.

## <a name="features-and-patterns"></a>기능 및 패턴

### <a name="sub-orchestrations"></a>하위 오케스트레이션

오케스트레이터 함수는 활동 함수를 호출하는 것 외에도 다른 오케스트레이터 함수를 호출할 수 있습니다. 예를 들어 오케스트레이터 함수 라이브러리에서 더 큰 오케스트레이션을 작성할 수 있습니다. 또는 오케스트레이터 함수의 여러 인스턴스를 병렬로 실행할 수 있습니다.

자세한 정보 및 예제는 [하위 오케스트레이션 문서](durable-functions-sub-orchestrations.md)에서 확인할 수 있습니다.

### <a name="durable-timers"></a>지속형 타이머

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머*를 제공합니다. 지속성 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#) 또는 `setTimeout()` 및 `setInterval()`(JavaScript) 대신, 오케스트레이터 함수에 사용해야 합니다.

지속형 타이머에 대한 자세한 정보 및 예제는 [지속형 타이머 문서](durable-functions-timers.md)에서 확인할 수 있습니다.

### <a name="external-events"></a>외부 이벤트

오케스트레이터 함수는 외부 이벤트가 오케스트레이션 인스턴스를 업데이트할 때까지 대기할 수 있습니다. 지속형 함수의 이 기능은 종종 사람의 조작이나 다른 외부 콜백을 처리하는 데 유용합니다.

자세한 정보 및 예제는 [외부 이벤트 문서](durable-functions-external-events.md)에서 확인할 수 있습니다.

### <a name="error-handling"></a>오류 처리

지속형 함수 오케스트레이션은 코드로 구현되며, 프로그래밍 언어의 오류 처리 기능을 사용할 수 있습니다.  이는 “try/catch”와 같은 패턴이 오케스트레이션에서 작동함을 의미합니다.  지속형 함수와 함께 몇 가지 기본 제공 재시도 정책도 제공됩니다.  예외가 발생할 경우, 동작이 자동으로 작업을 지연하고 다시 시도할 수 있습니다.  재시도를 통해 오케스트레이션을 중단하지 않고 일시적인 예외를 처리할 수 있습니다.

자세한 정보 및 예제는 [오류 처리 문서](durable-functions-error-handling.md)에서 확인할 수 있습니다.

### <a name="cross-function-app-communication"></a>함수 앱 간 통신

지속형 오케스트레이션은 일반적으로 단일 함수 앱의 컨텍스트에서 사용되지만, 다양한 함수 앱에서 오케스트레이션을 조정할 수 있게 해주는 패턴이 있습니다.  HTTP를 통해 앱 간 통신이 발생할 수도 있지만, 각 작업에 대해 지속형 프레임워크를 사용하면 두 앱 간에 지속형 프로세스를 유지 관리할 수 있습니다.

아래에는 C#으로 작성된 함수 앱 간 오케스트레이션의 예가 나와 있습니다.  한 작업이 외부 오케스트레이션을 시작합니다. 그런 다음, 다른 작업이 상태를 검색하고 반환합니다.  오케스트레이터는 계속하기 전에 상태가 완료될 때까지 대기합니다.

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 함수 설명서 참조 계속](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [지속성 함수 확장 및 샘플 설치](durable-functions-install.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png