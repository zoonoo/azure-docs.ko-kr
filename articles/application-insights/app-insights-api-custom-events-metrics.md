---
title: "사용자 지정 이벤트 및 메트릭용 Application Insights API | Microsoft Docs"
description: "장치 또는 데스크톱 앱, 웹 페이지, 서비스에 코드를 몇 줄 삽입하여 사용 및 진단 문제를 추적할 수 있습니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 64632e58330b8212be24b98f861a3a4f358e72df
ms.lasthandoff: 04/12/2017


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>사용자 지정 이벤트 및 메트릭용 Application Insights API


응용 프로그램에 몇 줄의 코드를 삽입하여 사용자가 해당 응용 프로그램으로 어떤 작업을 하는지 살펴보거나 진단 문제를 지원할 수 있습니다. 장치 및 데스크톱 앱, 웹 클라이언트, 웹 서버에서 원격 분석을 보낼 수 있습니다. [Azure Application Insights](app-insights-overview.md) 코어 원격 분석 API를 사용하여 사용자 지정 이벤트 및 메트릭 그리고 고유한 버전의 표준 원격 분석을 보냅니다. 이 API는 표준 Application Insights 데이터 수집기에서 사용하는 동일한 API입니다.

## <a name="api-summary"></a>API 요약
API는 사소한 차이를 제외하고 모든 플랫폼에서 동일합니다.

