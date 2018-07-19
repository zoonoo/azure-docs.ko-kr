---
title: 지속성 함수의 단일 항목 - Azure
description: Azure Functions의 지속성 함수 확장에 단일 항목을 사용하는 방법입니다.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 71c0cebf676d29308fe9f4942350ae96d3bedcf6
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340834"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>지속성 함수의 단일 항목 오케스트레이터(Azure Functions)

백그라운드 작업 또는 작업자 스타일 오케스트레이션의 경우 한 번에 하나의 특정 오케스트레이터 인스턴스만 실행되도록 해야 합니다. 생성할 때 오케스트레이터에 특정 인스턴스 ID를 할당하여 [지속성 함수](durable-functions-overview.md)에서 수행할 수 있습니다.

## <a name="singleton-example"></a>단일 항목 예제

다음 C# 예제에서는 단일 항목 백그라운드 작업 오케스트레이션을 만드는 HTTP 트리거 함수를 보여 줍니다. 이 코드는 지정한 인스턴스 ID에 대해 인스턴스가 하나만 존재하는지 확인합니다.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

기본적으로 인스턴스 ID는 임의로 GUID에서 생성됩니다. 하지만 이 경우 인스턴스 ID가 URL에서 경로 데이터에 전달됩니다. 이 코드는 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_)를 호출하여 지정된 ID를 갖는 인스턴스가 이미 실행되고 있는지 확인합니다. 실행되고 있지 않으면 해당 ID를 사용하여 인스턴스가 만들어집니다.

> [!NOTE]
> 이 샘플에는 잠재적 경합 상태가 있습니다. **HttpStartSingle**의 두 인스턴스가 올바르게 실행되는 경우 결과는 싱글톤의 다르게 만든 두 개 인스턴스로서 한 인스턴스가 다른 것을 덮어쓸 수 있습니다. 요구 사항에 따라 바람직하지 않은 부작용이 있을 수 있습니다. 이러한 이유로 두 요청이 이 트리거 함수를 동시에 실행할 수 없도록 하는 것이 중요합니다.

오케스트레이터 함수의 구현 세부 정보는 실제로 중요하지 않습니다. 시작하고 완료하는 일반 오케스트레이터 함수일 수 있거나 무기한 실행하는 오케스트레이터 함수일 수 있습니다(즉, [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)). 중요한 점은 한 번에 하나의 인스턴스만 실행된다는 점입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하위 오케스트레이션을 호출하는 방법 알아보기](durable-functions-sub-orchestrations.md)
