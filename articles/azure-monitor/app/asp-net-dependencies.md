---
title: Azure Application Insights의 종속성 추적 | Microsoft Docs
description: 온-프레미스 또는 Application Insights를 사용 하 여 Microsoft Azure 웹 응용 프로그램에서 종속성 호출을 모니터링 합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565380"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure Application Insights에서 종속성 추적 

*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. [Application Insights](../../azure-monitor/app/app-insights-overview.md) 여부 종속성 호출 기간을 측정 해당 실패를 종속성의 이름 등 추가 정보와 등입니다. 특정 종속성 호출을 조사할 수 있으며 상관 관계를 요청 및 예외를 지정할 수 있습니다.

## <a name="automatically-tracked-dependencies"></a>자동으로 추적 된 종속성

과 함께 제공 되며.NET 및.NET Core에 대 한 application Insights Sdk `DependencyTrackingTelemetryModule` 는 종속성을 자동으로 수집 하는 원격 분석 모듈입니다. 이 종속성 컬렉션에 대 한 자동으로 설정 됩니다 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 하 고 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 응용 프로그램을 연결 된 공식 문서에 따라 구성 하는 경우. `DependencyTrackingTelemetryModule` 요소로 제공 되며 [이](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet 패키지, NuGet 패키지 중 하나를 사용 하는 경우 자동으로 상태가 되 고 `Microsoft.ApplicationInsights.Web` 또는 `Microsoft.ApplicationInsights.AspNetCore`합니다.

 `DependencyTrackingTelemetryModule` 현재 다음 종속성을 자동으로 추적합니다.

|종속성 |세부 정보|
|---------------|-------|
|Http/Https | 로컬 또는 원격 http/https 호출 |
|WCF 호출| Http 기반 바인딩을 사용 하는 경우 자동으로 추적만 합니다.|
|SQL | 사용 하 여 호출 `SqlClient`합니다. 참조 [이](##advanced-sql-tracking-to-get-full-sql-query) SQL 캡처를 위해 쿼리 합니다.  |
|[Azure storage (Blob, 테이블, 큐)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage 클라이언트를 사용 하 여 호출 합니다. |
|[EventHub 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 버전 1.1.0 이상. |
|[ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 버전 3.0.0 이상. |
|Azure Cosmos DB | HTTP/HTTPS를 사용 하는 경우 자동으로 추적만 합니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>자동 종속성 추적 콘솔 응용 프로그램에서 설치

.NET/.NET Core 콘솔 앱에서 종속성을 자동으로 추적, Nuget 패키지를 설치 `Microsoft.ApplicationInsights.DependencyCollector`, 초기화 및 `DependencyTrackingTelemetryModule` 다음과 같습니다.

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>작동을 모니터링 하는 방법을 자동 종속성?

종속성은 다음 방법 중 하나를 사용 하 여 자동으로 수집 됩니다.

* 선택 메서드 주위 바이트 코드 계측을 사용합니다. (InstrumentationEngine StatusMonitor 또는 Azure 웹 앱 확장에서)
* EventSource 콜백
* DiagnosticSource 콜백을 (에서 최신.NET/.NET Core Sdk)

## <a name="manually-tracking-dependencies"></a>수동으로 종속성 추적

다음은 몇 가지 예가 종속성을 자동으로 수집 되지 않습니다 하 고 따라서 수동 추적 하려면 필요 합니다.

* Azure Cosmos DB는 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking)를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다.
* Redis

SDK에서 자동으로 수집 된 이러한 종속성을 사용 하 여 수동으로 추적할 수 있습니다 합니다 [TrackDependency API](api-custom-events-metrics.md#trackdependency) 자동 표준 컬렉션 모듈에 의해 사용 되는 합니다.

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

한편 `TelemetryClient` 확장 메서드를 제공 `StartOperation` 하 고 `StopOperation` 사용할 수 있는 수동으로 종속성을 추적 하려면 표시 된 것 처럼 [여기](custom-operations-tracking.md#outgoing-dependencies-tracking)

표준 종속성 추적 모듈을 해제 하려는 경우에서 DependencyTrackingTelemetryModule에 대 한 참조를 제거 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ASP.NET 응용 프로그램에 대 한 합니다. ASP.NET Core 응용 프로그램에 대 한 지침을 따릅니다 [여기](asp-net-core.md#configuring-or-removing-default-telemetrymodules)합니다.

## <a name="tracking-ajax-calls-from-web-pages"></a>웹 페이지의 AJAX 호출 추적

웹 페이지 용 Application Insights JavaScript SDK를 자동으로 수집 AJAX 호출이 종속성으로 설명 된 대로 [여기](javascript.md#ajax-performance)합니다. 이 문서는 서버 구성 요소에서 종속성에 중점을 둡니다.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>고급 SQL 추적에서 전체 SQL 쿼리를 가져옵니다.

SQL 호출에 대 한 서버 및 데이터베이스의 이름을 항상 수집 되어 수집 된 이름으로 저장 `DependencyTelemetry`합니다. 추가 필드인 '데이터' 전체 SQL 쿼리 텍스트를 포함할 수 있습니다.

ASP.NET Core 응용 프로그램에 대 한는 전체 SQL 쿼리 하는 데 필요한 추가 단계가 없습니다.

ASP.NET 응용 프로그램에 대 한 전체 SQL 쿼리 엔진 계측 해야 하는 바이트 코드 계측을 사용 하 여 수집 됩니다. 추가 플랫폼별, 아래 설명 된 대로 단계가 필요 합니다.

| 플랫폼 | 전체 SQL 쿼리 하는 데 필요한 단계 |
| --- | --- |
| Azure 웹앱 |사용자 웹 앱 제어판에서 [Application Insights 블레이드를 열고](../../azure-monitor/app/azure-web-apps.md) .net SQL 명령을 사용 하도록 설정 하 고 |
| IIS 서버 (Azure VM, 온-프레미스, 및 등입니다.) | [응용 프로그램이 실행 중인 서버에 상태 모니터 설치](../../azure-monitor/app/monitor-performance-live-website-now.md) IIS를 다시 시작 합니다.
| Azure 클라우드 서비스 | 추가 [StatusMonitor를 설치 하 여 시작 작업](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 앱에 대 한 NuGet 패키지를 설치 하 여 빌드 시 application Insights SDK에는 등록에 이어야 합니다 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 또는 [ASP.NET Core 응용 프로그램](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | 지원되지 않음

위의 경우에는 해당 계측 엔진 유효성을 검사 하는 올바른 방법은 올바르게 설치은 수집 된 SDK 버전의 유효성을 검사 하 여 `DependencyTelemetry` 'rddp' 됩니다. 'rdddsd' 또는 'rddf' 종속성 DiagnosticSource 또는 EventSource 콜백을 통해 수집 되 고 따라서 전체 SQL 쿼리를 캡처할 수 없습니다 나타냅니다.

## <a name="where-to-find-dependency-data"></a>종속성 데이터를 찾을 수 있는 위치

* [애플리케이션 맵](app-map.md)은 앱과 인접 구성 요소 간의 종속성을 시각화합니다.
* [트랜잭션 진단](transaction-diagnostics.md) 통합, 서버 데이터를 상호 관련을 보여 줍니다.
* [브라우저 탭](javascript.md#ajax-performance) 사용자의 브라우저에서 AJAX 호출을 보여 줍니다.
* 느리거나 실패한 요청 클릭: 해당 종속성 호출을 확인합니다.
* [분석](#logs-analytics): 종속성 데이터를 쿼리하는 데 사용됩니다.

## <a name="diagnosis"></a> 느린 요청 진단

각 요청 이벤트는 종속성 호출, 예외 및 앱은 요청을 처리 하는 동안 추적 되는 다른 이벤트와 연결 합니다. 따라서 일부 요청이 잘못 수행 하는 경우에 종속성의 느린 응답 때문 인지 확인할 수 있습니다.

### <a name="tracing-from-requests-to-dependencies"></a>요청에서 종속성까지 추적

엽니다는 **성능** 탭을 이동할 합니다 **종속성** 작업 옆에 있는 맨 위에 있는 탭.

클릭 된 **종속성 이름** 전체에서. 종속성을 선택 하면 해당 종속성의 기간 분포 그래프 오른쪽에 표시 됩니다.

![성능에서 탭 위쪽 차트에서 종속성 이름 다음에 종속성 탭에서 클릭](./media/asp-net-dependencies/2-perf-dependencies.png)

파란색 **샘플** 단추 오른쪽 아래에 다음 종단 간 트랜잭션 세부 정보를 보려면 샘플입니다.

![종단 간 트랜잭션 세부 정보를 보려면 샘플 클릭](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>라이브 사이트 프로파일링

시간에 따른 위치를 알 수 없나요? 합니다 [Application Insights profiler](../../azure-monitor/app/profiler.md) 추적 HTTP 라이브 사이트에 대 한 호출 및 가장 오래 걸린 코드의 함수를 보여 줍니다.

## <a name="failed-requests"></a>실패한 요청

실패한 요청은 종속성에 대한 실패한 호출과 연관이 있을 수도 있습니다.

살펴볼 수 있습니다 합니다 **오류** 왼쪽에 탭을 클릭 합니다 **종속성** 맨 위에 있는 탭입니다.

![실패한 요청 차트 클릭](./media/asp-net-dependencies/4-fail.png)

여기서 실패 한 종속성 수를 확인 수 있습니다. 아래 테이블의 종속성 이름을 클릭 하면 시도 실패 한 항목에 대 한 자세한 세부 정보를 가져올 수 파란색을 클릭할 수 있습니다 **종속성** 종단 간 트랜잭션 세부 정보를 가져오려면 오른쪽 아래에 있는 단추입니다.

## <a name="logs-analytics"></a>로그 (분석)

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*어떻게 않습니다 자동 종속성 수집기 보고서 종속성 호출 실패 했습니까?*

* 실패 한 종속성 호출에는 '성공' 필드가 False로 설정 해야 합니다. `DependencyTrackingTelemetryModule` 보고 하지 않습니다 `ExceptionTelemetry`합니다. 종속성에 대 한 전체 데이터 모델은 설명 [여기](data-model-dependency-telemetry.md)합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
모든 Application Insights SDK를 사용 하 여 같은 종속성 컬렉션 모듈은 오픈 소스 이기도 합니다. 읽기 및 코드 기여 또는 문제 보고 [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet-server)합니다.

## <a name="next-steps"></a>다음 단계

* [예외](../../azure-monitor/app/asp-net-exceptions.md)
* [사용자 및 페이지 데이터](../../azure-monitor/app/javascript.md)
* [Availability](../../azure-monitor/app/monitor-web-app-availability.md)
