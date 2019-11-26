---
title: 지속성 함수의 하위 오케스트레이션 - Azure
description: Azure Functions의 지속성 함수 확장에서 오케스트레이션을 호출하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 379f2cb238aef08faba8dd3c8e5d9da4542a1867
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231285"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>지속성 함수의 하위 오케스트레이션(Azure Functions)

오케스트레이터 함수는 활동 함수를 호출하는 것 외에도 다른 오케스트레이터 함수를 호출할 수 있습니다. 예를 들어 작은 오 케 스트레이 터 함수 라이브러리에서 더 큰 오케스트레이션을 만들 수 있습니다. 또는 오케스트레이터 함수의 여러 인스턴스를 병렬로 실행할 수 있습니다.

오 케 스트레이 터 함수는 `CallSubOrchestratorAsync` 또는 .NET의 `CallSubOrchestratorWithRetryAsync` 메서드 또는 JavaScript의 `callSubOrchestrator` 또는 `callSubOrchestratorWithRetry` 메서드를 사용 하 여 다른 orchestrator 함수를 호출할 수 있습니다. [오류 처리 및 보정](durable-functions-error-handling.md#automatic-retry-on-failure) 문서에서는 자동 다시 시도에 대해 자세히 설명하고 있습니다.

하위 오케스트레이터 함수는 호출자의 관점에서 작업 함수처럼 작동합니다. 이러한 함수는 값을 반환하고, 예외를 throw하며, 부모 오케스트레이터 함수에서 기다릴 수 있습니다. 
## <a name="example"></a>예

다음 예제에서는 프로비전해야 하는 여러 디바이스가 있는 IoT("사물 인터넷") 시나리오를 보여줍니다. 다음 함수는 각 장치에 대해 실행 해야 하는 프로 비전 워크플로를 나타냅니다.

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

이 오케스트레이터 함수는 일회용 디바이스 프로비전을 위해 그대로 사용하거나 더 큰 오케스트레이션의 일부로 포함될 수 있습니다. 후자의 경우 부모 오 케 스트레이 터 함수는 `CallSubOrchestratorAsync` (.NET) 또는 `callSubOrchestrator` (JavaScript) API를 사용 하 여 `DeviceProvisioningOrchestration` 인스턴스를 예약할 수 있습니다.

다음은 여러 오케스트레이터 함수를 병렬로 실행하는 방법을 보여 주는 예제입니다.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> 위의 C# 예는 Durable Functions 2.x에 대 한 것입니다. 1\.x Durable Functions의 경우 `IDurableOrchestrationContext`대신 `DurableOrchestrationContext`를 사용 해야 합니다. 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

> [!NOTE]
> 하위 오케스트레이션은 부모 오케스트레이션과 동일한 함수 앱에서 정의 되어야 합니다. 를 호출 하 고 다른 함수 앱에서 오케스트레이션을 대기 해야 하는 경우 HTTP Api 및 HTTP 202 폴링 소비자 패턴에 대 한 기본 제공 지원을 사용 하는 것이 좋습니다. 자세한 내용은 [HTTP 기능](durable-functions-http-features.md) 항목을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 오케스트레이션 상태를 설정 하는 방법 알아보기](durable-functions-custom-orchestration-status.md)