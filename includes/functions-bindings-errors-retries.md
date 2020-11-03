---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284406"
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
- [재시도 정책 구현](#retry-policies) (해당 하는 경우)

### <a name="use-structured-error-handling"></a>구조적 오류 처리 사용

오류 캡처 및 로깅은 응용 프로그램의 상태를 모니터링 하는 데 매우 중요 합니다. 함수 코드의 최상위 수준에는 try/catch 블록이 포함 되어야 합니다. Catch 블록에서 오류를 캡처 및 기록할 수 있습니다.

## <a name="retry-policies"></a>재시도 정책

재시도 정책은 함수 앱의 모든 트리거 형식에 대 한 모든 함수에서 정의할 수 있습니다.  다시 시도 정책은 성공적으로 실행 되거나 최대 다시 시도 횟수가 발생할 때까지 함수를 다시 실행 합니다.  다시 시도 정책은 앱의 모든 함수 또는 개별 기능에 대해 정의할 수 있습니다.  기본적으로 함수 앱은 [트리거 소스에 대 한 재시도 정책이 있는 특정 트리거와](#trigger-specific-retry-support)는 별도로 메시지를 다시 시도 하지 않습니다.  재시도 정책은 실행 결과로 catch 되지 않는 예외가 발생할 때마다 평가 됩니다.  모범 사례로, 코드의 모든 예외를 catch 하 고 다시 시도해 야 하는 오류를 다시 throw 해야 합니다.  실행에 대 한 재시도 정책이 완료 될 때까지 Event Hubs 및 Azure Cosmos DB 검사점이 작성 되지 않습니다. 즉, 현재 일괄 처리가 완료 될 때까지 해당 파티션의 진행이 일시 중지 됩니다.

### <a name="retry-policy-options"></a>다시 시도 정책 옵션

재시도 정책을 정의 하는 데 사용할 수 있는 옵션은 다음과 같습니다.

**최대 다시 시도 횟수** 는 최종 실패 전에 실행을 다시 시도 하는 최대 횟수입니다. 값은 `-1` 무기한으로 다시 시도 하는 것을 의미 합니다. 현재 다시 시도 횟수는 인스턴스의 메모리에 저장 됩니다. 인스턴스는 다시 시도 사이에 오류가 있을 수 있습니다.  다시 시도 정책 중에 인스턴스가 실패 하면 재시도 횟수가 손실 됩니다.  인스턴스 오류가 발생 하면 Event Hubs, Azure Cosmos DB 및 큐 저장소와 같은 트리거가 처리를 다시 시작 하 고 다시 시도 횟수를 0으로 설정 하 여 새 인스턴스에서 일괄 처리를 다시 시도할 수 있습니다.  HTTP 및 타이머와 같은 다른 트리거는 새 인스턴스에서 다시 시작 되지 않습니다.  이는 최대 다시 시도 횟수를 가장 많이 사용 하는 것입니다. 드문 경우 지만 실행을 최대값 보다 더 많이 다시 시도 하거나 HTTP 및 타이머와 같은 트리거의 경우 최대값 보다 낮게 다시 시도할 수 있습니다.

**재시도 전략** 은 재시도 동작을 제어 합니다.  다음은 두 가지 지원 되는 다시 시도 옵션입니다.

| 옵션 | 설명|
|---|---|
|**`fixedDelay`**| 각 다시 시도 사이에 지정 된 시간이 경과할 수 있습니다.|
| **`exponentialBackoff`**| 첫 번째 다시 시도는 최소 지연 시간 동안 대기 합니다. 이후 재시도 시 최대 지연 시간에 도달할 때까지 다시 시도 하는 각 다시 시도에 대 한 초기 기간에는 시간이 상당히 추가 됩니다.  지 수 백오프는 처리량이 많은 시나리오에서 다시 시도를 분산 하는 몇 가지 작은 임의를 지연에 추가 합니다.|

#### <a name="app-level-configuration"></a>앱 수준 구성

다시 시도 정책은 [해당 `host.json` 파일을 사용 하 여](../articles/azure-functions/functions-host-json.md#retry)앱의 모든 함수에 대해 정의할 수 있습니다. 

#### <a name="function-level-configuration"></a>함수 수준 구성

다시 시도 정책은 특정 기능에 대해 정의할 수 있습니다.  기능별 구성이 앱 수준 구성 보다 우선 합니다.

#### <a name="fixed-delay-retry"></a>지연 시간 고정 다시 시도

# <a name="c"></a>[C#](#tab/csharp)

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

|function.json 속성  |Attribute 속성 | 설명 |
|---------|---------|---------| 
|방식의|해당 없음|필수 사항입니다. 사용하는 재시도 전략입니다. 유효한 값은 `fixedDelay` 또는 `exponentialBackoff`입니다.|
|maxRetryCount|해당 없음|필수 사항입니다. 함수 실행 당 허용 되는 최대 다시 시도 횟수입니다. `-1` 무기한으로 다시 시도 하는 것을 의미 합니다.|
|delayInterval|해당 없음|전략을 사용할 때 재시도 사이에 사용 되는 지연입니다 `fixedDelay` .|
|minimumInterval|해당 없음|전략을 사용 하는 경우 최소 재시도 지연 `exponentialBackoff` 입니다.|
|maximumInterval|해당 없음|전략을 사용 하는 경우 다시 시도 하는 최대 시간 `exponentialBackoff` 입니다.| 

## <a name="trigger-specific-retry-support"></a>트리거 별 다시 시도 지원

일부 트리거는 트리거 원본에서 재시도를 제공 합니다.  이러한 트리거 재시도는 또는 함수 앱 호스트 재시도 정책에 대 한 대체 방법으로 사용할 수 있습니다.  고정 된 수의 재시도를 원하는 경우 일반 호스트 재시도 정책에 따라 트리거 별 재시도 정책을 사용 해야 합니다.  다음 트리거는 트리거 원본에서 재시도를 지원 합니다.

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로이 작업은 다시 시도 요청을 최대 5 회까지 트리거합니다. 5 번째 재시도 후에는 Azure Queue storage와 Azure Service Bus 트리거에서 모두 [포이즌 큐](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)에 메시지를 씁니다.
