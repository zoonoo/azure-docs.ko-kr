---
title: 애플리케이션 인사이트 이벤트 카운터 | 마이크로 소프트 문서
description: 애플리케이션 인사이트내 시스템 및 사용자 지정 .NET/.NET 코어 이벤트 카운터를 모니터링합니다.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663592"
---
# <a name="eventcounters-introduction"></a>이벤트 카운터 소개

`EventCounter`은 .NET/.NET 코어 메커니즘을 사용하여 카운터 또는 통계를 게시하고 사용합니다. [이](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) 문서에서는 게시 `EventCounters` 및 사용 방법에 대한 개요와 예제를 제공합니다. 이벤트 카운터는 모든 OS 플랫폼에서 지원됩니다 - 윈도우, 리눅스, 맥 OS. Windows 시스템에서만 지원되는 [PerformanceCounter에](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) 해당하는 플랫폼 간 카운터로 생각할 수 있습니다.

사용자가 필요에 맞게 `EventCounters` 사용자 지정을 게시할 수 있지만 .NET Core 3.0 런타임은 기본적으로 이러한 카운터 집합을 게시합니다. 문서는 Azure 응용 프로그램 인사이트에서 `EventCounters` 수집 및 보기(시스템 정의 또는 사용자 정의)에 필요한 단계를 안내합니다.

## <a name="using-application-insights-to-collect-eventcounters"></a>애플리케이션 인사이트를 사용하여 이벤트 카운터 수집

응용 프로그램 `EventCounters` 인사이트는 `EventCounterCollectionModule`새로 릴리스된 nuget 패키지 [Microsoft.ApplicationInsights.EventCounterCollector의](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)일부인 을 통해 수집을 지원합니다. `EventCounterCollectionModule`[AspNetCore](asp-net-core.md) 또는 [WorkerService](worker-service.md)를 사용할 때 자동으로 활성화됩니다. `EventCounterCollectionModule`구성할 수 없는 수집 빈도가 60초인 카운터를 수집합니다. EventCounter를 수집하는 데 필요한 특별한 권한은 없습니다.

## <a name="default-counters-collected"></a>수집된 기본 카운터

.NET Core 3.0에서 실행되는 앱의 경우 다음 카운터는 SDK에서 자동으로 수집됩니다. 카운터의 이름은 "카테고리 | 카운터".

|Category | 카운터|
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
> 범주 의 카운터 Microsoft.AspNetCore.Hosting은 ASP.NET 핵심 응용 프로그램에만 추가됩니다.

## <a name="customizing-counters-to-be-collected"></a>수집할 카운터 사용자 지정

다음 예제에서는 카운터를 추가/제거하는 방법을 보여 주며, 이 사용자 지정은 응용 `ConfigureServices` 프로그램 인사이트 원격 분석 컬렉션을 사용 `AddApplicationInsightsTelemetry()` 하거나 `AddApplicationInsightsWorkerService()`을 사용 하 여 사용 하는 후 응용 프로그램의 메서드에서 수행 됩니다. 다음은 ASP.NET Core 응용 프로그램의 예제 코드입니다. 다른 유형의 응용 프로그램의 경우 [이](worker-service.md#configuring-or-removing-default-telemetrymodules) 문서를 참조하십시오.

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

[메트릭 탐색기에서](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)EventCounter 메트릭을 보려면 응용 프로그램 인사이트 리소스를 선택하고 로그 기반 메트릭을 메트릭 네임스페이스로 선택했습니다. 그런 다음 EventCounter 메트릭이 사용자 지정 범주 아래에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![응용 프로그램 인사이트에 보고된 이벤트 카운터](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>애널리틱스의 이벤트 카운터

사용자 지정 메트릭 테이블에서 [애널리틱스에서](../../azure-monitor/app/analytics.md)이벤트 카운터 보고서를 검색하고 표시할 수도 **있습니다.**

예를 들어 다음 쿼리를 실행하여 수집되고 쿼리할 수 있는 카운터를 확인합니다.

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![응용 프로그램 인사이트에 보고된 이벤트 카운터](./media/event-counters/analytics-event-counters.png)

최근 기간 동안 특정 카운터(예: `ThreadPool Completed Work Item Count`)의 차트를 얻으려면 다음 쿼리를 실행합니다.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![애플리케이션 인사이트 내 단일 카운터 채팅](./media/event-counters/analytics-completeditems-counters.png)

다른 원격 분석과 마찬가지로 **customMetrics에는** 앱이 실행 중인 호스트 서버 인스턴스의 ID를 나타내는 열도 `cloud_RoleInstance` 있습니다. 위의 쿼리는 인스턴스당 카운터 값을 보여 주며 다른 서버 인스턴스의 성능을 비교하는 데 사용할 수 있습니다.

## <a name="alerts"></a>경고
다른 측정항목과 마찬가지로 이벤트 [카운터가 지정한](../../azure-monitor/app/alerts.md) 제한을 벗어나면 경고하는 경고를 설정할 수 있습니다. [경고] 창을 열고 [경고 추가]를 클릭합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-see-eventcounters-in-live-metrics"></a>라이브 측정항목에서 이벤트 카운터를 볼 수 있나요?

라이브 메트릭은 오늘 현재 이벤트 카운터를 표시하지 않습니다. 메트릭 탐색기 또는 분석을 사용하여 원격 분석을 확인합니다.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>.NET Core 3.0 카운터의 기본 목록은 어떤 플랫폼으로 볼 수 있습니까?

EventCounter는 특별한 권한이 필요하지 않으며 모든 플랫폼에서 지원됩니다 .NET Core 3.0이 지원됩니다. 다음 내용이 포함됩니다.

* **운영 체제**: 윈도우, 리눅스, 또는 맥 OS.
* **호스팅 방법**: 프로세스 또는 프로세스 중.
* **배포 방법**: 프레임워크 종속 또는 독립형입니다.
* **웹 서버**: IIS (인터넷 정보 서버) 또는 케스트렐.
* **호스팅 플랫폼**: Azure 앱 서비스, Azure VM, Docker, Azure Kubernetes 서비스(AKS) 등의 웹 앱 기능입니다.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure 웹 앱 포털에서 응용 프로그램 정보를 사용하도록 설정했습니다. 하지만 이벤트 카운터를 볼 수 없습니다.?

 ASP.NET 코어에 대한 [응용 프로그램 인사이트 확장은](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) 아직이 기능을 지원하지 않습니다. 이 기능이 지원되면 이 문서가 업데이트됩니다.

## <a name="next-steps"></a><a name="next"></a>다음 단계

* [종속성 추적](../../azure-monitor/app/asp-net-dependencies.md)
