<properties 
	pageTitle="웹 앱에서 실패와 예외 감지 및 진단" 
	description="웹 앱에서 실패와 예외 감지 및 진단" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Application Insights를 사용하여 웹 앱의 실패 및 예외 진단

[Visual Studio Application Insights][start]는 응용 프로그램에서 오류를 감지하고 진단할 수 있는 강력한 도구입니다. 여기서 웹 응용 프로그램에 대해 집중적으로 살펴보겠습니다. (그러나 [다양한 다른 플랫폼][platforms]에도 Application Insights를 적용할 수도 있습니다.)

## Application Insights로 앱 설치

응용 프로그램 프로젝트에 SDK를 추가합니다. 앱이 출시되고 실행되면 SDK가 Application Insights 포털에 대한 앱의 성능을 원격 분석하여 전송합니다.

* [ASP.NET 앱에 Application Insights 추가][greenbrown]
* [Java 앱에 Application Insights 추가][java]

웹 페이지에 상당한 스크립트가 있으면 아마도 *[웹 페이지에 Application Insights를 추가][track]하려 할 것입니다.


## 진단 검색



SDK가 자동으로 전송하는 원격 분석을 보려면 진단 검색을 엽니다.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

하나의 응용 프로그램 유형과 다른 응용 프로그램은 세부 정보가 달라집니다. 자세한 내용을 보려면 개별 이벤트를 통해 클릭할 수 있습니다.

##<a name="events"></a>사용자 지정 이벤트

사용자 지정 이벤트는 [진단 검색][diagnostic] 및 [메트릭 탐색기][metrics]에 모두 표시됩니다. 이것들을 장치, 웹 페이지 및 서버 응용 프로그램에서 보낼 수 있습니다. 이것들은 진단 목적 및 [사용 패턴을 이해][track]하기 위해 사용될 수 있습니다.

사용자 지정 이벤트는 숫자 측정값처럼 이름이 있고, 필터링 할 수 있는 속성을 전달할 수 있습니다.

클라이언트의 JavaScript

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

서버의 C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


서버의 VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### 앱을 실행하고 결과를 보십시오.

진단 검색 열기

사용자 지정 이벤트를 선택하고 특정 이벤트 이름을 선택하십시오.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


속성 값에 검색 단어를 입력하여 더 많은 데이터를 필터링하십시오.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

개별 이벤트의 자세한 속성을 보려면 확인하십시오.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> 원격 분석 추적

추적 원격 분석은 진단 로그를 만들기 위해 삽입하는 코드입니다.

예를 들어 다음과 같이 호출을 삽입할 수 있습니다:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  로깅 프레임워크 어댑터 설치

Log4Net, NLog 또는 System.Diagnostics.Trace와 같은 로깅 프레임워크로 생성된 로그를 검색할 수 있습니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다. 
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
3. 온라인 > 모두를 선택하고, **시험판 포함**을 선택하고, "Microsoft.ApplicationInsights"를 검색합니다.

    ![적절한 어댑터의 시험판 버전 가져오기](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. 다음 패키지 중에서 적절한 패키지를 하나 선택합니다.
  + Microsoft.ApplicationInsights.TraceListener (to capture System.Diagnostics.Trace calls)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet 패키지는 필요한 어셈블리를 설치하고 web.config 또는 app.config를 수정합니다.

#### <a name="pepper"></a>진단 로그 호출 삽입

System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 원할 경우

    logger.Warn("Slow response - database01");

디버그 모드에서 앱을 실행하거나 배포합니다.

추적 필터를 선택하면 진단 검색에서 메시지를 보게 됩니다.

### <a name="exceptions"></a>예외

Application Insights에서 예외 보고서를 가져옴으로써 매우 강력한 환경을 제공하며, 특히 실패한 요청과 예외 사이를 탐색하고 예외 스택을 읽을 수 있습니다.

예외 원격 분석을 보내도록 코드를 작성할 수 있습니다:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

속성 및 측정 매개 변수는 선택적이지만 추가 정보를 필터링 및 추가하는 데 유용합니다. 예를 들어 여러 게임을 실행할 수 있는 앱이 있는 경우 특정 게임과 관련된 모든 예외 보고서를 찾을 수 있습니다. 각 사전에 원하는 만큼 항목을 추가할 수 있습니다.

#### 예외 사항 보기

개요 블레이드에 보고되는 예외의 요약을 참고하고 클릭하여 더 자세한 내용을 확인하십시오. 예:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

특정 항목을 보려면 모든 예외 형식을 클릭하십시오.

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

진단 검색을 직접 열고 예외에서 필터링하며 보려는 예외 유형을 선택할 수 있습니다.

### 처리되지 않은 예외 보고

Application Insights는 [상태 모니터][redfield] 또는 [Application Insights SDK][greenbrown]의 계측 여부와 관계없이 장치, [웹 브라우저][usage], 웹 서버에서 가능하지만 처리되지 않은 예외를 보고합니다.

> [AZURE.NOTE]웹 브라우저에서 CDN 또는 다른 도메인에서 스크립트 파일을 포함하는 경우, 스크립트 태그에 특성 ```crossorigin="anonymous"```이 있는지와 서버에서 이러한 리소스에서 처리 되지 않은 javascript 예외용 스택 추적 및 세부 정보를 가져올 CORS 헤더를 보내는지를 확인하십시오.

그러나 .NET framework는 예외를 포착하기 때문에 어떤 경우에는 항상 이렇게 할 수 없습니다. 따라서 모든 예외를 확실히 참조하려면 소량 예외 처리기를 작성해야만 합니다. 최상의 절차는 기술에 따라 다릅니다. 자세한 내용은 [이 블로그](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx)를 참조하십시오.

### 빌드 상관관계 지정

진단 로그를 읽을 때 라이브 코드가 배포된 이후 소스 코드가 변경되었을 가능성이 높습니다.

따라서 각 예외 또는 추적과 함께 속성에 현재 버전의 URL과 같은 빌드 정보를 배치하는 것이 유용합니다.

모든 예외 호출에는 속성을 개별적으로 추가하는 대신 기본 컨텍스트에서 정보를 설정할 수 있습니다.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

Global.asax.cs 같은 앱 이니셜라이저에서:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> 서버 웹 요청

[웹 서버의 상태 모니터를 설치][redfield] 또는 [웹 프로젝트에 Application Insights 추가][greenbrown]할 때, 원격 분석 요청이 자동으로 전송됩니다. 또한 이것은 메트릭 탐색기와 개요 페이지에서 요청 및 응답 시간 차트에 공급됩니다.

추가 이벤트를 전송하려면 TrackRequest() API를 사용할 수 있습니다.

## <a name="questions"></a>질문 및 답변

### <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.

Application Insights를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치한 것 같습니다.

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다. 이 경우 문제가 해결된 것입니다.

### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?

각 응용 프로그램에서 초당 최대 500개의 이벤트가 보존됩니다. 이벤트는 7일 동안 보존됩니다.

## <a name="add"></a>다음 단계

* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->