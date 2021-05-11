---
title: Application Insights의 이벤트 카운터 | Microsoft Docs
description: Application Insights에서 시스템 및 사용자 지정 .NET/.NET Core EventCounters 모니터링
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579535"
---
# <a name="eventcounters-introduction"></a>EventCounters 소개

[`EventCounter`](/dotnet/core/diagnostics/event-counters)는 카운터 또는 통계를 게시 및 소비하는 .NET/.NET Core 메커니즘입니다. EventCounters는 모든 OS 플랫폼(Windows, Linux 및 macOS)에서 지원됩니다. 이는 Windows 시스템에서만 지원되는 [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter)와 달리 플랫폼 간 지원에 해당하는 것으로 볼 수 있습니다.

사용자가 요구 사항을 충족하기 위해 사용자 지정 `EventCounters`를 게시할 수 있지만 .NET Core 3.0 이상의 런타임은 기본적으로 해당 카운터 집합을 게시합니다. 이 문서는 Azure Application Insights에서 `EventCounters`(시스템 정의 또는 사용자 정의)를 수집하고 확인하는 데 필요한 단계를 안내합니다.

## <a name="using-application-insights-to-collect-eventcounters"></a>Application Insights를 사용하여 EventCounters 수집

Application Insights는 새로 릴리스된 NuGet 패키지 [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)의 일부인 `EventCounterCollectionModule`을 사용한 `EventCounters` 수집을 지원합니다. `EventCounterCollectionModule`은 [AspNetCore](asp-net-core.md) 또는 [WorkerService](worker-service.md)를 사용하는 경우 자동으로 사용하도록 설정됩니다. `EventCounterCollectionModule`은 구성 불가능한 수집 빈도(60초)로 카운터를 수집합니다. EventCounters를 수집하는 데 필요한 특별 권한은 없습니다.

## <a name="default-counters-collected"></a>수집된 기본 카운터

[AspNetCore SDK](asp-net-core.md) 또는 [WorkerService SDK](worker-service.md)의 2.15.0 버전을 사용하여 시작하는 경우 기본적으로 카운터가 수집되지 않습니다. 모듈 자체를 사용할 수 있으므로 사용자가 원하는 카운터를 추가하여 수집하면 됩니다.

.NET 런타임에 게시된 잘 알려진 카운터 목록을 가져오려면 [사용 가능한 카운터](/dotnet/core/diagnostics/event-counters#available-counters) 문서를 참조하세요.

## <a name="customizing-counters-to-be-collected"></a>수집할 카운터 사용자 지정

다음 예에서는 카운터를 추가/제거하는 방법을 보여 줍니다. `AddApplicationInsightsTelemetry()` 또는 `AddApplicationInsightsWorkerService()`를 사용하여 Application Insights 원격 분석 수집을 사용하도록 설정한 후 애플리케이션의 `ConfigureServices` 메서드에서 이 사용자 지정을 수행합니다. ASP.NET Core 애플리케이션의 코드 예는 다음과 같습니다. 다른 유형의 애플리케이션에 대해서는 [이](worker-service.md#configuring-or-removing-default-telemetrymodules) 문서를 참조하세요.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>EventCounter 수집 모듈을 사용하지 않도록 설정

`EventCounterCollectionModule`은 `ApplicationInsightsServiceOptions`를 사용하여 사용하지 않도록 설정할 수 있습니다. ASP.NET Core SDK를 사용하는 예제는 아래와 같습니다.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

WorkerService SDK에도 유사한 방법을 사용할 수 있지만, 네임스페이스는 아래 예제에 표시된 것처럼 변경되어야 합니다.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>메트릭 탐색기의 이벤트 카운터

[메트릭 탐색기](../essentials/metrics-charts.md)에서 EventCounter 메트릭을 보려면 Application Insights 리소스를 선택하고 로그 기반 메트릭을 메트릭 네임스페이스로 선택합니다. 그러면 EventCounter 메트릭이 사용자 지정 범주에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![Application Insights 메트릭 Explorer에서 보고하는 이벤트 카운터](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics의 이벤트 카운터

[Analytics](../logs/log-query-overview.md)에서 **customMetrics** 테이블의 이벤트 카운터 보고서를 검색하고 표시할 수도 있습니다.

예를 들어 다음 쿼리를 실행하여 수집된 카운터 및 쿼리에 사용할 수 있는 카운터를 확인합니다.

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analytics에서 보고하는 이벤트 카운터](./media/event-counters/analytics-event-counters.png)

최근 기간 동안 특정 카운터 차트(예: `ThreadPool Completed Work Item Count`)를 가져오려면 다음 쿼리를 실행합니다.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights에서 단일 카운터의 채팅](./media/event-counters/analytics-completeditems-counters.png)

다른 원격 분석과 마찬가지로 **customMetrics** 에도 앱이 실행되는 호스트 서버 인스턴스의 ID를 나타내는 `cloud_RoleInstance` 열이 있습니다. 위의 쿼리는 인스턴스당 카운터 값을 표시하며 다른 서버 인스턴스의 성능을 비교하는 데 사용할 수 있습니다.

## <a name="alerts"></a>경고
다른 메트릭과 마찬가지로 이벤트 카운터에서 지정한 제한을 벗어나는 경우 경고 메시지를 표시하도록 [경고를 설정](../alerts/alerts-log.md)할 수 있습니다. [경고] 창을 열고 [경고 추가]를 클릭합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-see-eventcounters-in-live-metrics"></a>라이브 메트릭의 EventCounters를 볼 수 있나요?

라이브 메트릭은 현재 EventCounters를 표시하지 않습니다. 원격 분석을 보려면 메트릭 탐색기 또는 Analytics를 사용합니다.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure Web App 포털에서 Application Insights를 사용하도록 설정했습니다. 하지만 EventCounters를 볼 수 없습니다.

 ASP.NET Core용 [Application Insights 확장](./azure-web-apps.md)은 아직 이 기능을 지원하지 않습니다. 이 기능이 지원되면 이 문서가 업데이트됩니다.

## <a name="next-steps"></a><a name="next"></a>다음 단계

* [종속성 추적](./asp-net-dependencies.md)

