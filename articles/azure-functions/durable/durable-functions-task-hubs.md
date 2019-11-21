---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 Learn how to configure task hubs.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 38c7da8a1de57ed5acf3248fc6a71431de0bd1e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232784"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>지속성 함수의 작업 허브(Azure Functions)

[지속형 함수](durable-functions-overview.md)의 *작업 허브*는 오케스트레이션에 사용되는 Azure Storage 리소스에 대한 논리적 컨테이너입니다. 오케스트레이터 및 작업 함수는 동일한 작업 허브에 속할 때만 상호 작용할 수 있습니다.

여러 함수 앱이 한 스토리지 계정을 공유하는 경우 *반드시* 함수 앱마다 별도의 작업 허브 이름으로 구성해야 합니다. 한 스토리지 계정에 여러 작업 허브가 포함될 수 있습니다. 다음 다이어그램에서는 공유 및 전용 스토리지 계정의 함수 앱당 단일 작업 허브를 보여 줍니다.

![공유 및 전용 스토리지 계정을 표시하는 다이어그램](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage 리소스

작업 허브는 다음과 같은 스토리지 리소스로 구성됩니다.

* 하나 이상의 제어 큐
* 하나의 작업 항목 큐
* 하나의 기록 테이블
* 하나의 인스턴스 테이블
* 하나 이상의 스토리지 Blob을 포함하는 하나의 스토리지 컨테이너
* A storage container containing large message payloads, if applicable.

All of these resources are created automatically in the default Azure Storage account when orchestrator, entity, or activity functions run or are scheduled to run. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

작업 허브는 다음 예제와 같이 *host.json*에 선언된 이름으로 식별됩니다.

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json(Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Task hubs can also be configured using app settings, as shown in the following `host.json` example file:

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

작업 허브 이름은 `MyTaskHub` 앱 설정의 값으로 설정됩니다. 다음 `local.settings.json`은 `MyTaskHub` 설정을 `samplehubname`으로 정의하는 방법을 보여 줍니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

The following code is a precompiled C# example of how to write a function that uses the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to work with a task hub that is configured as an App Setting:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript"></a>JavaScript

`function.json` 파일의 작업 허브 속성은 앱 설정을 통해 설정됩니다.

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. If not specified, a default task hub name will be used as shown in the following table:

| Durable extension version | Default task hub name |
| - | - |
| 2.x | When deployed in Azure, the task hub name is derived from the name of the _function app_. When running outside of Azure, the default task hub name is `TestHubName`. |
| 1.x | The default task hub name for all environments is `DurableFunctionsHub`. |

For more information about the differences between extension versions, see the [Durable Functions versions](durable-functions-versions.md) article.

> [!NOTE]
> 이 이름은 공유 스토리지 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 여러 함수 앱이 한 공유 스토리지 계정을 공유하는 경우 *host.json* 파일의 각 작업 허브에 서로 다른 이름을 명시적으로 구성해야 합니다. Otherwise the multiple function apps will compete with each other for messages, which could result in undefined behavior, including orchestrations getting unexpectedly "stuck" in the `Pending` or `Running` state.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Learn how to handle orchestration versioning](durable-functions-versioning.md)
