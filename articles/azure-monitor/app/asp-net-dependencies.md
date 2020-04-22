---
title: Azure Application Insights의 종속성 추적 | Microsoft Docs
description: 응용 프로그램 인사이트를 통해 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 종속성 호출을 모니터링합니다.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731502"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure 응용 프로그램 인사이트내 종속성 추적 

*종속성은* 응용 프로그램에서 호출하는 외부 구성 요소입니다. 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. [Application Insights는](../../azure-monitor/app/app-insights-overview.md) 종속성 호출의 실패 여부와 종속성 이름 등의 추가 정보와 함께 종속성 호출의 기간을 측정합니다. 특정 종속성 호출을 조사하고 요청 및 예외와 상관 관계를 지정할 수 있습니다.

## <a name="automatically-tracked-dependencies"></a>종속성 자동 추적

.NET 및 .NET Core에 `DependencyTrackingTelemetryModule` 대한 응용 프로그램 인사이트 SDK는 자동으로 종속성을 수집하는 원격 분석 모듈입니다. 이 종속성 컬렉션은 연결된 공식 문서에 따라 구성된 경우 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 및 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 응용 프로그램에 대해 자동으로 활성화됩니다. `DependencyTrackingTelemetryModule` [이](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet 패키지로 배송되며 NuGet 패키지 `Microsoft.ApplicationInsights.Web` 또는 `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule`현재 다음 종속성을 자동으로 추적합니다.

|종속성 |세부 정보|
|---------------|-------|
|Http/Https | 로컬 또는 원격 http/https 호출 |
|WCF 호출| Http 기반 바인딩을 사용하는 경우에만 자동으로 추적됩니다.|
|SQL | 로 걸려온 전화. `SqlClient` SQL 쿼리를 캡처하려면 [이 것을](#advanced-sql-tracking-to-get-full-sql-query) 참조하십시오.  |
|[Azure 저장소(Blob, 테이블, 큐)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure 저장소 클라이언트로 만든 호출입니다. |
|[이벤트 허브 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 버전 1.1.0 이상. |
|[ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 버전 3.0.0 이상. |
|Azure Cosmos DB | HTTP/HTTPS를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다. |

종속성이 없거나 다른 SDK를 사용하는 경우 [자동으로 수집된 종속성](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)목록에 있는지 확인합니다. 종속성이 자동으로 수집되지 않은 경우에도 [트랙 종속성 호출을](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)통해 수동으로 추적할 수 있습니다.

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>콘솔 앱에서 설정 자동 종속성 추적

.NET 콘솔 앱의 종속성을 자동으로 추적하려면 Nuget `Microsoft.ApplicationInsights.DependencyCollector`패키지를 설치하고 `DependencyTrackingTelemetryModule` 다음과 같이 초기화합니다.

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

.NET 코어 콘솔 앱의 경우 TelemetryConfiguration.Active가 더 이상 사용되지 않습니다. [작업자 서비스 설명서의](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) 지침 및 [ASP.NET 핵심 모니터링 설명서](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 참조

### <a name="how-automatic-dependency-monitoring-works"></a>자동 종속성 모니터링은 어떻게 작동합니까?

종속성은 다음 기술 중 하나를 사용하여 자동으로 수집됩니다.

* 선택 방법 주위에 바이트 코드 계측을 사용. (상태 모니터 또는 Azure 웹 앱 확장에서 계측 엔진)
* 이벤트 소스 콜백
* 진단 소스 콜백(최신 .NET/.NET 코어 SDK)

## <a name="manually-tracking-dependencies"></a>종속성 수동으로 추적

다음은 자동으로 수집되지 않으므로 수동 추적이 필요한 종속성의 몇 가지 예입니다.

* Azure Cosmos DB는 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking)를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다.
* Redis

SDK에서 자동으로 수집하지 않는 종속성의 경우 표준 자동 수집 모듈에서 사용하는 [TrackDependency API를](api-custom-events-metrics.md#trackdependency) 사용하여 수동으로 추적할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

또는 여기에 `TelemetryClient` 표시된 `StartOperation` 것처럼 `StopOperation` 확장 메서드를 제공하고 종속성을 수동으로 추적하는 [here](custom-operations-tracking.md#outgoing-dependencies-tracking) 데 사용할 수 있습니다.

표준 종속성 추적 모듈을 끄려면 응용 프로그램 [Insights.config에서 ASP.NET.config에서](../../azure-monitor/app/configuration-with-applicationinsights-config.md) DependencyTrackingTelemetryModule에 대한 참조를 제거합니다. ASP.NET 핵심 응용 프로그램의 경우 [여기에](asp-net-core.md#configuring-or-removing-default-telemetrymodules)지침을 따르십시오.

## <a name="tracking-ajax-calls-from-web-pages"></a>웹 페이지에서 AJAX 통화 추적

웹 페이지의 경우 응용 프로그램 인사이트 자바스크립트 SDK는 AJAX 호출을 종속성으로 자동으로 수집합니다.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>전체 SQL 쿼리를 얻기 위한 고급 SQL 추적

SQL 호출의 경우 서버 및 데이터베이스의 이름은 항상 수집되고 `DependencyTelemetry`수집된 의 이름으로 저장됩니다. 전체 SQL 쿼리 텍스트를 포함할 수 있는 '데이터'라는 추가 필드가 있습니다.

ASP.NET 핵심 응용 프로그램의 경우 전체 SQL 쿼리를 얻는 데 필요한 추가 단계가 없습니다.

ASP.NET 응용 프로그램의 경우 계측 엔진이 필요한 바이트 코드 계측을 사용하여 전체 SQL 쿼리가 수집됩니다. 아래에 설명된 대로 추가 플랫폼별 단계가 필요합니다.

| 플랫폼 | 전체 SQL 쿼리를 얻는 데 필요한 단계 |
| --- | --- |
| Azure 웹앱 |웹 앱 제어판에서 [응용 프로그램 인사이트 블레이드를 열고](../../azure-monitor/app/azure-web-apps.md) .NET 에서 SQL 명령을 사용하도록 설정합니다. |
| IIS 서버(Azure VM, 온-프레임 등) | 상태 모니터 PowerShell 모듈을 사용하여 [계측 엔진을 설치하고 IIS를](../../azure-monitor/app/status-monitor-v2-api-reference.md) 다시 시작합니다. |
| Azure 클라우드 서비스 | 시작 작업을 추가하여 [상태 모니터 설치](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 응용 프로그램은 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 또는 ASP.NET 핵심 응용 프로그램에 대한 NuGet 패키지를 설치하여 빌드 시간에 ApplicationInsights SDK에 온보딩되어야 [합니다.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | 지원되지 않음

위의 경우 계측 엔진이 올바르게 설치되어 있는지 확인하는 올바른 방법은 수집된 `DependencyTelemetry` SDK 버전이 'rddp'인것을 확인하는 것입니다. 'rddsd' 또는 'rddf'는 DiagnosticSource 또는 EventSource 콜백을 통해 종속성이 수집됨을 나타내므로 전체 SQL 쿼리가 캡처되지 않습니다.

## <a name="where-to-find-dependency-data"></a>종속성 데이터를 찾을 수 있는 위치

* [애플리케이션 맵](app-map.md)은 앱과 인접 구성 요소 간의 종속성을 시각화합니다.
* [트랜잭션 진단에는](transaction-diagnostics.md) 상호 연관된 통합 서버 데이터가 표시됩니다.
* [브라우저 탭에는](javascript.md) 사용자의 브라우저에서 AJAX 호출이 표시됩니다.
* 느리거나 실패한 요청 클릭: 해당 종속성 호출을 확인합니다.
* [분석](#logs-analytics): 종속성 데이터를 쿼리하는 데 사용됩니다.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> 느린 요청 진단

각 요청 이벤트는 앱이 요청을 처리하는 동안 추적되는 종속성 호출, 예외 및 기타 이벤트와 연결됩니다. 따라서 일부 요청이 잘못 수행되는 경우 종속성의 느린 응답 때문인지 여부를 확인할 수 있습니다.

### <a name="tracing-from-requests-to-dependencies"></a>요청에서 종속성까지 추적

**성능** 탭을 열고 작업 옆상단의 **종속성** 탭으로 이동합니다.

전체 아래에서 **종속성 이름을** 클릭합니다. 종속성을 선택하면 해당 종속성의 기간 분포 그래프가 오른쪽에 표시됩니다.

![성능 탭에서 상단의 종속성 탭을 클릭한 다음 차트에서 종속성 이름을 클릭합니다.](./media/asp-net-dependencies/2-perf-dependencies.png)

오른쪽 하단의 파란색 **샘플 단추를** 클릭한 다음 샘플에서 종단 간 트랜잭션 세부 정보를 확인합니다.

![종단 간 거래 세부 정보를 보려면 샘플을 클릭하십시오.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>라이브 사이트 프로파일링

시간에 따른 위치를 알 수 없나요? [Application Insights 프로파일러는](../../azure-monitor/app/profiler.md) 라이브 사이트에 대한 HTTP 호출을 추적하고 가장 긴 시간이 걸린 코드의 함수를 보여 줍니다.

## <a name="failed-requests"></a>실패한 요청

실패한 요청은 종속성에 대한 실패한 호출과 연관이 있을 수도 있습니다.

왼쪽의 **실패** 탭으로 이동한 다음 상단의 **종속성** 탭을 클릭할 수 있습니다.

![실패한 요청 차트 클릭](./media/asp-net-dependencies/4-fail.png)

여기서 실패한 종속성 수를 볼 수 있습니다. 아래쪽 테이블의 종속성 이름을 클릭하여 실패한 발생에 대한 자세한 내용을 얻으려면 오른쪽 하단의 파란색 **종속성** 버튼을 클릭하여 종단 간 트랜잭션 세부 정보를 얻을 수 있습니다.

## <a name="logs-analytics"></a>로그(분석)

[Kusto 쿼리 언어](/azure/kusto/query/)에서 종속성을 추적할 수 있습니다. 다음은 몇 가지 예제입니다.

* 실패한 종속성 호출을 찾습니다.

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX 호출을 찾습니다.

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* 요청과 연관된 종속성 호출을 찾습니다.

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 페이지 보기와 관련된 AJAX 호출을 찾습니다.

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*자동 종속성 수집기 보고서는 종속성에 대한 호출에 실패한 방법을 보고합니다.*

* 실패한 종속성 호출에는 '성공' 필드가 False로 설정됩니다. `DependencyTrackingTelemetryModule`보고되지 `ExceptionTelemetry`않습니다. 종속성에 대한 전체 데이터 모델은 [여기에](data-model-dependency-telemetry.md)설명되어 있습니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
모든 응용 프로그램 인사이트 SDK와 마찬가지로 종속성 수집 모듈도 오픈 소스입니다. 코드를 읽고 기여하거나 [공식 GitHub 리포지토리에서](https://github.com/Microsoft/ApplicationInsights-dotnet-server)문제를 보고합니다.

## <a name="next-steps"></a>다음 단계

* [예외](../../azure-monitor/app/asp-net-exceptions.md)
* [사용자 및 페이지 데이터](../../azure-monitor/app/javascript.md)
* [가용성](../../azure-monitor/app/monitor-web-app-availability.md)