| 메서드 | 용도 |
| --- | --- |
| [`TrackPageView`](#page-views) |페이지, 화면, 블레이드 또는 양식. |
| [`TrackEvent`](#trackevent) |사용자 작업 및 기타 이벤트. 사용자 동작을 추적하거나 성능을 모니터링하는 데 사용됩니다. |
| [`TrackMetric`](#send-metrics) |특정 이벤트와 관련이 없는 큐 길이와 같은 성능 측정. |
| [`TrackException`](#trackexception) |진단 예외를 기록합니다. 다른 이벤트와 관련하여 발생 위치를 추적하고 스택 추적을 검사합니다. |
| [`TrackRequest`](#trackrequest) |성능 분석에 대한 서버 요청 빈도 및 기간을 기록합니다. |
| [`TrackTrace`](#tracktrace) |진단 로그 메시지. 타사 로그도 캡처할 수 있습니다. |
| [`TrackDependency`](#trackdependency) |기간 및 빈도 앱이 종속된 외부 구성 요소에 대한 호출을 기록합니다. |

이러한 대부분의 원격 분석 호출에 [속성 및 메트릭을 연결](#properties) 할 수 있습니다.

## <a name="prep"></a>시작하기 전에
다음 작업을 아직 수행하지 않은 경우

* 프로젝트에 Application Insights SDK 추가:

  * [ASP.NET 프로젝트](app-insights-asp-net.md)
  * [Java 프로젝트](app-insights-java-get-started.md)
  * [각 웹 페이지의 JavaScript](app-insights-javascript.md) 
* 장치 또는 웹 서버 코드에 다음을 포함합니다.

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="constructing-a-telemetryclient-instance"></a>TelemetryClient 인스턴스 생성
TelemetryClient의 인스턴스를 생성합니다(웹 페이지의 JavaScript는 제외).

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient는 스레드로부터 안전합니다.

앱의 각 모듈에 대해 TelemetryClient 인스턴스를 사용하는 것이 좋습니다. 예를 들어 웹 서비스에 들어오는 HTTP 요청을 보고하는 TelemetryClient 인스턴스 하나가 있고 미들웨어 클래스에 비즈니스 논리 이벤트를 보고하는 다른 하나가 있을 수 있습니다. `TelemetryClient.Context.User.Id`와 같은 속성을 설정하여 사용자 및 세션을 추적하거나 `TelemetryClient.Context.Device.Id`를 설정하여 컴퓨터를 식별할 수 있습니다. 이 정보는 인스턴스에서 보내는 모든 이벤트에 연결됩니다.

## <a name="trackevent"></a>TrackEvent
Application Insights에서 *사용자 지정 이벤트*는 [메트릭 탐색기](app-insights-metrics-explorer.md)에 집계된 개수로 표시하고 [진단 검색](app-insights-diagnostic-search.md)에 개별 항목으로 표시할 수 있는 데이터 요소입니다. MVC 또는 다른 프레임워크 "이벤트"와 관련이 없습니다.

코드에 TrackEvent 호출을 삽입하여 사용자가 특정 기능을 얼마나 자주 선택하는지, 특정 목표를 얼마나 자주 달성하는지 또는 특정 유형의 실수를 얼마나 자주 하는지 계산할 수 있습니다.

예를 들어 게임 앱은 사용자가 이길 때마다 이벤트를 보냅니다.

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Azure 포털에서 이벤트 보기
이벤트의 수를 보려면 [메트릭 탐색기](app-insights-metrics-explorer.md) 블레이드를 열고 새 차트를 추가한 다음 **이벤트**를 선택합니다.  

![사용자 지정 이벤트 수 보기](./media/app-insights-api-custom-events-metrics/01-custom.png)

여러 이벤트의 수를 비교하려면 차트 유형을 **그리드**로 설정하고 이벤트 이름으로 그룹화합니다.

![차트 종류 및 그룹화 설정](./media/app-insights-api-custom-events-metrics/07-grid.png)

그리드에서 이벤트 이름을 클릭하여 해당 이벤트의 개별 항목을 살펴봅니다. 원하는 항목을 클릭하여 자세히 살펴볼 수 있습니다.

![이벤트를 드릴스루합니다.](./media/app-insights-api-custom-events-metrics/03-instances.png)

검색 또는 메트릭 탐색기에서 특정 이벤트를 자세히 살펴보려면 관심 있는 이벤트 이름으로 블레이드 필터를 설정합니다.

![필터를 열고 이벤트 이름을 확장한 다음 하나 이상의 값을 선택합니다.](./media/app-insights-api-custom-events-metrics/06-filter.png)


## <a name="send-metrics"></a>메트릭 보내기

Application Insights에서는 특정 이벤트에 연결되지 않은 메트릭을 차트로 작성할 수 있습니다. 예를 들어 정기적으로 큐 길이를 모니터링할 수 있습니다. 메트릭을 사용할 경우 개별 측정값보다 변형 및 추세에 좀 더 관심을 갖게 되며 통계 차트가 유용합니다.

메트릭을 전송하는 방법에는 다음 두 가지가 있습니다.

* **MetricManager**는 대역폭을 줄이면서 메트릭을 전송하는 편리한 방법으로 권장됩니다. 이 방법은 앱의 메트릭을 집계한 후 집게한 통계를 1분 간격으로 포털로 전송합니다. MetricManager는 ASP.NET용 Application Insights SDK 버전 2.4에서 사용할 수 있습니다.
* **TrackMetric**은 포털에 메트릭 통계를 전송합니다. 단일 메트릭 값을 전송하거나 자체 집계를 수행한 후 TrackMetric을 사용하여 통계를 전송할 수 있습니다.

### <a name="metricmanager"></a>MetricManager

(ASP.NET용 Application Insights v2.4.0+)

MetricManager의 인스턴스를 만들고 메트릭에 대한 팩터리로 사용합니다.

*C#*
```C#
    // Initially:
    var manager = new Microsoft.ApplicationInsights.Extensibility.MetricManager(telemetryClient);

    // For each metric that you want to use:
    var metric1 = mgr.CreateMetric("m1", dimensions);

    // Each time you want to record a measurement:
    metric1.Track(value);

```

`dimensions`는 선택적 문자열 사전입니다. 속성 값별로 구분할 수 있도록 [속성](#properties)을 메트릭에 연결하려는 경우에 사용합니다. 

### <a name="trackmetric"></a>TrackMetric

TrackMetric은 집계된 메트릭을 전송하기 위한 기본 메서드입니다. 

단일 메트릭 값을 전송하려면

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

그러나 앱에서 전송하기 전에 메트릭을 집계해서 대역폭을 줄이는 것이 좋습니다.
최신 버전의 ASP.NET용 SDK를 사용하는 경우 [`MetricManager`](#metricmanager)를 사용하여 이 작업을 수행할 수 있습니다. 다음은 집계 코드 예제입니다.

*C#*

```C#
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    class MetricAggregator
    {
        private List<double> measurements = new List<double>();
        private string name;
        private TelemetryClient telemetryClient;
        private BackgroundWorker thread;
        private Boolean stop = false;
        public void TrackMetric (double value)
        {
            lock (this)
            {
                measurements.Add(value);
            }
        }
        public MetricTelemetry Aggregate()
        {
            lock (this)
            {
                var sample = new MetricTelemetry();
                sample.Name = "metric name";
                sample.Count = measurements.Count;
                sample.Max = measurements.Max();
                sample.Min = measurements.Min();
                sample.Sum = measurements.Sum();
                var mean = sample.Sum / measurements.Count;
                sample.StandardDeviation = Math.Sqrt(measurements.Sum(v => { var diff = v - mean; return diff * diff; }) / measurements.Count);
                sample.Timestamp = DateTime.Now;
                measurements.Clear();
                return sample;
            }
        }
        public MetricAggregator(string Name)
        {
            name = Name;
            thread = new BackgroundWorker();
            thread.DoWork += async (o, e) => {
                while (!stop)
                {
                    await Task.Delay(60000);
                    telemetryClient.TrackMetric(this.Aggregate());
                }
            };
            thread.RunWorkerAsync();
        }
    }
```
### <a name="custom-metrics-in-metrics-explorer"></a>메트릭 탐색기의 사용자 지정 메트릭

결과를 보려면 메트릭 탐색기를 열고 새 차트를 추가합니다. 차트를 편집하여 메트릭을 표시합니다.

> [!NOTE]
> 사용자 지정 메트릭이 사용 가능한 메트릭 목록에 표시되는 데는 몇 분 정도 걸릴 수 있습니다.
>

![새 차트를 추가하거나 차트를 선택하고, 사용자 지정 아래에서 메트릭을 선택합니다.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>분석의 사용자 지정 메트릭

customMetrics 테이블에서 원격 분석을 사용할 수 있습니다. 각 행은 앱의 trackMetric() 호출을 나타냅니다. 따라서 MetricManager 또는 자체 집계 코드를 사용한 경우 각 행은 단일 측정값을 나타내지 않습니다. 

* `valueSum` - 측정값의 합계입니다. 평균 값을 가져오려면 `valueCount`로 나눕니다.
* `valueCount` - 이 trackMetric 호출로 집계된 측정값의 수입니다.



## <a name="page-views"></a>페이지 보기
장치 또는 웹 페이지 앱에서 각 화면 또는 페이지가 로드되면 기본적으로 페이지 보기 원격 분석이 전송됩니다. 하지만 추가 시간에 또는 다른 시간에 페이지 보기를 추적하도록 변경할 수 있습니다. 예를 들어 탭 또는 블레이드를 표시하는 앱에서 사용자가 새 블레이드를 열 때마다 "페이지"를 추적할 수 있습니다.

![개요 블레이드의 사용 현황 렌즈](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

사용자 및 세션 데이터는 페이지 보기와 함께 속성으로 전송됩니다. 따라서 페이지 보기 원격 분석이 있으면 사용자 및 세션 차트가 실시간으로 표시됩니다.

### <a name="custom-page-views"></a>사용자 지정 페이지 보기
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


여러 다른 HTML 페이지 내에 여러 탭이 있으면 URL도 지정할 수 있습니다.

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>페이지 보기 시간
기본적으로 시간은 브라우저가 요청을 보낼 때부터 브라우저의 페이지 로드 이벤트를 호출할 때까지 측정되는 **페이지 보기 로드 시간**으로 보고됩니다.

대신, 다음을 수행할 수 있습니다.

* [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) 호출에서 명시적 기간을 설정합니다. `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* 페이지 보기 시간 호출 `startTrackPage` 및 `stopTrackPage`를 사용합니다.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

첫 번째 매개 변수로 사용하는 이름은 시작 및 중지 호출을 연결합니다. 기본적으로 현재 페이지 이름이 지정됩니다.

메트릭 탐색기에 표시된 결과 페이지 로드 기간은 시작 및 중지 호출 사이의 간격에서 파생됩니다. 실제로 걸리는 시간 간격에 달려 있습니다.

## <a name="trackrequest"></a>TrackRequest
서버 SDK는 TrackRequest를 사용하여 HTTP 요청을 기록합니다.

실행 중인 웹 서비스 모듈이 없는 상황에서 요청을 시뮬레이션하고 싶다면 사용자가 직접 호출할 수도 있습니다.

그러나 요청 원격 분석을 전송하는 권장 방법은 요청이 <a href="#operation-context">작업 컨텍스트</a>로 작동하는 경우입니다.

## <a name="operation-context"></a>작업 컨텍스트
원격 분석 항목을 일반 작업 ID에 연결하여 함께 연결할 수 있습니다. 표준 요청 추적 모듈은 예외 및 HTTP 요청이 처리되는 동안 전송되는 다른 이벤트에 대해 이를 수행합니다. [검색](app-insights-diagnostic-search.md) 및 [분석](app-insights-analytics.md)에서 ID를 사용하여 요청과 관련된 모든 이벤트를 쉽게 찾을 수 있습니다.

ID를 설정하는 가장 쉬운 방법은 이 패턴을 사용하여 작업 컨텍스트를 설정하는 것입니다.

*C#*

```C#

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item--for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.
```

작업 컨텍스트 설정과 함께 `StartOperation`은 지정하는 유형의 원격 분석 항목을 만듭니다. 작업을 삭제할 때 또는 명시적으로 `StopOperation`을 호출하는 경우 원격 분석 항목을 보냅니다. 원격 분석 형식으로 `RequestTelemetry`를 사용하는 경우 해당 기간은 시작 및 중지 사이의 시간 제한 간격으로 설정됩니다.

작업 컨텍스트는 중첩할 수 없습니다. 작업 컨텍스트가 이미 있는 경우 해당 ID가 `StartOperation`을 사용하여 만든 항목을 비롯한 모든 포함된 항목에 연결됩니다.

검색에서 작업 컨텍스트는 **관련 항목** 목록을 만드는 데 사용됩니다.

![관련 항목](./media/app-insights-api-custom-events-metrics/21.png)

## <a name="trackexception"></a>TrackException
Application Insights로 예외를 보냅니다.

* 문제의 빈도 표시로 [계산](app-insights-metrics-explorer.md)하려면
* [개별 항목을 검사](app-insights-diagnostic-search.md)하려면

보고서는 스택 추적을 포함합니다.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK에서 대부분의 예외를 자동으로 catch하므로 항상 TrackException을 명시적으로 호출할 필요는 없습니다.

* ASP.NET: [예외를 catch하는 코드 작성](app-insights-asp-net-exceptions.md).
* J2EE: [예외가 자동으로 catch됨](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: 예외가 자동으로 catch됨. 자동 수집을 사용하지 않도록 설정하려면 웹 페이지에 삽입하는 코드 조각에 다음 한 줄을 추가합니다.

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

## <a name="tracktrace"></a>TrackTrace
이 TrackTrace를 사용하여 Application Insights에 '이동 경로 트레일'을 전송하면 문제 진단에 도움이 됩니다. 진단 데이터의 청크를 보내고 [진단 검색](app-insights-diagnostic-search.md)에서 검사할 수 있습니다.

[로그 어댑터](app-insights-asp-net-trace-logs.md)는 이 API를 사용하여 포털에 타사 로그를 보냅니다.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


메시지 내용을 검색할 수 있지만 속성 값과는 달리 필터링할 수는 없습니다.

`message`의 크기 제한이 속성의 크기 제한보다 훨씬 높습니다.
TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.  

또한 메시지에 심각도 수준을 추가할 수 있습니다. 또 다른 원격 분석처럼, 다른 추적 집합에 대해 필터링 또는 검색하는 데 도움이 되는 속성 값을 추가할 수 있습니다. 예:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

[검색](app-insights-diagnostic-search.md)에서 특정 데이터베이스와 관련된 특정 심각도 수준의 모든 메시지를 쉽게 필터링할 수 있습니다.

## <a name="trackdependency"></a>TrackDependency
TrackDependency 호출을 사용하여 응답 시간과 외부 코드 부분에 대한 호출의 성공률을 추적합니다. 포털에서 종속성 차트에 결과가 나타납니다.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

서버 SDK는 특정 종속성 호출(예: 데이터베이스 및 REST API)을 자동으로 검색하고 추적하는 [종속성 모듈](app-insights-asp-net-dependencies.md)을 포함합니다. 모듈 작업을 만들기 위해 서버에 에이전트를 설치해야 합니다. 자동화된 추적에서 포착하지 않는 호출을 추적하려는 경우 또는 에이전트를 설치하지 않으려는 경우, 이 호출을 사용합니다.

표준 종속성 추적 모듈을 해제하려면 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)를 편집하고 `DependencyCollector.DependencyTrackingTelemetryModule`에 대한 참조를 삭제합니다.

## <a name="flushing-data"></a>데이터 플러시
일반적으로 SDK는 사용자에 미치는 영향을 최소화하기 위해 선택한 시간에 데이터를 보냅니다. 그러나 버퍼를 플러시하려는 경우가 있습니다. 종료되는 응용 프로그램에서 SDK를 사용하는 경우를 예로 들 수 있습니다.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

함수는 [서버 원격 분석 채널](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)에 대해 비동기입니다.

## <a name="authenticated-users"></a>인증된 사용자
웹앱에서 사용자는 기본적으로 쿠키로 식별됩니다. 사용자가 다른 컴퓨터 또는 브라우저에서 앱에 액세스하거나 쿠키를 삭제하는 경우 두 번 이상 계산될 수 있습니다.

사용자가 앱에 로그인하면 브라우저 코드에서 인증된 사용자 ID를 설정하여 보다 정확한 개수를 얻을 수 있습니다.

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

ASP.NET 웹 MVC 응용 프로그램에서의 예:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

사용자의 실제 로그인 이름을 사용할 필요는 없습니다. 해당 사용자에게 고유한 ID이기만 하면 됩니다. 공백이나 `,;=|` 문자를 포함할 수 없습니다.

사용자 ID도 세션 쿠키에 설정되고 서버로 전송됩니다. 서버 SDK가 설치된 경우 인증된 사용자 ID가 클라이언트 및 서버 원격 분석 둘 다에 대한 컨텍스트 속성의 일부로 전송됩니다. 필터링하고 검색할 수 있습니다.

앱이 사용자를 계정으로 그룹화하는 경우 계정 식별자를 전달할 수도 있습니다(동일한 문자 제한이 적용됨).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

[메트릭 탐색기](app-insights-metrics-explorer.md)에서 **사용자, 인증** 및 **사용자 계정**을 계산하는 차트를 만들 수 있습니다.

특정 사용자 이름과 계정으로 클라이언트 데이터 지점을 [검색](app-insights-diagnostic-search.md)할 수도 있습니다.

## <a name="properties"></a>속성을 사용하여 데이터를 필터링, 검색 및 세분화
이벤트에(그리고 메트릭, 페이지 보기, 예외 및 기타 원격 분석 데이터에) 속성 및 측정을 연결할 수 있습니다.

*속성*은 사용 현황 보고서에서 원격 분석을 필터링하는 데 사용할 수 있는 문자열 값입니다. 예를 들어 앱이 여러 게임을 제공하는 경우 각 이벤트에 게임 이름을 연결하여 인기가 더 많은 게임을 확인할 수 있습니다.

문자열 길이는 8192로 제한됩니다. 많은 양의 데이터를 보내려면 메시지 매개 변수 [TrackTrace](#track-trace)를 사용하세요.

*메트릭* 은 그래픽으로 표시할 수 있는 숫자 값입니다. 예를 들어 게이머의 획득 점수가 점진적으로 증가하는지 확인할 수 있습니다. 여러 게임에 대한 별도의 그래프 또는 누적 그래프를 볼 수 있도록 이벤트와 함께 전송된 속성을 사용하여 그래프를 분할할 수 있습니다.

메트릭 값을 올바르게 표시하려면 0보다 크거나 같아야 합니다.

[속성 수, 속성 값 및 메트릭에 사용 가능한 제한](#limits) 이 몇 가지 있습니다.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> 속성에 개인 식별이 가능한 정보를 기록하지 않도록 주의해야 합니다.
>
>

*메트릭을 사용한 경우* 메트릭 탐색기를 열고 **사용자 지정** 그룹에서 메트릭을 선택합니다.

![메트릭 탐색기를 열고, 차트를 선택하고, 메트릭을 선택합니다.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> 메트릭이 표시되지 않거나 **사용자 지정** 머리글이 없는 경우 선택 블레이드를 닫고 나중에 다시 시도합니다. 때로는 메트릭이 파이프라인을 통해 집계되는 데 한 시간 정도 걸릴 수 있습니다.

*속성 및 메트릭을 사용한 경우*속성에 따라 메트릭을 분할합니다.

![그룹화를 설정한 다음 그룹화 기준 아래에서 속성을 선택합니다.](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*진단 검색*에서 개별 이벤트 항목의 속성 및 메트릭을 볼 수 있습니다.

![인스턴스를 선택한 다음 '...'를 선택합니다.](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

**검색** 필드를 사용하여 특정 속성 값이 포함된 이벤트 항목을 볼 수 있습니다.

![검색에 용어를 입력합니다.](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[검색 식에 대해 자세히 알아보세요](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>속성 및 메트릭을 설정하는 또 다른 방법
이벤트 매개 변수를 별도의 개체에 수집하는 방법이 더 편하다면 이 방법을 사용해도 됩니다.

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Track*()을 여러 번 호출하기 위해 같은 원격 분석 항목 인스턴스(이 예에서 `event`)를 다시 사용하지 않습니다. 그러면 원격 분석을 잘못된 구성과 함께 보낼 수 있습니다.
>
>

## <a name="timed"></a> 타이밍 이벤트
작업을 수행하는 데 걸리는 시간을 차트로 표시하고 싶은 경우가 있습니다. 예를 들어 게임에서 사용자가 옵션을 선택하는 데 걸리는 시간을 알고 싶을 수 있습니다. 이를 위해 측정 매개 변수를 사용할 수 있습니다.

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>사용자 지정 원격 분석에 대한 기본 속성
작성하는 사용자 정의 이벤트의 일부에 대해 기본 속성 값을 설정하려는 경우 TelemetryClient 인스턴스에서 설정할 수 있습니다. 설정된 값은 해당 클라이언트에서 보낸 모든 원격 분석 항목에 연결됩니다.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");



개별 원격 분석 호출이 자신의 속성 사전에 있는 기본값을 재정의할 수 있습니다.

*JavaScript 웹 클라이언트의 경우*[JavaScript 원격 분석 이니셜라이저를 사용합니다](#js-initializer).

표준 컬렉션 모듈의 데이터를 포함하여 *모든 원격 분석에 속성을 추가하려면* [`ITelemetryInitializer`를 구현합니다](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>원격 분석 샘플링, 필터링 및 처리
SDK에서 전송하기 전에 원격 분석을 처리하는 코드를 작성할 수 있습니다. 처리는 HTTP 요청 컬렉션 및 종속성 컬렉션과 같은 표준 원격 분석 모듈에서 전송된 데이터를 포함합니다.

`ITelemetryInitializer`를 구현하여 원격 분석에 [속성을 추가](app-insights-api-filtering-sampling.md#add-properties)합니다. 예를 들어 다른 속성에서 계산된 버전 번호 또는 값을 추가할 수 있습니다.

`ITelemetryProcesor`를 구현하여 원격 분석이 SDK에서 전송되기 전에 [필터링](app-insights-api-filtering-sampling.md#filtering)을 통해 원격 분석을 수정 또는 삭제할 수 있습니다. 전송 또는 삭제될 대상을 제어하지만 메트릭에 미치는 영향을 고려해야 합니다. 항목 삭제 방법에 따라 관련된 항목 사이를 이동하는 기능이 손실될 수 있습니다.

[샘플링](app-insights-api-filtering-sampling.md)은 앱에서 포털로 전송되는 데이터의 양을 줄이는 패키지 솔루션입니다. 표시된 메트릭에 영향을 주지 않습니다. 예외, 요청 및 페이지 뷰와 같은 관련된 항목 간을 이동하여 문제를 진단하는 기능에 영향을 주지 않습니다.

[자세히 알아보세요](app-insights-api-filtering-sampling.md)을 확인하세요.

## <a name="disabling-telemetry"></a>원격 분석 사용 안 함
원격 분석의 컬렉션 및 전송을 *동적으로 중지 및 시작하려면* :

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*선택한 표준 수집기(예: 성능 카운터, HTTP 요청 또는 종속성)를 사용하지 않도록 설정*하려면 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 관련 줄을 삭제하거나 주석으로 처리합니다. 사용자 고유의 TrackRequest 데이터를 전송하려는 경우를 예로 들 수 있습니다.

## <a name="debug"></a>개발자 모드
디버깅하는 동안 결과를 즉시 볼 수 있도록 파이프라인을 통해 원격 분석을 신속하게 처리할 때 유용합니다. 또한 원격 분석과 관련된 모든 문제를 추적하는 데 도움이 되는 추가 메시지가 제공됩니다. 앱이 느려질 수 있으므로 프로덕션 환경에서는 끄는 것이 좋습니다.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> 선택한 사용자 지정 원격 분석에 대해 계측 키 설정
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> 동적 계측 키
개발, 테스트 및 프로덕션 환경에서 원격 분석이 섞이지 않게 방지하려면 [별도의 Application Insights 리소스를 만들고](app-insights-create-new-resource.md) 환경에 따라 키를 변경하세요.

구성 파일에서 계측 키를 가져오는 대신 코드에서 설정할 수 있습니다. ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



웹 페이지에서 문자 그대로 스크립트에 코딩하는 대신 웹 서버의 상태를 이용하여 설정할 수 있습니다. 예를 들어 ASP.NET 앱에서 생성된 웹 페이지에서 다음과 같이 설정합니다.

*Razor에서 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient에는 컨텍스트 속성이 있고, 이 속성은 모든 원격 분석 데이터와 함께 전송되는 값을 포함하고 있습니다. 일반적으로 표준 원격 분석 모듈에 의해 설정되지만 사용자가 직접 설정할 수도 있습니다. 예:

    telemetry.Context.Operation.Name = "MyOperationName";

이러한 값을 직접 설정하는 경우 사용자의 값과 표준 값이 혼동되지 않도록 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 관련 줄을 제거해야 합니다.

* **Component**: 앱 및 앱 버전입니다.
* **Device**: 앱이 실행되는 장치에 대한 데이터입니다. (웹앱에서 원격 분석이 전송되는 서버 또는 클라이언트 장치입니다.)
* **InstrumentationKey**: Azure에서 원격 분석이 표시되는 Application Insights 리소스입니다. 일반적으로 ApplicationInsights.config에서 선택합니다.
* **Location**: 장치의 지리적 위치입니다.
* **Operation**: 웹앱에서 현재 HTTP 요청입니다. 다른 유형의 앱에서는 이 값을 설정하여 이벤트를 그룹화할 수 있습니다.
  * **Id**: 진단 검색의 이벤트를 검사할 때 "항목 관련"을 찾을 수 있도록 여러 이벤트를 상호 연결하는 생성된 값입니다.
  * **Name**: 식별자이며, 일반적으로 HTTP 요청의 URL입니다.
  * **SyntheticSource**: null이거나 비어 있지 않다면 요청의 원본이 로봇 또는 웹 테스트로 확인되었음을 나타내는 문자열입니다. 기본적으로 메트릭 탐색기의 계산에서 제외됩니다.
* **Properties**: 모든 원격 분석 데이터와 함께 전송되는 속성입니다. 개별 Track* 호출에서 재정의될 수 있습니다.
* **Session**: 사용자의 세션입니다. ID는 생성된 값으로 설정되며, 사용자가 잠시 동안 비활성 상태이면 값이 변경됩니다.
* **User**: 사용자 정보입니다.

## <a name="limits"></a>제한
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

데이터 속도 제한에 도달하지 않도록 하려면 [샘플링](app-insights-sampling.md)을 사용합니다.

데이터 유지 기간을 결정하려면 [데이터 보존 및 개인 정보](app-insights-data-retention-privacy.md)를 참조하세요.

## <a name="reference-docs"></a>참조 문서
* [ASP.NET 참조](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 참조](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK 코드
* [ASP.NET 핵심 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Windows Server 패키지](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [모든 플랫폼](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>질문
* *Track_() 호출에서 발생할 수 있는 예외는 무엇인가요?*

    없음. try-catch 절에 래핑할 필요가 없습니다. SDK에 문제가 발생하는 경우 디버그 콘솔 출력에 메시지를 작성하고 메시지가 완료되는 경우 진단 검색에 표시됩니다.
* *포털에서 데이터를 가져오는 REST API가 있나요?*

    예, [데이터 액세스 API](https://dev.applicationinsights.io/)가 있습니다. 데이터를 추출하는 다른 방법에는 [Analytics에서 Power BI로 내보내기](app-insights-export-power-bi.md) 및 [연속 내보내기](app-insights-export-telemetry.md)가 있습니다.

## <a name="next"></a>다음 단계
* [검색 이벤트 및 로그](app-insights-diagnostic-search.md)

* [문제 해결](app-insights-troubleshoot-faq.md)




