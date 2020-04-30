---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브를 구성 하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604607"
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
* 해당 하는 경우에는 큼 메시지 페이로드가 포함 된 저장소 컨테이너입니다.

Orchestrator, entity 또는 activity 함수를 실행 하거나 실행 하도록 예약 하는 경우 이러한 모든 리소스가 기본 Azure Storage 계정에 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

작업 허브는 다음 규칙을 준수 하는 이름으로 식별 됩니다.

* 영숫자만 포함
* 문자로 시작
* 의 최소 길이는 3 자, 최대 길이는 45 자입니다.

작업 허브 이름은 다음 예제와 같이 *호스트 json* 파일에 선언 됩니다.

### <a name="hostjson-functions-20"></a>host. json (함수 2.0)

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

다음 `host.json` 예제 파일에 표시 된 것 처럼 앱 설정을 사용 하 여 작업 허브를 구성할 수도 있습니다.

### <a name="hostjson-functions-10"></a>host. json (함수 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host. json (함수 2.0)

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

다음 코드에서는 [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client) 사용 하 여 앱 설정으로 구성 된 작업 허브로 작업 하는 함수를 작성 하는 방법을 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

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
> 이전 c # 예제는 Durable Functions 2.x에 대 한 것입니다. 1.x Durable Functions의 경우 대신를 사용 `DurableOrchestrationContext` 해야 합니다. `IDurableOrchestrationContext` 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

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

---

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정 하지 않으면 기본 작업 허브 이름이 다음 표에 표시 된 것 처럼 사용 됩니다.

| 지 속성 확장 버전 | 기본 작업 허브 이름 |
| - | - |
| 2.x | Azure에 배포 된 경우 작업 허브 이름은 _함수 앱_의 이름에서 파생 됩니다. Azure 외부에서 실행 되는 경우 기본 작업 허브 이름은 `TestHubName`입니다. |
| 1.x | 모든 환경에 대 한 기본 작업 허브 이름은 `DurableFunctionsHub`입니다. |

확장 버전 간의 차이점에 대 한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조 하세요.

> [!NOTE]
> 이 이름은 공유 스토리지 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 여러 함수 앱이 한 공유 스토리지 계정을 공유하는 경우 *host.json* 파일의 각 작업 허브에 서로 다른 이름을 명시적으로 구성해야 합니다. 그렇지 않으면 여러 함수 앱에서 메시지에 대해 서로 경쟁 하 게 됩니다 .이로 인해 `Pending` 또는 `Running` 상태에서 예기치 않게 "중지" 된 오케스트레이션이 포함 된 정의 되지 않은 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오케스트레이션 버전 관리를 처리 하는 방법 알아보기](durable-functions-versioning.md)
