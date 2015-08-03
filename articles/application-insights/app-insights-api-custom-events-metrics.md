<properties 
	pageTitle="사용자 지정 이벤트 및 메트릭용 Application Insights API" 
	description="장치 또는 데스크톱 앱, 웹 페이지, 서비스에 코드를 몇 줄 삽입하여 사용 및 진단 문제를 추적할 수 있습니다." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2015" 
	ms.author="awills"/>

# 사용자 지정 이벤트 및 메트릭용 Application Insights API 

*Application Insights는 미리 보기 상태입니다.*

응용 프로그램에 몇 줄의 코드를 삽입하여 사용자가 해당 응용 프로그램으로 어떤 작업을 하는지 살펴보거나 진단 문제를 지원할 수 있습니다. 장치 및 데스크톱 앱, 웹 클라이언트, 웹 서버에서 원격 분석을 보낼 수 있습니다.

Application Insights 데이터 수집기는 이 API를 사용하여 페이지 보기 및 예외 보고서 같은 표준 원격 분석을 보냅니다. 하지만 이 API를 사용하여 사용자 지정 원격 분석을 보낼 수도 있습니다.

## API 요약

API는 사소한 차이를 제외하고 모든 플랫폼에서 동일합니다.

메서드 | 용도
---|---
[`TrackPageView`](#page-views)로 바꿉니다. | 페이지, 화면, 블레이드 또는 양식
[`TrackEvent`](#track-event)로 바꿉니다. | 사용자 작업 및 기타 이벤트. 사용자 동작을 추적하거나 성능을 모니터링하는 데 사용됩니다.
[`TrackMetric`](#track-metric)로 바꿉니다. | 특정 이벤트와 관련이 없는 큐 길이와 같은 성능 측정
[`TrackException`](#track-exception)로 바꿉니다.|진단 예외를 기록합니다. 다른 이벤트와 관련하여 발생 위치를 추적하고 스택 추적을 검사합니다.
[`TrackRequest`](#track-request)로 바꿉니다.| 성능 분석에 대한 서버 요청 빈도 및 기간을 기록합니다.
[`TrackTrace`](#track-trace)로 바꿉니다.|진단 로그 메시지. 타사 로그도 캡처할 수 있습니다.
[`TrackDependency`](#track-dependency)로 바꿉니다.|기간 및 빈도 앱이 종속된 외부 구성 요소에 대한 호출을 기록합니다.

이러한 대부분의 원격 분석 호출에 [속성 및 메트릭을 연결](#properties)할 수 있습니다.


## <a name="prep"></a>시작하기 전에

다음 작업을 아직 수행하지 않은 경우

* 프로젝트에 Application Insights SDK 추가:
 * [ASP.NET 프로젝트][greenbrown]
 * [Windows 프로젝트][windows]
 * [Java 프로젝트][java] 
 * [각 웹 페이지의 JavaScript][client]   

* 장치 또는 웹 서버 코드에 다음을 포함합니다.

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## TelemetryClient 생성

TelemetryClient의 인스턴스를 생성합니다(웹 페이지의 JavaScript는 제외).

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

웹 앱의 각 요청에 대해 또는 다른 앱의 각 세션에 대해 `TelemetryClient` 인스턴스를 하나만 사용하는 것이 좋습니다. `TelemetryClient.Context.User.Id` 같은 속성을 설정하여 사용자 및 세션을 추적할 수 있습니다. 이 정보는 인스턴스에서 보낸 모든 이벤트에 연결됩니다.

TelemetryClient는 스레드로부터 안전합니다.



## 이벤트 추적

[메트릭 탐색기][metrics]에 이벤트 총 집계를 표시할 수도 있고 [진단 검색][diagnostic]에 개별 항목을 표시할 수도 있습니다.

코드에 이벤트를 삽입하여 해당 이벤트에서 특정 기능을 얼마나 자주 사용하는지, 특정 목표를 얼마나 자주 달성하는지, 특정 항목을 얼마나 자주 선택하는지 집계할 수 있습니다.

예를 들어 게임 앱은 사용자가 이길 때마다 이벤트를 보냅니다.

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


개요 블레이드에서 사용자 지정 이벤트 타일을 클릭합니다.

![Portal.azure.com에서 응용 프로그램 리소스를 찾습니다.](./media/app-insights-api-custom-events-metrics/01-custom.png)

클릭하여 개요 차트와 전체 목록을 살펴봅니다.

차트를 선택하고 이벤트 이름별로 분할하여 가장 중요한 이벤트의 상대적인 기여도를 살펴봅니다.

![차트를 선택하고 그룹화를 선택합니다.](./media/app-insights-api-custom-events-metrics/02-segment.png)

차트 아래에 있는 목록에서 이벤트 이름을 선택합니다. 클릭하여 개별 이벤트 항목을 살펴봅니다.

![이벤트를 드릴스루합니다.](./media/app-insights-api-custom-events-metrics/03-instances.png)

원하는 항목을 클릭하여 자세히 살펴볼 수 있습니다.

## <a name="properties"></a>속성을 사용하여 데이터를 필터링, 검색 및 분할

이벤트에(그리고 메트릭, 페이지 보기 및 기타 원격 분석 데이터에) 속성 및 측정을 연결할 수 있습니다.

**속성**은 사용 현황 보고서에서 원격 분석을 필터링하는 데 사용할 수 잇는 문자열 값입니다 예를 들어 앱이 여러 게임을 제공하는 경우 각 이벤트에 게임 이름을 연결하여 인기가 더 많은 게임을 확인할 수 있습니다.

문자열 길이는 약 1k로 제한됩니다. 많은 양의 데이터를 보내려면 메시지 매개 변수 [TrackTrace](#track-trace)를 사용하세요.

**메트릭**은 그래픽으로 표시할 수 있는 숫자 값입니다. 예를 들어 게이머의 획득 점수가 점진적으로 증가하는지 확인할 수 있습니다. 여러 게임에 대한 별도의 그래프 또는 누적 그래프를 볼 수 있도록 이벤트와 함께 전송된 속성을 사용하여 그래프를 분할할 수 있습니다.

메트릭 값이 0 이상이어야 올바르게 표시됩니다.


[속성 수, 속성 값 및 메트릭에 사용 가능한 제한](#limits)이 몇 가지 있습니다.


*JavaScript*

    appInsights.trackEvent // or trackPageView, trackMetric, ...
      ("WinGame",
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


*VB*

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
    
    telemetry.trackEvent("WinGame", properties, metrics2/7/2015 12:05:25 AM );


> [AZURE.NOTE]속성에 개인 식별이 가능한 정보를 기록하지 않도록 주의해야 합니다.

**메트릭을 사용한 경우** 메트릭 탐색기를 열고 사용자 지정 그룹에서 메트릭을 선택합니다.

![메트릭 탐색기를 열고, 차트를 선택하고, 메트릭을 선택합니다.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*메트릭이 나타나지 않으면 선택 블레이드를 닫고 잠시 기다린 후 새로 고침을 클릭합니다.*

**속성 및 메트릭을 사용한 경우** 속성에 따라 메트릭을 분할합니다.


![그룹화를 설정한 다음 그룹화 기준 아래에서 속성을 선택합니다.](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**진단 검색**에서 개별 이벤트 항목의 속성 및 메트릭을 볼 수 있습니다.


![인스턴스를 선택한 다음 '...'를 선택합니다.](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


검색 필드를 사용하여 특정 속성 값이 포함된 이벤트 항목을 볼 수 있습니다.


![검색에 용어를 입력합니다.](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[검색 식에 대해 자세히 알아보세요][diagnostic].

#### 속성 및 메트릭을 설정하는 또 다른 방법

이벤트 매개 변수를 별도의 개체에 수집하는 방법이 더 편하다면 이 방법을 사용해도 됩니다.

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);



#### <a name="timed"></a> 타이밍 이벤트

어떤 작업을 수행하는 데 걸리는 시간을 차트로 표시하고 싶은 경우가 있습니다. 예를 들어 게임에서 사용자가 옵션을 선택하는 데 걸리는 시간을 알고 싶을 수 있습니다. 다음은 측정 매개 변수 사용 방법을 보여 주는 유용한 예입니다.


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



## 메트릭 추적

TrackMetric을 사용하여 특정 이벤트에 연결되지 않은 메트릭을 보낼 수 있습니다. 예를 들어 정기적으로 큐 길이를 모니터링할 수 있습니다.

메트릭은 메트릭 탐색기에 통계 차트로 표시됩니다. 하지만 이벤트와는 달리 진단 검색에서 개별 항목을 검색할 수 없습니다.

메트릭 값이 0 이상이어야 올바르게 표시됩니다.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

사실, 이 작업을 백그라운드 스레드에서 수행할 수도 있습니다.

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


결과를 보려면 메트릭 탐색기를 열고 새 차트를 추가합니다. 메트릭을 표시하도록 설정합니다.

![새 차트를 추가하거나 차트를 선택하고, 사용자 지정 아래에서 메트릭을 선택합니다.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

[메트릭 수에 제한을 적용](#limits)할 수 있습니다.

## 페이지 보기

장치 또는 웹 페이지 앱에서 각 화면 또는 페이지가 로드되면 기본적으로 페이지 보기 원격 분석이 전송됩니다. 하지만 추가 시간에 또는 다른 시간에 페이지 보기를 추적하도록 변경할 수 있습니다. 예를 들어 탭 또는 블레이드를 표시하는 앱에서 사용자가 새 블레이드를 열 때마다 "페이지"를 추적할 수 있습니다.

![개요 블레이드의 사용 현황 렌즈](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

사용자 및 세션 데이터는 페이지 보기와 함께 속성으로 전송됩니다. 따라서 페이지 보기 원격 분석이 있으면 사용자 및 세션 차트가 실시간으로 표시됩니다.

#### 사용자 지정 페이지 보기

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


여러 다른 HTML 페이지 내에 여러 탭이 있으면 URL도 지정할 수 있습니다.

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### 시간 제한 페이지 보기

trackPageView 대신 이 메서드 쌍을 호출하여 사용자가 페이지에 머문 시간을 분석할 수 있습니다.

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

호출 시작 및 중지에서 동일한 문자열을 첫 번째 매개 변수로 사용합니다.

[메트릭 탐색기][metrics]에서 페이지 기간 메트릭을 살펴봅니다.


## 요청 추적

서버 SDK에서 HTTP 요청을 기록하는 데 사용합니다.

실행 중인 웹 서비스 모듈이 없는 상황에서 요청을 시뮬레이션하고 싶다면 사용자가 직접 호출할 수도 있습니다.

*C#*

    // At start of processing this request:

    // Operation Id is attached to all telemetry and helps you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success

## 예외 추적

Application Insights로 예외를 보낸 후 [집계][metrics]하여 문제 발생 빈도를 확인하거나 [개별 항목을 검사][diagnostic]할 수 있습니다. 보고서는 스택 추적을 포함합니다.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

Windows 모바일 앱에서 SDK는 처리되지 않은 예외를 catch합니다. 따라서 모바일 앱에 로그인할 필요가 없습니다. ASP.NET에서 [예외를 자동으로 catch하는 코드를 작성][exceptions]할 수 있습니다.


## 흔적 추적 

이 코드를 사용하여 Application Insights에 '이동 경로 트레일'을 전송하면 문제 진단에 도움이 됩니다. 진단 데이터의 청크를 보내고 [진단 검색][diagnostic]에서 검사합니다.

 

[로그 어댑터][trace] 이 API를 사용하여 포털에 타사 로그를 보냅니다.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

`message`의 크기 제한이 속성의 크기 제한보다 훨씬 높습니다. 메시지 내용을 검색할 수 있지만 속성 값과는 달리 필터링할 수는 없습니다.

## 종속성을 추적합니다.

표준 종속성 추적 모듈은 API를 사용하여 데이터베이스 혹은 REST API 같은 외부 종속성에 대한 호출을 기록합니다. 모듈은 자동으로 일부 외부 종속성을 탐색하지만, 사용자가 일부 추가 구성 요소를 동일한 방식으로 취급하고 싶을지도 모릅니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

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

표준 종속성 추적 모듈을 해제 하려면 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)을 편집하고 참조를 삭제합니다. `DependencyCollector.DependencyTrackingTelemetryModule`

## <a name="defaults"></a>선택한 사용자 지정 원격 분석에 대한 기본값 설정

작성하는 사용자 정의 이벤트의 일부에 대해 기본 속성 값을 설정하려는 경우 TelemetryClient에서 설정할 수 있습니다. 설정된 값은 해당 클라이언트에서 보낸 모든 원격 분석 항목에 연결됩니다.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

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




## <a name="ikey"></a> 선택한 사용자 지정 원격 분석에 대해 계측 키를 설정합니다.

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


## <a name="default-properties"></a>컨텍스트 이니셜라이저 - 모든 원격 분석에 대한 기본 속성 설정

유니버설 아니셜라이저를 설정하여 새로운 모든 TelemetryClients는 사용자 컨텍스트를 자동으로 사용할 수 있습니다. 여기에는 웹 서버 요청 추적처럼 플랫폼별 원격 분석 모듈에서 전송하는 표준 원격 분석이 포함됩니다.

일반적으로 여러 앱 버전 또는 여러 앱 구성 요소에서 들어오는 원격 분석을 식별하는 데 사용됩니다. 포털에서 "응용 프로그램 버전" 속성을 사용하여 결과를 필터링 또는 그룹화할 수 있습니다.

**이니셜라이저 정의**


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

**이니셜라이저 로드**

ApplicationInsights.config에서:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*또는*, 코드에서 이니셜라이저를 인스턴스화할 수 있습니다.

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```

JavaScript 웹 클라이언트에서 현재 기본 속성을 설정하는 방법은 없습니다.

## 원격 분석 이니셜라이저

원격 분석 이니셜라이저를 사용하여 표준 원격 분석 모듈의 선택한 동작을 재정의할 수 있습니다.

예를 들어, 웹 패키지에 대한 Application Insights는 HTTP 요청에 대한 원격 분석을 수집합니다. 기본적으로, 모든 요청을 응답 코드 > = 400으로 실패한 것으로 플래그합니다. 하지만 400를 성공으로 처리하려는 경우 성공 속성을 설정하는 원격 분석 이니셜라이저를 제공할 수 있습니다.

원격 분석 이니셜라이저를 제공하는 경우 Track*() 메소드가 호출될 때마다 호출됩니다. 표준 원격 분석 모듈에 의해 호출되는 메서드가 포함됩니다. 규칙에 따라 이러한 모듈은 이니셜라이저에서 이미 설정된 모든 속성을 설정하지 않습니다.

**이니셜라이저 정의**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**이니셜라이저 로드**

ApplicationInsights.config에서:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*또는*, 코드에서 이니셜라이저를 인스턴스화할 수 있습니다(예: Global.aspx.cs).


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[이 샘플에 대해 자세히 알아봅니다.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

## <a name="dynamic-ikey"></a> 동적 계측 키

개발, 테스트 및 프로덕션 환경에서 원격 분석이 섞이지 않게 방지하려면 [별도의 Application Insights 리소스를 만들고][create] 환경에 따라 키를 변경하세요.

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
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...



## 데이터 플러시

일반적으로 SDK는 사용자에 미치는 영향을 최소화하기 위해 선택한 시간에 데이터를 보냅니다. 그러나 버퍼를 플러시하려는 경우가 있습니다. 종료되는 응용 프로그램에서 SDK를 사용하는 경우를 예로 들 수 있습니다.

*C#*

    telemetry.Flush();

함수는 방식입니다.



## 표준 원격 분석을 사용하지 않도록 설정

`ApplicationInsights.config`를 편집하여 [표준 원격 분석에서 선택한 부분을 사용하지 않도록 설정][config]할 수 있습니다. 사용자 고유의 TrackRequest 데이터를 전송하려는 경우를 예로 들 수 있습니다.

[자세히 알아봅니다][config].


## <a name="debug"></a>개발자 모드

디버깅하는 동안 결과를 즉시 볼 수 있도록 파이프라인을 통해 원격 분석을 신속하게 처리할 때 유용합니다. 또한 원격 분석과 관련된 모든 문제를 추적하는 데 도움이 되는 추가 메시지가 제공됩니다. 앱이 느려질 수 있으므로 프로덕션 환경에서는 끄는 것이 좋습니다.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True

## TelemetryContext

TelemetryClient에는 컨텍스트 속성이 있고, 이 속성은 모든 원격 분석 데이터와 함께 전송되는 다양한 값을 포함하고 있습니다. 일반적으로 표준 원격 분석 모듈에 의해 설정되지만 사용자가 직접 설정할 수도 있습니다.

이러한 값을 직접 설정하는 경우 사용자의 값과 표준 값이 혼동되지 않도록 [ApplicationInsights.config][config]에서 관련 줄을 제거해야 합니다.

* **Component** 앱 및 앱 버전을 식별합니다.
* **Device** 앱이 실행되는 장치에 대한 데이터입니다(웹 앱의 경우 원격 분석을 보내는 서버 또는 클라이언트).
* **InstrumentationKey** Azure에서 원격 분석이 표시되는 Application Insights 리소스를 식별합니다. 일반적으로 ApplicationInsights.config에서 선택합니다.
* **Location** 장치의 지리적 위치를 식별합니다.
* **Operation** 웹 앱에서 현재 HTTP 요청입니다. 다른 유형의 앱에서는 이 값을 설정하여 이벤트를 그룹화할 수 있습니다.
 * **Id**: 진단 검색의 이벤트를 검사할 때 "항목 관련"을 찾을 수 있도록 여러 이벤트를 상호 연결하는 생성된 값입니다.
 * **Name**: HTTP 요청의 URL입니다.
 * **SyntheticSource**: null이거나 비어 있지 않다면 이 문자열은 요청의 원본이 로봇 또는 웹 테스트로 확인되었음을 나타냅니다. 기본적으로 메트릭 탐색기의 계산에서 제외됩니다.
* **Properties** 모든 원격 분석 데이터와 함께 전송되는 속성입니다. 개별 Track* 호출에서 재정의할 수 있습니다.
* **Session** 사용자의 세션을 식별합니다. ID는 생성된 값으로 설정되며, 사용자가 잠시 동안 비활성 상태이면 값이 변경됩니다.
* **User** 사용자 수를 계산할 수 있습니다. 웹 앱의 경우 쿠키가 있으면 해당 쿠키에서 사용자 ID를 가져옵니다. 쿠키가 없으면 새 쿠키가 생성됩니다. 사용자가 앱에 로그인해야 하는 경우 사용자의 인증된 ID로 ID를 설정하면 사용자가 다른 컴퓨터에서 로그인하더라도 사용자 수를 정확하게 계산할 수 있습니다. 




## 제한

응용프로그램당 허용되는 메트릭 및 이벤트 수가 제한되어 있습니다.

1. 계측 키마다 초당(즉, 응용 프로그램별로) 최대 500개의 원격 분석 데이터 요소가 허용됩니다. 여기에는 SDK 모듈 및 사용자 지정 이벤트에서 보낸 표준 원격 분석과 코드에서 보낸 메트릭 및 기타 원격 분석이 모두 포함됩니다.
1.	응용 프로그램에는 최대 200개의 고유한 메트릭 이름과 200개의 고유한 속성 이름이 허용됩니다. 메트릭은 TrackMetric을 통해 전송된 데이터와 이벤트 같은 기타 데이터 유형의 측정을 포함합니다. 메트릭 및 속성 이름의 범위는 데이터 유형으로 한정되지 않고 계측 키마다 전역적입니다.
2.	속성은 필터링 및 그룹화에만 사용할 수 있으며 각 속성에 허용되는 고유한 값은 100개 미만입니다. 고유한 값이 100개를 초과할 경우 해당 속성을 검색 및 필터링에는 계속 사용할 수 있지만 필터에는 더 이상 사용할 수 없습니다.
3.	요청 이름 및 페이지 URL 같은 표준 속성은 일주일에 고유한 값 1000개로 제한됩니다. 고유한 값이 1000개를 초과할 경우 초과하는 값이 "기타 값"으로 표시됩니다. 원래 값을 전체 텍스트 검색 및 필터링에 계속 사용할 수 있습니다.

* *Q: 데이터가 얼마 동안 보존되나요?*

    [데이터 보존 및 개인 정보][data]를 참조하세요.


## 참조 문서

* [ASP.NET 참조](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 참조](http://dl.windowsazure.com/applicationinsights/javadoc/)

## 질문

* *Track * 호출에서 throw할 수 있는 예외는 무엇인가요?*
    
    없음 catch 절에 래핑할 필요가 없습니다.



* *REST API가 있나요?*

    예, 하지만 아직은 게시하지 않고 있습니다.

## <a name="next"></a>다음 단계


[검색 이벤트 및 로그][diagnostic]

[문제 해결][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO4-->