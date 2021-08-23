---
title: 지속성 함수의 하위 오케스트레이션 - Azure
description: Azure Functions의 지속성 함수 확장에서 오케스트레이션을 호출하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0f27b19b101c997e002f6cf60b7cab988e3cbed4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457716"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>지속성 함수의 하위 오케스트레이션(Azure Functions)

오케스트레이터 함수는 활동 함수를 호출하는 것 외에도 다른 오케스트레이터 함수를 호출할 수 있습니다. 예를 들어 더 작은 오케스트레이터 함수 라이브러리에서 더 큰 오케스트레이션을 빌드할 수 있습니다. 또는 오케스트레이터 함수의 여러 인스턴스를 병렬로 실행할 수 있습니다.

오케스트레이터 함수는 .NET의 `CallSubOrchestratorAsync` 또는 `CallSubOrchestratorWithRetryAsync` 메서드, JavaScript의 `callSubOrchestrator` 또는 `callSubOrchestratorWithRetry` 메서드 및 Python의 `call_sub_orchestrator` 또는 `call_sub_orchestrator_with_retry` 메서드를 사용하여 다른 오케스트레이터 함수를 호출할 수 있습니다. [오류 처리 및 보정](durable-functions-error-handling.md#automatic-retry-on-failure) 문서에서는 자동 다시 시도에 대해 자세히 설명하고 있습니다.

하위 오케스트레이터 함수는 호출자의 관점에서 작업 함수처럼 작동합니다. 이러한 함수는 값을 반환하고, 예외를 throw하며, 부모 오케스트레이터 함수에서 기다릴 수 있습니다. 

> [!NOTE]
> 현재 하위 오케스트레이션은 .NET, JavaScript 및 Python에서 지원됩니다.

## <a name="example"></a>예제

다음 예제에서는 프로비전해야 하는 여러 디바이스가 있는 IoT("사물 인터넷") 시나리오를 보여줍니다. 다음 함수는 각 디바이스에서 실행되어야 하는 프로비저닝 워크플로를 나타냅니다.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    device_id = context.get_input()

    # Step 1: Create an installation package in blob storage and return a SAS URL.
    sas_url = yield context.call_activity("CreateInstallationPackage", device_id)

    # Step 2: Notify the device that the installation package is ready.
    yield context.call_activity("SendPackageUrlToDevice", { "id": device_id, "url": sas_url })

    # Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.call_activity("DownloadCompletedAck")

    # Step 4: ...
```

---

이 오케스트레이터 함수는 일회용 디바이스 프로비전을 위해 그대로 사용하거나 더 큰 오케스트레이션의 일부로 포함될 수 있습니다. 후자의 경우 부모 오케스트레이터 함수는 `CallSubOrchestratorAsync`(.NET), `callSubOrchestrator`(JavaScript) 또는 `call_sub_orchestrator`(Python) API를 사용하여 `DeviceProvisioningOrchestration` 인스턴스를 예약할 수 있습니다.

다음은 여러 오케스트레이터 함수를 병렬로 실행하는 방법을 보여 주는 예제입니다.

# <a name="c"></a>[C#](#tab/csharp)

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
> 이전 C# 예제는 Durable Functions 2.x에 대한 것입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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


# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):

    device_IDs = yield context.call_activity("GetNewDeviceIds")

    # Run multiple device provisioning flows in parallel
    provisioning_tasks = []
    id_ = 0
    for device_id in device_IDs:
        child_id = f"{context.instance_id}:{id_}"
        provision_task = context.call_sub_orchestrator("DeviceProvisioningOrchestration", device_id, child_id)
        provisioning_tasks.append(provision_task)
        id_ += 1

    yield context.task_all(provisioning_tasks)

    # ...
```
---

> [!NOTE]
> 하위 오케스트레이션은 부모 오케스트레이션과 동일한 함수 앱에서 정의되어야 합니다. 다른 함수 앱에서 오케스트레이션을 호출하고 기다려야 하는 경우 HTTP API 및 HTTP 202 폴링 소비자 패턴에 대한 기본 제공 지원을 사용하는 것이 좋습니다. 자세한 내용은 [HTTP 기능](durable-functions-http-features.md) 항목을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 오케스트레이션 상태를 설정하는 방법 알아보기](durable-functions-custom-orchestration-status.md)
