---
title: Application Insights에서 이벤트 카운터 | Microsoft Docs
description: Application Insights에서 시스템 및 사용자 지정 .NET/.NET Core 카운터를 모니터링 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 5a47f5c2f9c9d4e22e8205853d85214997a2bea7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406913"
---
# <a name="eventcounters-introduction"></a>EventCounters 소개

`EventCounter`는 카운터 또는 통계를 게시 하 고 사용 하는 .NET/.NET Core 메커니즘입니다. [이](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) 문서에서는 `EventCounters`에 대 한 개요와 게시 및 사용 방법에 대 한 예제를 제공 합니다. EventCounters는 Windows, Linux 및 macOS의 모든 OS 플랫폼에서 지원 됩니다. 이는 Windows 시스템 에서만 지원 되는 [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) 에 대 한 플랫폼 간 동등으로 간주할 수 있습니다.

사용자가 요구 사항을 충족 하기 위해 사용자 지정 `EventCounters`를 게시할 수 있지만 .NET Core 3.0 런타임은 기본적으로 이러한 카운터 집합을 게시 합니다. 이 문서에서는 Azure 애플리케이션 Insights에서 `EventCounters` (시스템 정의 또는 사용자 정의)를 수집 하 고 보는 데 필요한 단계를 안내 합니다.

## <a name="using-application-insights-to-collect-eventcounters"></a>Application Insights를 사용 하 여 EventCounters 수집

Application Insights는 새로 릴리스된 nuget 패키지의 일부인 `EventCounterCollectionModule`를 사용 하 여 `EventCounters` 수집을 지원 합니다. [EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) [AspNetCore 또는](asp-net-core.md) [서비스](worker-service.md)를 사용 하는 경우 `EventCounterCollectionModule` 자동으로 사용 하도록 설정 됩니다. `EventCounterCollectionModule`는 구성할 수 없는 컬렉션 빈도 60 초를 사용 하 여 카운터를 수집 합니다. EventCounters를 수집 하는 데 필요한 특별 한 권한이 없습니다.

## <a name="default-counters-collected"></a>수집 된 기본 카운터

.NET Core 3.0에서 실행 되는 앱의 경우 다음 카운터가 SDK에 의해 자동으로 수집 됩니다. 카운터 이름은 "Category | 형식이 됩니다. Counter ".

|범주 | 카운터|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> AspNetCore 범주의 카운터는 ASP.NET Core 응용 프로그램 에서만 추가 됩니다.

## <a name="customizing-counters-to-be-collected"></a>수집할 카운터 사용자 지정

다음 예제에서는 카운터를 추가/제거 하는 방법을 보여 줍니다. `AddApplicationInsightsTelemetry()` 또는 `AddApplicationInsightsWorkerService()`를 사용 하 여 Application Insights 원격 분석 컬렉션을 사용 하도록 설정한 후 응용 프로그램의 `ConfigureServices` 메서드에서이 사용자 지정을 수행 합니다. ASP.NET Core 응용 프로그램의 예제 코드는 다음과 같습니다. 다른 유형의 응용 프로그램은 [이](worker-service.md#configuring-or-removing-default-telemetrymodules) 문서를 참조 하세요.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>메트릭 탐색기의 이벤트 카운터

[메트릭 탐색기](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)에서 eventcounter 메트릭을 보려면 Application Insights 리소스를 선택 하 고 로그 기반 메트릭을 메트릭 네임 스페이스로 선택 합니다. 그런 다음 EventCounter 메트릭은 사용자 지정 범주에 표시 됩니다.

> [!div class="mx-imgBorder"]
> Application Insights](./media/event-counters/metrics-explorer-counter-list.png)에 보고 된 ![이벤트 카운터

## <a name="event-counters-in-analytics"></a>분석의 이벤트 카운터

[분석](../../azure-monitor/app/analytics.md)의 **custommetrics** 테이블에서 이벤트 카운터 보고서를 검색 하 고 표시할 수도 있습니다.

예를 들어 다음 쿼리를 실행 하 여 수집 되 고 쿼리에 사용할 수 있는 카운터를 확인 합니다.

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> Application Insights](./media/event-counters/analytics-event-counters.png)에 보고 된 ![이벤트 카운터

최근 기간 동안 특정 카운터 (예: `ThreadPool Completed Work Item Count`)의 차트를 가져오려면 다음 쿼리를 실행 합니다.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> Application Insights에서 단일 카운터의 채팅을 ![](./media/event-counters/analytics-completeditems-counters.png)

다른 원격 분석과 마찬가지로 **Custommetrics** 는 앱이 실행 되는 호스트 서버 인스턴스의 id를 나타내는 열 `cloud_RoleInstance` 있습니다. 위의 쿼리는 인스턴스당 카운터 값을 표시 하며 다른 서버 인스턴스의 성능을 비교 하는 데 사용할 수 있습니다.

## <a name="alerts"></a>경고
다른 메트릭과 마찬가지로 이벤트 카운터가 지정한 한도를 벗어나면 경고를 [설정할](../../azure-monitor/app/alerts.md) 수 있습니다. [경고] 창을 열고 [경고 추가]를 클릭합니다.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="can-i-see-eventcounters-in-live-metrics"></a>라이브 메트릭의 EventCounters를 볼 수 있나요?

라이브 메트릭은 오늘부터 EventCounters를 표시 하지 않습니다. 메트릭 탐색기 또는 분석을 사용 하 여 원격 분석을 볼 수 있습니다.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>.NET Core 3.0 카운터의 기본 목록을 볼 수 있는 플랫폼은 무엇 인가요?

EventCounter는 특별 한 권한이 필요 하지 않으며 모든 플랫폼에서 지원 됩니다. .NET Core 3.0는 지원 됩니다. 다음 내용이 포함됩니다.

* **운영 체제**: Windows, Linux 또는 macos
* **호스팅 메서드**: 프로세스 또는 프로세스를 진행 중입니다.
* **배포 방법**: 프레임 워크 종속 또는 자체 포함.
* **웹 서버**: IIS (인터넷 정보 서버) 또는 Kestrel.
* **호스팅 플랫폼**: Azure App Service, azure VM, Docker, Azure Kubernetes 서비스 (AKS) 등의 Web Apps 기능입니다.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure 웹 앱 포털에서 Application Insights를 사용 하도록 설정 했습니다. 하지만 EventCounters는 볼 수 없습니다.

 ASP.NET Core [Application Insights 확장](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) 은이 기능을 아직 지원 하지 않습니다. 이 기능이 지원 되는 경우이 문서가 업데이트 됩니다.

## <a name="next"></a>다음 단계

* [종속성 추적](../../azure-monitor/app/asp-net-dependencies.md)
