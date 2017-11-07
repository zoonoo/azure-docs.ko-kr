---
title: "Azure Functions에 대한 host.json 참조"
description: "Azure Functions host.json 파일에 대한 참조 설명서입니다."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: b3e5976a84e0ec91a41d683a426b58635fd5abd6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2017
---
# <a name="hostjson-reference-for-azure-functions"></a>Azure Functions에 대한 host.json 참조

*host.json* 메타데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 이 문서에는 사용 가능한 설정이 나열되어 있습니다. JSON 스키마는 http://json.schemastore.org/host에 있습니다.

[앱 설정](functions-app-settings.md) 및 [local.settings.json](functions-run-local.md#local-settings-file) 파일에는 다른 전역 구성 옵션이 있습니다.

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

다음 샘플 *host.json* 파일에는 가능한 모든 옵션이 지정되어 있습니다.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[Application Insights에 대한 메트릭을 계산](functions-monitoring.md#configure-the-aggregator)할 때 집계되는 함수 호출 수를 지정합니다. 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|batchSize|1000|집계할 최대 요청 수입니다.| 
|flushTimeout|00:00:30|집계할 최대 기간입니다.| 

함수 호출은 두 개의 한도 중 첫 번째 한에 도달할 때 집계됩니다.

## <a name="applicationinsights"></a>applicationInsights

[Application Insights에서 샘플링 기능](functions-monitoring.md#configure-sampling)을 제어합니다.

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|isEnabled|false|샘플링을 사용 여부를 설정합니다.| 
|maxTelemetryItemsPerSecond|5|샘플링이 시작되는 임계값입니다.| 

## <a name="eventhub"></a>eventHub

[Event Hub 트리거 및 바인딩](functions-bindings-event-hubs.md)에 대한 구성 설정입니다.

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|maxBatchSize|64|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|해당 없음|기본 EventProcessorHost에서 사용할 기본 PrefetchCount입니다.| 
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.| 

## <a name="functions"></a>functions

작업 호스트가 실행할 함수 목록입니다.  빈 배열은 모든 함수를 실행한다는 의미입니다.  [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. 함수 앱에서 *host.json*의 이 속성 대신 *function.json* `disabled` 속성을 사용하십시오.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. 사용 계획에서 유효한 범위는 1초에서 10분 사이이고 기본값은 5분입니다. App Service 계획에는 한도 없고 기본값은 null이며 이것은 시간 제한이 없음을 나타냅니다.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

[http 트리거 및 바인딩](functions-bindings-http-webhook.md)에 대한 구성 설정입니다.

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|routePrefix|api|모든 경로에 적용되는 경로 접두사입니다. 기본 접두사를 제거하려면 빈 문자열을 사용하십시오. |
|maxOutstandingRequests|-1|지정된 시간에 보유할 미해결 요청의 최대 수입니다(-1은 무한함을 의미). 한도에는 대기 중이지만 실행이 시작되지 않은 요청과 진행 중인 모든 실행이 포함됩니다. 이 한도를 초과하여 들어오는 요청이 있으면 429 "Too Busy" 응답으로 거부됩니다. 호출자는 응답을 사용하여 시간 기반 재시도 전략을 채택할 수 있습니다. 이 설정은 작업 호스트 실행 경로 내에서 발생하는 큐만 제어합니다. ASP.NET 요청 큐와 같은 다른 큐는 이 설정의 영향을 받지 않습니다. |
|maxConcurrentRequests|-1|병렬로 실행될 HTTP 함수의 최대 수입니다(-1은 무한함을 의미). 예를 들어 동시성이 높을 때 HTTP 함수가 시스템 리소스를 너무 많이 사용하는 경우 한도를 설정할 수 있습니다. 또는 함수가 타사 서비스에 아웃바운드 요청을 하는 경우 해당 호출의 속도가 제한될 수 있습니다.|
|dynamicThrottlesEnabled|false|요청 처리 파이프라인이 시스템 성능 카운터를 주기적으로 확인하도록 합니다. 카운터에는 연결, 스레드, 프로세스, 메모리 및 CPU가 포함됩니다. 카운터 중 하나라도 기본 제공 임계값(80%)을 초과하면 카운터가 정상 수준으로 돌아올 때까지 429 "Too Busy" 응답으로 요청이 거부됩니다.|

## <a name="id"></a>id

작업 호스트의 고유 ID입니다. 대시가 제거된 소문자 GUID일 수 있습니다. 로컬에서 실행될 때 필요합니다. Azure Functions에서 실행할 때 `id`가 생략되면 ID가 자동으로 생성됩니다.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

[ILogger 개체](functions-monitoring.md#write-logs-in-c-functions) 또는 [context.log](functions-monitoring.md#write-logs-in-javascript-functions)에 의해 기록된 로그 필터링을 제어합니다.

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|categoryFilter|해당 없음|범주별 필터링을 지정합니다.| 
|defaultLevel|정보|`categoryLevels` 배열에 지정되지 않은 범주가 있으면 이 수준 이상의 로그를 Application Insights로 보내십시오.| 
|categoryLevels|해당 없음|각 범주에 대해 Application Insight에 보낼 최소 로그 수준을 지정하는 범주 배열입니다. 여기에 지정된 범주는 동일한 값으로 시작하는 모든 범주를 제어하며 긴 값이 우선합니다. 앞의 샘플 *host.json* 파일에서 "Host.Aggregator"로 시작하는 모든 범주는 `Information` 수준으로 기록됩니다. "Host.Executor"와 같이 "Host"로 시작하는 다른 모든 범주는 `Error` 수준으로 기록됩니다.| 

## <a name="queues"></a>queues

[저장소 큐 트리거 및 바인딩](functions-bindings-storage-queue.md)에 대한 구성 설정입니다.

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|maxPollingInterval|60000|큐 폴링 사이 최대 간격(밀리초)입니다.| 
|visibilityTimeout|0|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다.| 
|batchSize|16|동시에 검색하고 처리할 큐 메시지의 수입니다. 최대값은 32입니다.| 
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.| 
|newBatchThreshold|batchSize/2|새 메시지 일괄 처리를 페치할 임계값입니다.| 

## <a name="servicebus"></a>serviceBus

[Service Bus 트리거 및 바인딩](functions-bindings-service-bus.md)에 대한 구성 설정입니다.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|maxConcurrentCalls|16|메시지 펌프가 시작되어야 하는 콜백에 대한 최대 동시 호출 수입니다. | 
|prefetchCount|해당 없음|기본 MessageReceiver에서 사용할 기본 PrefetchCount입니다.| 
|autoRenewTimeout|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.| 

## <a name="singleton"></a>singleton

Singleton 잠금 동작에 대한 구성 설정입니다. 자세한 내용은 [singleton 지원에 대한 GitHub 문제](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)를 참조하세요.

```json
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 적용되는 기간입니다. 잠금은 자동 갱신됩니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 적용되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작할 때 수신기 잠금을 가져올 수 없는 경우 수신기 잠금 복구에 사용되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 확보하려고 시도하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|해당 없음|잠금 확보 시도 사이의 간격입니다.| 

## <a name="tracing"></a>tracing

`TraceWriter` 개체를 사용하여 만드는 로그에 대한 구성 설정입니다. [C# 로깅](functions-reference-csharp.md#logging) 및 [Node.js 로깅](functions-reference-node.md#writing-trace-output-to-the-console)을 참조하세요. 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|consoleLevel|info|콘솔 로깅의 추적 수준입니다. 옵션은 `off`, `error`, `warning`, `info` 및 `verbose`입니다.|
|fileLoggingMode|debugOnly|파일 로깅에 대한 추적 수준입니다. 옵션은 `never`, `always`, `debugOnly`입니다.| 

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이 디렉터리의 코드가 변경되면 변경 내용이 함수에 의해 선택되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

[지속성 함수](durable-functions-overview.md)의 [작업 허브](durable-functions-task-hubs.md) 이름입니다.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다. 자세한 내용은 [작업 허브](durable-functions-task-hubs.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [host.json 파일을 업데이트하는 방법 알아보기](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
