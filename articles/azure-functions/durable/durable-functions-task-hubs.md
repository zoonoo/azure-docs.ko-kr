---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브를 구성하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ffb3d590aebe80994de1e7e834a2eba5777df9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262489"
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
* 해당되는 경우 큰 메시지 페이로드를 포함하는 저장소 컨테이너입니다.

이러한 모든 리소스는 오케스트레이터, 엔터티 또는 활동 함수가 실행되거나 실행되도록 예약될 때 기본 Azure Storage 계정에서 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

작업 허브는 다음 예제와 같이 *host.json*에 선언된 이름으로 식별됩니다.

### <a name="hostjson-functions-20"></a>host.json (함수 2.0)

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

### <a name="hostjson-functions-10"></a>host.json (함수 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (함수 2.0)

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

다음 코드는 [오케스트레이션 클라이언트 바인딩을](durable-functions-bindings.md#orchestration-client) 사용하여 앱 설정으로 구성된 작업 허브로 작업하는 함수를 작성하는 방법을 보여 줍니다.

# <a name="c"></a>[C #](#tab/csharp)

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
> 이전 C# 예제는 지속 기능 2.x에 대 한 입니다. 지속 기능 1.x의 경우 `DurableOrchestrationContext` `IDurableOrchestrationContext`을 대신 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [지속 형 기능 버전](durable-functions-versions.md) 문서를 참조하십시오.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

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

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정하지 않으면 다음 표와 같이 기본 작업 허브 이름이 사용됩니다.

| 내구성 확장 버전 | 기본 작업 허브 이름 |
| - | - |
| 2.x | Azure에 배포할 때 작업 허브 이름은 _함수 앱의_이름에서 파생됩니다. Azure 외부에서 실행하는 경우 기본 작업 `TestHubName`허브 이름은 입니다. |
| 1.x | 모든 환경의 기본 작업 `DurableFunctionsHub`허브 이름은 . |

확장 버전 간의 차이점에 대한 자세한 내용은 [지속형 함수 버전](durable-functions-versions.md) 문서를 참조하십시오.

> [!NOTE]
> 이 이름은 공유 스토리지 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 여러 함수 앱이 한 공유 스토리지 계정을 공유하는 경우 *host.json* 파일의 각 작업 허브에 서로 다른 이름을 명시적으로 구성해야 합니다. 그렇지 않으면 여러 함수 앱이 메시지를 위해 서로 경쟁하므로 오케스트레이션이 예기치 않게 `Pending` 또는 `Running` 상태에 "갇혀"되는 등 정의되지 않은 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오케스트레이션 버전 전환 처리 방법 알아보기](durable-functions-versioning.md)
