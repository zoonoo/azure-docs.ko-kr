---
title: "지속성 함수의 단일 항목 - Azure"
description: "Azure Functions의 지속성 함수 확장에 단일 항목을 사용하는 방법입니다."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>지속성 함수의 단일 항목 오케스트레이터(Azure Functions)

백그라운드 작업 또는 작업자 스타일 오케스트레이션의 경우 한 번에 하나의 특정 오케스트레이터 인스턴스만 실행되도록 해야 합니다. 생성할 때 오케스트레이터에 특정 인스턴스 ID를 할당하여 [지속성 함수](durable-functions-overview.md)에서 수행할 수 있습니다.

## <a name="singleton-example"></a>단일 항목 예제

다음 C# 예제에서는 단일 항목 백그라운드 작업 오케스트레이션을 만드는 HTTP 트리거 함수를 보여 줍니다. 하나의 인스턴스만 존재하도록 하기 위해 잘 알려진 인스턴스 ID를 사용합니다.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

기본적으로 인스턴스 ID는 임의로 GUID에서 생성됩니다. 그러나 이 경우 트리거 함수는 `MySingletonInstanceId`의 값으로 미리 정의된 `InstanceId` 변수를 사용하여 오케스트레이터 함수에 인스턴스 ID를 미리 할당합니다. 이렇게 하면 트리거는 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_)를 호출하여 잘 알려진 인스턴스가 이미 실행 중인지 여부를 확인할 수 있습니다.

오케스트레이터 함수의 구현 세부 정보는 실제로 중요하지 않습니다. 시작하고 완료하는 일반 오케스트레이터 함수일 수 있거나 무기한 실행하는 오케스트레이터 함수일 수 있습니다(즉, [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)). 중요한 점은 한 번에 하나의 인스턴스만 실행된다는 점입니다.

> [!NOTE]
> 단일 항목 오케스트레이션 인스턴스가 종료하고, 실패하거나 완료하는 경우 동일한 ID를 사용하여 다시 만들 수 없습니다. 이러한 경우 새 인스턴스 ID를 사용하여 다시 만들도록 준비해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하위 오케스트레이션을 호출하는 방법 알아보기](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [샘플 단일 항목 실행](durable-functions-counter.md)
