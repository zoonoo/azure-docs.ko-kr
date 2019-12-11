---
title: Azure Functions 2.x에 대 한 호스트 json 참조
description: V2 runtime을 사용 하는 Azure Functions 호스트 json 파일에 대 한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: 08d772fc9b2871262b449a017f8be59a344576b2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975451"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 이상에 대 한 호스트 json 참조 

> [!div class="op_single_selector" title1="사용 중인 Azure Functions 런타임 버전을 선택 합니다. "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

*Host. json* 메타 데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함 되어 있습니다. 이 문서에서는 Azure Functions 런타임의 버전 2.x부터 사용 가능한 설정을 나열 합니다.  

> [!NOTE]
> 이 문서는 Azure Functions 2.x 이상 버전에 대 한 것입니다.  Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

다른 함수 앱 구성 옵션은 [앱 설정](functions-app-settings.md)에서 관리 됩니다.

일부 host. json 설정은 [로컬. 설정 json](functions-run-local.md#local-settings-file) 파일에서 로컬로 실행 하는 경우에만 사용 됩니다.

## <a name="sample-hostjson-file"></a>샘플 호스트 json 파일

다음 샘플 *호스트 json* 파일에는 모든 가능한 옵션이 지정 되어 있습니다.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명 합니다. 별도로 지정 하지 않는 한 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>Applicationinsights.config

이 설정은 [로깅](#logging)의 하위입니다.

[Application Insights의 샘플링 기능](./functions-monitoring.md#configure-sampling)을 제어 합니다.

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> 로그 샘플링으로 인해 일부 실행이 Application Insights 모니터 블레이드에서 표시 되지 않을 수 있습니다.

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|igaz|샘플링을 사용 하거나 사용 하지 않도록 설정 합니다.| 
|maxTelemetryItemsPerSecond|20|샘플링을 시작 하는 임계값입니다.| 
|EnableLiveMetrics |igaz|라이브 메트릭 수집을 사용 하도록 설정 합니다.|
|EnableDependencyTracking|igaz|종속성 추적을 사용 합니다.|
|EnablePerformanceCountersCollection|igaz|Kudu 성능 카운터 수집을 사용 하도록 설정 합니다.|

## <a name="cosmosdb"></a>cosmosDb

구성 설정은 [Cosmos DB 트리거 및 바인딩에서](functions-bindings-cosmosdb-v2.md#host-json)찾을 수 있습니다.

## <a name="durabletask"></a>Microsoft.azure.webjobs.extensions.durabletask

[Durable Functions에 대 한 바인딩에서](durable/durable-functions-bindings.md#host-json)구성 설정을 찾을 수 있습니다.

## <a name="eventhub"></a>eventHub

[이벤트 허브 트리거 및 바인딩에서](functions-bindings-event-hubs.md#host-json)구성 설정을 찾을 수 있습니다. 

## <a name="extensions"></a>확장할

[Http](#http) 및 [eventHub](#eventhub)와 같은 모든 바인딩 관련 설정을 포함 하는 개체를 반환 하는 속성입니다.

## <a name="extensionbundle"></a>extensionBundle 

확장 번들을 사용 하면 호환 되는 함수 바인딩 확장 집합을 함수 앱에 추가할 수 있습니다. 자세히 알아보려면 [로컬 개발용 확장 번들](functions-bindings-register.md#extension-bundles)을 참조 하세요.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

작업 호스트에서 실행 하는 함수 목록입니다. 빈 배열은 모든 함수를 실행 함을 의미 합니다. [로컬로 실행](functions-run-local.md)하는 경우에만 사용할 목적입니다. Azure의 함수 앱에서 대신 [Azure Functions에서 함수를 사용 하지 않도록 설정 하는 방법](disable-function.md) 의 단계를 수행 하 여이 설정을 사용 하는 대신 특정 기능을 사용 하지 않도록 설정 해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수의 제한 시간을 나타냅니다. Timespan 문자열 형식을 따릅니다. 서버를 사용 하지 않는 소비 계획에서 유효한 범위는 1 초에서 10 분 사이 이며 기본값은 5 분입니다.  

프리미엄 계획에서 유효한 범위는 1 초에서 60 분 사이 이며 기본값은 30 분입니다.

전용 (App Service) 계획에서는 전체 제한이 없으며 기본값은 30 분입니다. `-1` 값은 바인딩되지 않은 실행을 나타내지만 고정 상한을 유지 하는 것이 좋습니다.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[호스트 상태 모니터](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)에 대 한 구성 설정입니다.

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|enabled|igaz|기능이 사용 되는지 여부를 지정 합니다. | 
|healthCheckInterval|10 másodperc|정기적인 백그라운드 상태 검사 간의 시간 간격입니다. | 
|healthCheckWindow|2 분|`healthCheckThreshold` 설정과 함께 사용 되는 슬라이딩 시간 창입니다.| 
|healthCheckThreshold|6|호스트 재활용이 시작 되기 전에 상태 검사가 실패할 수 있는 최대 횟수입니다.| 
|counterThreshold|0,80|성능 카운터가 비정상으로 간주 되는 임계값입니다.| 

## <a name="http"></a>http

구성 설정은 [http 트리거 및 바인딩에서](functions-bindings-http-webhook.md#hostjson-settings)찾을 수 있습니다.

## <a name="logging"></a>logging

Application Insights를 포함 하 여 함수 앱의 로깅 동작을 제어 합니다.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|fileLoggingMode|debugOnly|사용할 수 있는 파일 로깅 수준을 정의 합니다.  옵션은 `never`, `always`, `debugOnly`입니다. |
|logLevel|–|앱에서 함수에 대 한 로그 범주 필터링을 정의 하는 개체입니다. 버전 2.x 이상에서는 로그 범주 필터링을 위한 ASP.NET Core 레이아웃을 따릅니다. 이렇게 하면 특정 함수에 대 한 로깅을 필터링 할 수 있습니다. 자세한 내용은 ASP.NET Core 설명서의 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) 을 참조 하세요. |
|konzol|–| [console](#console) 로깅 설정입니다. |
|Applicationinsights.config|–| [Applicationinsights](#applicationinsights) 설정입니다. |

## <a name="console"></a>konzol

이 설정은 [로깅](#logging)의 하위입니다. 디버깅 모드가 아닌 경우 콘솔 로깅을 제어 합니다.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|hamis|콘솔 로깅을 사용 하거나 사용 하지 않도록 설정 합니다.| 

## <a name="manageddependency"></a>managedDependency

관리 되는 종속성은 현재 PowerShell 기반 함수 에서만 지원 되는 기능입니다. 이를 통해 서비스에서 종속성을 자동으로 관리할 수 있습니다. `enabled` 속성이 `true`로 설정 된 경우 `requirements.psd1` 파일이 처리 됩니다. 부 버전이 릴리스되면 종속성이 업데이트 됩니다. 자세한 내용은 PowerShell 문서의 [관리 되는 종속성](functions-reference-powershell.md#dependency-management) 을 참조 하세요.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>쿼리

[저장소 큐 트리거 및 바인딩에서](functions-bindings-storage-queue.md#host-json)구성 설정을 찾을 수 있습니다.  

## <a name="sendgrid"></a>sendGrid

구성 설정은 [SendGrid 트리거 및 바인딩에서](functions-bindings-sendgrid.md#host-json)찾을 수 있습니다.

## <a name="servicebus"></a>serviceBus

구성 설정은 [Service Bus 트리거 및 바인딩에서](functions-bindings-service-bus.md#host-json)찾을 수 있습니다.

## <a name="singleton"></a>단일

Singleton 잠금 동작에 대 한 구성 설정입니다. 자세한 내용은 [singleton 지원에 대 한 GitHub 문제](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)를 참조 하세요.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 수행 되는 기간입니다. 에서 자동 갱신을 잠급니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 수행 되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작 시 수신기 잠금을 획득할 수 없는 경우 수신기 잠금 복구에 사용 되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 획득 하려고 시도 하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|–|잠금 획득 시도 사이의 간격입니다.| 

## <a name="version"></a>version

버전 문자열 `"version": "2.0"` v2 런타임을 대상으로 하는 함수 앱에 필요 합니다.

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링 해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이러한 디렉터리의 코드가 변경 될 때 함수에 의해 변경 내용이 선택 되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [호스트 json 파일을 업데이트 하는 방법을 알아봅니다.](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
