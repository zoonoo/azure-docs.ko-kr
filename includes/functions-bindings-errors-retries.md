---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 39c0556350482e171234a3ff9dce0c16ed88d110
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406650"
---
Azure Functions 발생 하는 오류는 다음 원본 중 하나에서 가져올 수 있습니다.

- 기본 제공 Azure Functions [트리거 및 바인딩](..\articles\azure-functions\functions-triggers-bindings.md) 사용
- 기본 Azure 서비스의 Api 호출
- REST 끝점에 대 한 호출
- 클라이언트 라이브러리, 패키지 또는 타사 Api에 대 한 호출

데이터 또는 누락 된 메시지의 손실을 방지 하기 위해 다음과 같은 좋은 오류 처리 방법을 사용 하는 것이 중요 합니다. 권장 되는 오류 처리 방법에는 다음 작업이 포함 됩니다.

- [Application Insights 사용](../articles/azure-functions/functions-monitoring.md)
- [구조적 오류 처리 사용](#use-structured-error-handling)
- [멱 등 성 디자인](../articles/azure-functions/functions-idempotent.md)
- [재시도 정책 구현](#retry-policies-preview) (해당 하는 경우)

### <a name="use-structured-error-handling"></a>구조적 오류 처리 사용

오류 캡처 및 로깅은 응용 프로그램의 상태를 모니터링 하는 데 매우 중요 합니다. 함수 코드의 최상위 수준에는 try/catch 블록이 포함 되어야 합니다. Catch 블록에서 오류를 캡처 및 기록할 수 있습니다.

## <a name="retry-policies-preview"></a>다시 시도 정책 (미리 보기)

재시도 정책은 함수 앱의 모든 트리거 형식에 대 한 모든 함수에서 정의할 수 있습니다.  다시 시도 정책은 성공적으로 실행 되거나 최대 다시 시도 횟수가 발생할 때까지 함수를 다시 실행 합니다.  다시 시도 정책은 앱의 모든 함수 또는 개별 기능에 대해 정의할 수 있습니다.  기본적으로 함수 앱은 [트리거 소스에 대 한 재시도 정책이 있는 특정 트리거와](#using-retry-support-on-top-of-trigger-resilience)는 별도로 메시지를 다시 시도 하지 않습니다.  재시도 정책은 실행 결과로 catch 되지 않는 예외가 발생할 때마다 평가 됩니다.  모범 사례로, 코드의 모든 예외를 catch 하 고 다시 시도해 야 하는 모든 오류를 다시 throw 해야 합니다.  실행에 대 한 재시도 정책이 완료 될 때까지 Event Hubs 및 Azure Cosmos DB 검사점이 작성 되지 않습니다. 즉, 현재 일괄 처리가 완료 될 때까지 해당 파티션의 진행이 일시 중지 됩니다.

### <a name="retry-policy-options"></a>다시 시도 정책 옵션

재시도 정책을 정의 하는 데 사용할 수 있는 옵션은 다음과 같습니다.

**최대 다시 시도 횟수** 는 최종 실패 전에 실행을 다시 시도 하는 최대 횟수입니다. 값은 `-1` 무기한으로 다시 시도 하는 것을 의미 합니다. 현재 다시 시도 횟수는 인스턴스의 메모리에 저장 됩니다. 인스턴스는 다시 시도 사이에 오류가 있을 수 있습니다.  다시 시도 정책 중에 인스턴스가 실패 하면 재시도 횟수가 손실 됩니다.  인스턴스 오류가 발생 하면 Event Hubs, Azure Cosmos DB 및 큐 저장소와 같은 트리거가 처리를 다시 시작 하 고 다시 시도 횟수를 0으로 설정 하 여 새 인스턴스에서 일괄 처리를 다시 시도할 수 있습니다.  HTTP 및 타이머와 같은 다른 트리거는 새 인스턴스에서 다시 시작 되지 않습니다.  이는 최대 다시 시도 횟수를 가장 많이 사용 하는 것입니다. 드문 경우 지만 실행을 최대값 보다 더 많이 다시 시도 하거나 HTTP 및 타이머와 같은 트리거의 경우 최대값 보다 낮게 다시 시도할 수 있습니다.

**재시도 전략** 은 재시도 동작을 제어 합니다.  다음은 두 가지 지원 되는 다시 시도 옵션입니다.

| 옵션 | Description|
|---|---|
|**`fixedDelay`**| 각 다시 시도 사이에 지정 된 시간이 경과할 수 있습니다.|
| **`exponentialBackoff`**| 첫 번째 다시 시도는 최소 지연 시간 동안 대기 합니다. 이후 재시도 시 최대 지연 시간에 도달할 때까지 다시 시도 하는 각 다시 시도에 대 한 초기 기간에는 시간이 상당히 추가 됩니다.  지 수 백오프는 처리량이 많은 시나리오에서 다시 시도를 분산 하는 몇 가지 작은 임의를 지연에 추가 합니다.|

#### <a name="app-level-configuration"></a>앱 수준 구성

다시 시도 정책은 [해당 `host.json` 파일을 사용 하 여](../articles/azure-functions/functions-host-json.md#retry)앱의 모든 함수에 대해 정의할 수 있습니다. 

#### <a name="function-level-configuration"></a>함수 수준 구성

다시 시도 정책은 특정 기능에 대해 정의할 수 있습니다.  기능별 구성이 앱 수준 구성 보다 우선 합니다.

#### <a name="fixed-delay-retry"></a>지연 시간 고정 다시 시도

# <a name="c"></a>[C#](#tab/csharp)

다시 시도 하려면 NuGet 패키지 WebJobs >= 3.0.23가 필요 [합니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>지 수 백오프 다시 시도

# <a name="c"></a>[C#](#tab/csharp)

다시 시도 하려면 NuGet 패키지 WebJobs >= 3.0.23가 필요 [합니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

다음은 파일 *에 대 한function.js* 의 재시도 정책입니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json 속성  |Attribute 속성 | Description |
|---------|---------|---------| 
|방식의|해당 없음|필수 사항입니다. 사용하는 재시도 전략입니다. 유효한 값은 `fixedDelay` 또는 `exponentialBackoff`입니다.|
|maxRetryCount|해당 없음|필수 사항입니다. 함수 실행 당 허용 되는 최대 다시 시도 횟수입니다. `-1` 무기한으로 다시 시도 하는 것을 의미 합니다.|
|delayInterval|해당 없음|전략을 사용할 때 재시도 사이에 사용 되는 지연입니다 `fixedDelay` .|
|minimumInterval|해당 없음|전략을 사용 하는 경우 최소 재시도 지연 `exponentialBackoff` 입니다.|
|maximumInterval|해당 없음|전략을 사용 하는 경우 다시 시도 하는 최대 시간 `exponentialBackoff` 입니다.| 

### <a name="retry-limitations-during-preview"></a>미리 보기 중에 재시도 제한

- .NET 프로젝트의 경우 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23의 버전을 수동으로 가져와야 할 수도 있습니다.
- 소비 계획에서 앱은 큐에서 최종 메시지를 다시 시도 하는 동안 0으로 축소 될 수 있습니다.
- 소비 계획에서 재시도를 수행 하는 동안 앱이 축소 될 수 있습니다.  최상의 결과를 위해 재시도 간격 <= 00:01:00 및 <= 5 회 재시도를 선택 합니다.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>트리거 복원 력 위에 다시 시도 지원 사용

함수 앱 다시 시도 정책은 트리거에서 제공 하는 다시 시도 또는 복원 력을 독립적입니다.  함수 다시 시도 정책은 트리거 복원 력 다시 시도의 맨 위에만 계층화 됩니다.  예를 들어 Azure Service Bus 사용 하는 경우 기본적으로 큐의 메시지 배달 수는 10입니다.  기본 배달 수는 큐 메시지를 10 번 배달 한 후 메시지를 배달 하지 못한 것을 의미 하는 것 Service Bus을 의미 합니다.  Service Bus 트리거를 포함 하는 함수에 대 한 재시도 정책을 정의할 수 있지만 재시도는 Service Bus 배달 시도의 맨 위에 계층화 됩니다.  

예를 들어 기본 Service Bus 배달 수를 10으로 사용 하 고 함수 다시 시도 정책 (5)을 정의 합니다.  메시지는 먼저 큐에서 제거 되며 service bus 배달 계정이 1로 증가 합니다.  모든 실행이 실패 한 경우 동일한 메시지를 트리거하기 위해 5 번 시도 하면 해당 메시지가 중단 된 것으로 표시 됩니다.  Service Bus 즉시 메시지를 다시 대기 함수를 트리거하고 배달 수를 2로 늘립니다.  마지막으로, 50 최종 시도 후 (10 개의 service bus 배달 * 배달 당 5 개의 함수 다시 시도) 메시지는 중단 되 고 service bus에서 배달 못 한 편지를 트리거합니다.

> [!WARNING]
> 큐 Service Bus와 같은 트리거의 배달 횟수를 1로 설정 하는 것은 권장 되지 않습니다. 즉, 단일 함수 재시도 주기 바로 뒤에 메시지가 배달 못 한 문자로 배달 됩니다.  이는 트리거는 다시 시도를 다시 시도 하는 것을 제공 하는 반면, 함수 다시 시도 정책은 최상의 노력으로, 원하는 총 재시도 횟수 보다 적을 수 있기 때문입니다.

### <a name="triggers-with-additional-resiliency-or-retries"></a>추가 복원 력을 포함 하는 트리거 또는 다시 시도

다음 트리거는 트리거 원본에서 재시도를 지원 합니다.

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로 대부분의 트리거는 다시 시도 요청은 최대 5 회입니다. 5 번째 재시도 후에는 Azure Queue storage가 모두 [포이즌 큐](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)에 메시지를 기록 합니다.  기본 Service Bus 큐 및 토픽 정책은 10 번 시도 후 [배달 못 한 편지 큐](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) 에 메시지를 기록 합니다.
