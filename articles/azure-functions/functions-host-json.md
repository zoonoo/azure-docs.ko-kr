---
title: Azure Functions 2.x에 대한 host.json 참조
description: v2 런타임을 사용하는 Azure Functions host.json 파일에 대한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7967cdc7f5f7cbb92c12de15d31471fda8aa6569
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758842"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure 함수 2.x 이상에 대한 host.json 참조 

> [!div class="op_single_selector" title1="사용 중인 Azure Functions 런타임 버전을 선택합니다. "]
> * [버전 1](functions-host-json-v1.md)
> * [버전 2+](functions-host-json.md)

*host.json* 메타데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 이 문서에는 Azure Functions 런타임의 버전 2.x부터 사용할 수 있는 설정이 나열됩니다.  

> [!NOTE]
> 이 문서는 Azure Functions 2.x 및 이후 버전에 대한 것입니다.  Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

다른 기능 앱 구성 옵션은 [앱 설정(배포된](functions-app-settings.md) 앱의 경우) 또는 [local.settings.json](functions-run-local.md#local-settings-file) 파일(로컬 개발용)에서 관리됩니다.

바인딩과 관련된 host.json의 구성은 함수 앱의 각 함수에 동일하게 적용됩니다. 

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

버전 2.x+에 대한 다음 샘플 *host.json* 파일에는 가능한 모든 옵션이 지정되어 있습니다(내부 용도로만 사용).

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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
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

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

이 설정은 [logging](#logging)의 자식입니다.

샘플링 옵션을 포함하여 응용 프로그램 인사이트에 대한 [옵션을 제어합니다.](./functions-monitoring.md#configure-sampling)

전체 JSON 구조에 대 한 이전 [예제 host.json 파일을](#sample-hostjson-file)참조 하십시오.

> [!NOTE]
> 로그 샘플링으로 인해 Application Insights 모니터 블레이드에 일부 실행이 표시되지 않을 수 있습니다. 로그 샘플링을 `excludedTypes: "Request"` 방지하려면 `samplingSettings` 값을 추가합니다.

| 속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| 샘플링설정 | 해당 없음 | [응용 프로그램 인사이트.샘플링설정](#applicationinsightssamplingsettings)을 참조하십시오. |
| 사용 가능라이브메트릭스 | true | 라이브 메트릭 컬렉션을 활성화합니다. |
| 사용 자종 속성 추적 | true | 종속성 추적을 활성화합니다. |
| 사용 가능성능 카운터컬렉션 | true | Kudu 성능 카운터 컬렉션을 활성화합니다. |
| 라이브메트릭초기화지연 | 00:00:15 | 내부 전용입니다. |
| http자동수집옵션 | 해당 없음 | [응용 프로그램 인사이트.http자동 컬렉션 옵션을](#applicationinsightshttpautocollectionoptions)참조하십시오. |
| 스냅샷 구성 | 해당 없음 | [응용 프로그램 Insights.snapshot 구성을](#applicationinsightssnapshotconfiguration)참조하십시오. |

### <a name="applicationinsightssamplingsettings"></a>애플리케이션인사이트.샘플링설정

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| isEnabled | true | 샘플링을 사용 여부를 설정합니다. | 
| maxTelemetryItemsPerSecond | 20 | 각 서버 호스트에서 초당 기록된 원격 분석 항목의 대상 수입니다. 앱이 여러 호스트에서 실행되는 경우 이 값을 줄이면 전체 목표 트래픽 속도 내에 유지됩니다. | 
| 평가간격 | 01:00:00 | 현재 원격 분석 속도를 다시 평가하는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다. |
| 초기 샘플링백분율| 1.0 | 샘플링 프로세스의 시작 부분에 적용된 초기 샘플링 백분율은 백분율을 동적으로 변경합니다. 디버깅하는 동안 값을 줄이지 마십시오. |
| 샘플링비율증가시간 | 00:00:01 | 샘플링 백분율 값이 변경되면 이 속성은 나중에 Application Insights에서 샘플링 백분율을 다시 인상하여 더 많은 데이터를 캡처할 수 있는 방법을 결정합니다. |
| 샘플링백분율감소시간 | 00:00:01 | 샘플링 백분율 값이 변경되면 이 속성은 나중에 Application Insights가 샘플링 백분율을 다시 낮게 하여 더 적은 데이터를 캡처할 수 있는 방법을 결정합니다. |
| 최소 샘플링 백분율 | 0.1 | 샘플링 백분율이 다양하므로 이 속성은 허용되는 최소 샘플링 백분율을 결정합니다. |
| 최대 샘플링 백분율 | 0.1 | 샘플링 백분율이 다양하므로 이 속성은 허용되는 최대 샘플링 백분율을 결정합니다. |
| 이동평균비율 | 1.0 | 이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다. |
| 제외유형 | null | 샘플링하지 않으려는 세미콜론으로 구분된 형식 목록입니다. 인식된 형식은 `Dependency`" `PageView` `Request`, `Trace`" `Event` `Exception`, " 지정된 형식의 모든 인스턴스가 전송됩니다. 지정되지 않은 형식이 샘플링됩니다. |
| 포함유형 | null | 샘플링할 세미콜론 으로 구분된 형식 목록입니다. 빈 목록은 모든 형식을 의미합니다. 여기에 나열된 `excludedTypes` 재정의 유형에 나열된 형식입니다. 인식된 형식은 `Dependency`" `PageView` `Request`, `Trace`" `Event` `Exception`, " 지정된 형식의 인스턴스가 샘플링됩니다. 지정되거나 암시되지 않은 형식은 샘플링 없이 전송됩니다. |

### <a name="applicationinsightshttpautocollectionoptions"></a>응용 프로그램인사이트.httpAutoCollection옵션

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| 사용 자http트리거확장정보컬렉션 | true | 들어오는 요청 상관 헤더, 다중 계측 키 지원, HTTP 메서드, 경로 및 응답과 같은 HTTP 트리거에 대한 확장된 HTTP 요청 정보를 활성화하거나 사용하지 않도록 설정합니다. |
| 사용 가능W3C분산Tracing | true | W3C 분산 추적 프로토콜의 지원을 활성화하거나 사용하지 않도록 설정합니다(레거시 상관 관계 스키마 켜기). true인 경우 `enableHttpTriggerExtendedInfoCollection` 기본적으로 활성화됩니다. false인 경우 `enableHttpTriggerExtendedInfoCollection` 이 플래그는 들어오는 요청이 아닌 나가는 요청에만 적용됩니다. |
| 사용 자응답헤더 주입 | true | 다중 구성 요소 상관 관계 헤더를 응답에 삽입하거나 사용하지 않도록 설정합니다. 인젝션을 사용하면 응용 프로그램 인사이트가 여러 계측 키를 사용할 때 응용 프로그램 맵을 생성할 수 있습니다. true인 경우 `enableHttpTriggerExtendedInfoCollection` 기본적으로 활성화됩니다. 이 설정은 false인 `enableHttpTriggerExtendedInfoCollection` 경우 적용되지 않습니다. |

### <a name="applicationinsightssnapshotconfiguration"></a>애플리케이션인사이트.스냅샷구성

스냅샷에 대한 자세한 내용은 [.NET 앱의 예외에 대한 스냅숏 디버그](/azure/azure-monitor/app/snapshot-debugger) 를 참조하고 [문제 해결 을 통해 응용 프로그램 인사이트 스냅숏 디버거 또는 스냅숏 을 볼 수 있습니다.](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| 에이전트엔드포인트 | null | 응용 프로그램 인사이트 스냅숏 디버거 서비스에 연결하는 데 사용되는 끝점입니다. null이면 기본 끝점이 사용됩니다. |
| 캡처스냅스냅샷메모리웨이트 | 0.5 | 스냅숏을 생성할 메모리가 충분한지 확인할 때 현재 프로세스 메모리 크기에 부여된 가중치입니다. 예상 값은 0보다 큰 적절한 분수(0 < CaptureSnapshotMemoryWeight < 1)보다 큽니다. |
| failed요청리 | 3 | 원격 분석 프로세서를 사용하지 않도록 설정하기 전에 스냅숏을 요청하는 데 실패한 요청 수제한입니다.|
| 핸들 추적되지 않은예외 | true | Application Insights 원격 분석에서 추적하지 않는 예외의 추적을 활성화하거나 사용하지 않도록 설정합니다. |
| isEnabled | true | 스냅샷 수집 활성화 또는 비활성화 | 
| isEnabledIn 개발자 모드 | false | 개발자 모드에서 스냅샷 수집이 활성화되어 활성화또는 비활성화됩니다. |
| isEnabledWhen 프로파일링 | true | 응용 프로그램 인사이트 프로파일러가 자세한 프로파일링 세션을 수집하는 경우에도 스냅샷 생성을 활성화하거나 사용하지 않도록 설정합니다. |
| is예외스냅포인트사용 | false | 예외 필터링을 활성화하거나 사용하지 않도록 설정합니다. |
| isLow우선 순위스냅샷 업로더 | true | 스냅샷 업로더 프로세스를 정상 우선 순위 미만으로 실행할지 여부를 결정합니다. |
| 최대컬렉션플랜사이즈 | 50 | 1에서 9999까지의 범위에서 언제든지 추적 할 수있는 최대 문제 수입니다. |
| 최대스냅샷 필수 | 3 | 단일 문제에 대해 수집된 최대 스냅숏 수(1개에서 999까지)입니다. 문제는 응용 프로그램에서 개별 throw 문으로 생각할 수 있습니다. 문제에 대해 수집된 스냅숏 수가 이 값에 도달하면 문제 카운터가 재설정되고 `problemCounterResetInterval` `thresholdForSnapshotting` 제한에 다시 도달할 때까지 해당 문제에 대한 스냅숏이 더 이상 수집되지 않습니다. |
| 문제카운터리셋간격 | 24:00:00 | 1분에서 7일 까지의 범위에서 문제 카운터를 재설정하는 빈도 이 간격에 도달하면 모든 문제 수가 0으로 재설정됩니다. 스냅숏 을 수행하기 위한 임계값에 이미 도달했지만 아직 `maximumSnapshotsRequired`스냅숏 수를 생성하지 않은 기존 문제는 활성 상태로 유지됩니다. |
| 제공익명 텔레메트리 | true | 익명 사용 및 오류 원격 분석을 Microsoft에 보낼지 여부를 결정합니다. 이 원격 분석을 사용하면 Microsoft에 문의하여 스냅숏 디버거 문제를 해결하는 데 사용할 수 있습니다. 또한 사용 패턴을 모니터링하는 데 사용됩니다. |
| 다시 연결간격 | 00:15:00 | 스냅숏 디버거 끝점에 다시 연결하는 빈도입니다. 허용 범위는 1분에서 1일 사이입니다. |
| 섀도우카피폴더 | null | 그림자 복사 바이너리에 사용할 폴더를 지정합니다. 설정하지 않으면 Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP: 다음 환경 변수에 의해 지정된 폴더가 순서대로 시도됩니다. |
| 공유업자프로세스 | true | true이면 스냅샷 Uploader의 인스턴스 가 하나만 계측키키를 공유하는 여러 앱의 스냅샷을 수집하고 업로드합니다. false로 설정하면 스냅샷 업로더는 각 튜플(프로세스 이름, 계측키) 튜플에 대해 고유합니다. |
| 스냅샷인로우우선순위스레드 | true | IO 우선 순위가 낮은 스레드에서 스냅샷을 처리할지 여부를 결정합니다. 스냅숏을 만드는 것은 빠른 작업이지만 스냅숏 디버거 서비스에 스냅샷을 업로드하려면 먼저 디스크에 미니덤프로 기록해야 합니다. 스냅샷 업로더 프로세스에서 발생합니다. 이 값을 true로 설정하면 우선 순위가 낮은 IO를 사용하여 리소스에 대한 응용 프로그램과 경쟁하지 않는 미니 덤프를 작성합니다. 이 값을 false로 설정하면 응용 프로그램의 속도가 느려지도록 최소화덤프 생성 속도가 빨라집니다. |
| 스냅샷퍼데이제한 | 30 | 하루(24시간)에 허용되는 최대 스냅샷 수입니다. 이 제한은 응용 프로그램 인사이트 서비스 측면에서도 적용됩니다. 업로드 속도는 응용 프로그램당 하루 50개(즉, 계측 키당)로 제한됩니다. 이 값을 사용하면 업로드 하는 동안 결국 거부 될 추가 스냅샷을 만들지 방지 할 수 있습니다. 값이 0이면 제한을 완전히 제거하므로 권장되지 않습니다. |
| 스냅샷퍼텐분제한 | 1 | 10분 동안 허용되는 최대 스냅샷 수입니다. 이 값에 대한 상한은 없지만 응용 프로그램의 성능에 영향을 줄 수 있으므로 프로덕션 워크로드에서 증가시키십시오. 스냅숏을 만드는 것은 빠르지만 스냅숏의 미니 덤프를 만들고 스냅숏 디버거 서비스에 업로드하는 것은 리소스(CPU 및 I/O 모두)를 위해 응용 프로그램과 경쟁하는 훨씬 느린 작업입니다. |
| 임시 폴더 | null | 미니덤프 및 업로더 로그 파일을 작성하도록 폴더를 지정합니다. 설정하지 않으면 *%TEMP%\덤프가* 사용됩니다. |
| 임계값For스냅샷 | 1 | 스냅숏을 요청하기 전에 응용 프로그램 인사이트가 예외를 표시해야 하는 횟수입니다. |
| 업로더프록시 | null | 스냅숏 업로더 프로세스에 사용된 프록시 서버를 재정의합니다. 응용 프로그램이 프록시 서버를 통해 인터넷에 연결되는 경우 이 설정을 사용해야 할 수 있습니다. 스냅숏 수집기는 응용 프로그램의 프로세스 내에서 실행되며 동일한 프록시 설정을 사용합니다. 그러나 스냅숏 업로더는 별도의 프로세스로 실행되며 프록시 서버를 수동으로 구성해야 할 수 있습니다. 이 값이 null이면 스냅숏 수집기는 System.Net.WebRequest.DefaultWebProxy를 검사하고 값을 스냅숏 업로더에 전달하여 프록시 주소를 자동으로 검색하려고 시도합니다. 이 값이 null이 아닌 경우 자동 검색이 사용되지 않으며 여기에 지정된 프록시 서버가 스냅샷 업로더에서 사용됩니다. |

## <a name="cosmosdb"></a>cosmosDb

구성 설정은 [Cosmos DB 트리거 및 바인딩](functions-bindings-cosmosdb-v2-output.md#host-json)에서 찾을 수 있습니다.

## <a name="durabletask"></a>durableTask

구성 설정은 [지속형 함수에 대한 바인딩](durable/durable-functions-bindings.md#host-json)에서 찾을 수 있습니다.

## <a name="eventhub"></a>eventHub

구성 설정은 [이벤트 허브 트리거 및 바인딩](functions-bindings-event-hubs-trigger.md#host-json)에서 찾을 수 있습니다. 

## <a name="extensions"></a>확장

[http](#http), [eventHub](#eventhub) 등의 모든 바인딩 관련 설정이 포함된 개체를 반환하는 속성입니다.

## <a name="extensionbundle"></a>확장 번들 

확장 번들을 사용하면 함수 앱에 호환되는 함수 바인딩 확장 집합을 추가할 수 있습니다. 자세한 내용은 [지역 개발을 위한 확장 번들을](functions-bindings-register.md#extension-bundles)참조하십시오.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

작업 호스트가 실행하는 함수 목록입니다. 빈 배열은 모든 함수를 실행한다는 의미입니다. [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. Azure의 함수 앱에서는 이 설정을 사용하는 대신 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md)의 단계를 수행하여 특정 함수를 사용하지 않도록 설정해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. 시간 범위 문자열 형식을 따릅니다. 서버리스 사용 계획에서 유효한 범위는 1초에서 10분 사이이고 기본값은 5분입니다.  

프리미엄 요금제에서 유효한 범위는 1초에서 60분사이이며 기본값은 30분입니다.

전용(App Service) 요금제에는 전체 제한이 없으며 기본값은 30분입니다. 값은 `-1` 무한 실행을 나타내지만 고정된 상한을 유지하는 것이 좋습니다.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[호스트 상태 모니터](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)에 대한 구성 설정

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|사용|true|기능의 사용 여부를 지정합니다. | 
|healthCheckInterval|10초|정기적인 백그라운드 상태 검사 사이의 간격 | 
|healthCheckWindow|2분|`healthCheckThreshold` 설정과 함께 사용되는 슬라이딩 시간 범위| 
|healthCheckThreshold|6|호스트 재생이 시작되기 전에 상태 검사 실패가 용인되는 최대 횟수| 
|counterThreshold|0.80|성능 카운터가 비정상으로 간주되는 임계값| 

## <a name="http"></a>http

구성 설정은 [http 트리거 및 바인딩](functions-bindings-http-webhook-output.md#hostjson-settings)에서 찾을 수 있습니다.

## <a name="logging"></a>logging

Application Insights를 포함한 함수 앱의 로깅 동작을 제어합니다.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
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

|속성  |기본값 | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|활성화할 파일 로깅의 수준을 정의합니다.  옵션은 `never`, `always`, `debugOnly`입니다. |
|logLevel|해당 없음|앱의 함수에 대한 로그 범주 필터링을 정의하는 개체입니다. 버전 2.x 이후 로그 범주 필터링에 대 한 ASP.NET 코어 레이아웃을 따릅니다. 이 설정을 사용하면 특정 함수에 대한 로깅을 필터링할 수 있습니다. 자세한 내용은 ASP.NET Core 설명서의 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)을 참조하세요. |
|console|해당 없음| [콘솔](#console) 로깅 설정입니다. |
|applicationInsights|해당 없음| [applicationInsights](#applicationinsights) 설정입니다. |

## <a name="console"></a>console

이 설정은 [logging](#logging)의 자식입니다. 디버깅 모드가 아닌 경우 콘솔 로깅을 제어합니다.

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|isEnabled|false|콘솔 로깅을 사용하거나 사용하지 않도록 설정합니다.| 

## <a name="manageddependency"></a>관리종속성

관리되는 종속성은 현재 PowerShell 기반 기능에서만 지원되는 기능입니다. 이를 통해 종속성을 서비스에서 자동으로 관리할 수 있습니다. 속성이 `enabled` `true`로 설정되면 `requirements.psd1` 파일이 처리됩니다. 종속성은 부 버전이 릴리스되면 업데이트됩니다. 자세한 내용은 PowerShell 문서에서 [관리되는 종속성을](functions-reference-powershell.md#dependency-management) 참조하십시오.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

구성 설정은 [스토리지 큐 트리거 및 바인딩](functions-bindings-storage-queue-output.md#host-json)에서 찾을 수 있습니다.  

## <a name="sendgrid"></a>sendGrid

구성 설정은 [SendGrid 트리거 및 바인딩](functions-bindings-sendgrid.md#host-json)에서 찾을 수 있습니다.

## <a name="servicebus"></a>serviceBus

구성 설정은 [Service Bus 트리거 및 바인딩](functions-bindings-service-bus-output.md#host-json)에서 찾을 수 있습니다.

## <a name="singleton"></a>singleton

Singleton 잠금 동작에 대한 구성 설정입니다. 자세한 내용은 [singleton 지원에 대한 GitHub 문제](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)를 참조하세요.

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 적용되는 기간입니다. 잠금은 자동 갱신됩니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 적용되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작할 때 수신기 잠금을 가져올 수 없는 경우 수신기 잠금 복구에 사용되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 확보하려고 시도하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|해당 없음|잠금 확보 시도 사이의 간격입니다.| 

## <a name="version"></a>버전

이 값은 host.json의 스키마 버전을 나타냅니다. 버전 문자열은 `"version": "2.0"` v2 런타임 또는 이후 버전을 대상으로 하는 함수 앱에 필요합니다. v2와 v3 사이에는 host.json 스키마 변경 사항이 없습니다.

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이 디렉터리의 코드가 변경되면 변경 내용이 함수에 의해 선택되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [host.json 파일을 업데이트하는 방법 알아보기](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
