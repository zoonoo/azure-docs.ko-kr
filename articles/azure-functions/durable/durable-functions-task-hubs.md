---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브를 구성하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 9172075ca22937a85fd7fd5827ebb40a4b58bcfa
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375761"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>지속성 함수의 작업 허브(Azure Functions)

[Durable Functions](durable-functions-overview.md)의 *작업 허브* 는 오케스트레이션 및 엔터티에 사용되는 지속형 스토리지 리소스에 대한 논리적 컨테이너입니다. 오케스트레이터, 작업, 엔터티 함수는 동일한 작업 허브에 속하는 경우에만 직접 상호 작용할 수 있습니다.

> [!NOTE]
> 이 문서에서는 [Durable Functions에 대한 기본 Azure Storage 공급자](durable-functions-storage-providers.md#azure-storage)와 관련된 방식으로 작업 허브의 세부 정보를 설명합니다. Durable Functions 앱에 대해 비기본 스토리지 공급자를 사용하는 경우 공급자별 설명서에서 자세한 작업 허브 설명서를 찾을 수 있습니다.
> 
> * [Netherite 스토리지 공급자에 대한 작업 허브 정보](https://microsoft.github.io/durabletask-netherite/#/storage)
> * [MSSQL(Microsoft SQL) 스토리지 공급자에 대한 작업 허브 정보](https://microsoft.github.io/durabletask-mssql/#/taskhubs)
> 
> 다양한 스토리지 공급자 옵션 및 비교 방법에 대한 자세한 내용은 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md) 설명서를 참조하세요.

여러 함수 앱이 한 스토리지 계정을 공유하는 경우 *반드시* 함수 앱마다 별도의 작업 허브 이름으로 구성해야 합니다. 한 스토리지 계정에 여러 작업 허브가 포함될 수 있습니다. 이 제한은 일반적으로 다른 스토리지 공급자에도 적용됩니다.

다음 다이어그램에서는 공유 및 전용 Azure Storage 계정의 함수 앱당 단일 작업 허브를 보여 줍니다.

![공유 및 전용 스토리지 계정을 표시하는 다이어그램](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage 리소스

Azure Storage의 작업 허브는 다음과 같은 리소스로 구성됩니다.

* 하나 이상의 제어 큐
* 하나의 작업 항목 큐
* 하나의 기록 테이블
* 하나의 인스턴스 테이블
* 하나 이상의 스토리지 Blob을 포함하는 하나의 스토리지 컨테이너
* 해당하는 경우에는 큰 메시지 페이로드가 포함된 스토리지 컨테이너

오케스트레이터, 엔터티 또는 작업 함수가 실행되거나 실행되도록 예약된 경우 이러한 모든 리소스는 구성된 Azure Storage 계정에 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

Azure Storage의 작업 허브는 다음 규칙을 준수하는 이름으로 식별됩니다.

* 영숫자만 포함
* 문자로 시작
* 최소 길이는 3자, 최대 길이는 45자

작업 허브 이름은 다음 예제와 같이 *host.json* 에 선언됩니다.

### <a name="hostjson-functions-20"></a>host.json(Functions 2.0)

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

다음 `host.json` 예제 파일과 같이 앱 설정을 사용하여 작업 허브를 구성할 수도 있습니다.

### <a name="hostjson-functions-10"></a>host.json(Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json(Functions 2.0)

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

**host.json** 외에도 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)메타 데이터에서 작업 허브 이름을 구성할 수 있습니다. 별도의 함수 앱에 상주하는 오케스트레이션 또는 엔터티에 액세스해야 하는 경우에 유용합니다. 다음 코드에서는 [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client)을 사용하여 앱 설정으로 구성된 작업 허브로 작업하는 함수를 작성하는 방법을 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> 이전 C# 예제는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableOrchestrationContext` 대신 `IDurableOrchestrationContext`를 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` 파일의 작업 허브 속성은 앱 설정을 통해 설정됩니다.

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

`function.json` 파일의 작업 허브 속성은 앱 설정을 통해 설정됩니다.

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

> [!NOTE]
> 클라이언트 바인딩 메타데이터에서 작업 허브 이름을 구성하는 작업은 하나의 함수 앱을 사용하여 다른 함수 앱의 오케스트레이션 및 엔터티에 액세스할 때만 필요합니다. 클라이언트 함수가 오케스트레이션 및 엔터티와 동일한 함수 앱에 정의된 경우 바인딩 메타데이터에서 작업 허브 이름을 지정하지 않아야 합니다. 기본적으로 모든 클라이언트 바인딩은 **host.json** 설정에서 작업 허브 메타데이터를 가져옵니다.

Azure Storage의 작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정하지 않으면 다음 표와 같이 기본 작업 허브 이름이 사용됩니다.

| 지속성 확장 버전 | 기본 작업 허브 이름 |
| - | - |
| 2.x | Azure에 배포된 경우 작업 허브 이름은 _함수 앱_ 의 이름에서 파생됩니다. Azure 외부에서 실행하는 경우 기본 작업 허브 이름은 `TestHubName`입니다. |
| 1.x | 모든 환경의 기본 작업 허브 이름은 `DurableFunctionsHub`입니다. |

확장 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

> [!NOTE]
> 이 이름은 공유 스토리지 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 여러 함수 앱이 한 공유 스토리지 계정을 공유하는 경우 *host.json* 파일의 각 작업 허브에 서로 다른 이름을 명시적으로 구성해야 합니다. 그러지 않으면 여러 함수 앱이 메시지를 위해 서로 경쟁하게 되며, 이로 인해 오케스트레이션이 예기치 않게 `Pending` 또는 `Running` 상태로 "중단"되는 것과 같은 정의되지 않은 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오케스트레이션 버전 관리를 처리하는 방법 알아보기](durable-functions-versioning.md)
