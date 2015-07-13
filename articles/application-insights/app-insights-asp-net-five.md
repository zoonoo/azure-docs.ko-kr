<properties 
	pageTitle="ASP.NET 5용 Application Insights" 
	description="응용 프로그램의 가용성, 성능 및 사용 현황을 모니터링합니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# ASP.NET 5용 Application Insights

Visual Studio Application Insights를 사용하여 웹 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

![예제](./media/app-insights-asp-net-five/sample.png)

[Microsoft Azure](http://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다.

## ASP.NET 5 프로젝트 만들기

아직 만들지 않았다면 지금 만드세요.

Visual Studio 2015의 표준 ASP.NET 5 프로젝트 템플릿을 사용합니다.


## Application Insights 리소스 만들기

[Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. ASP.NET 옵션을 선택합니다.

![새로 만들기, 개발자 서비스, Application Insights를 차례대로 클릭](./media/app-insights-asp-net-five/01-new-asp.png)

열리는 [리소스][roles] 블레이드에서 앱의 성능 및 사용 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.

선택하는 응용 프로그램 유형에 따라 [메트릭 탐색기][metrics]에 표시되는 리소스 블레이드 및 속성의 기본 콘텐츠가 설정됩니다.

##  계측 키를 사용하여 프로젝트를 구성합니다.

Application Insights 리소스에서 키를 복사합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-asp-net-five/02-props-asp.png)

ASP.NET 5 프로젝트에서 `config.json`에 붙여 넣습니다.

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

또는 동적으로 구성하려면 응용 프로그램의 시작 클래스에 이 코드를 추가합니다.

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## 프로젝트에 Application Insights 추가


#### NuGet 패키지 참조

NuGet 패키지의 [최신 릴리스 번호](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases)를 찾습니다.

`project.json`을 열고 `dependencies` 섹션을 편집합니다.

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### 구성 파일 구문 분석

`startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

`Startup` 메서드:

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

`ConfigurationServices` 메서드:

    services.AddApplicationInsightsTelemetry(Configuration);

`Configure` 메서드:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## JavaScript 클라이언트 계측 추가

_Layout.cshtml 파일이 있으면 다음 코드를 파일에 삽입합니다. 파일이 없으면 추적하려는 아무 페이지에 코드를 넣습니다.

파일의 맨 위에서 삽입을 정의합니다.

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

`</head>` 태그 앞에, 그리고 다른 모든 스크립트 앞에 Html 도우미를 삽입합니다. 페이지에서 보고하려는 모든 사용자 지정 JavaScript 원격 분석을 이 코드 조각 뒤에 삽입해야 합니다.

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## 앱을 실행합니다.

Visual Studio에서 응용 프로그램을 디버깅하거나 웹 서버에 게시합니다.

## 앱에 대한 데이터 보기

[Azure 포털][portal]로 돌아가서 Application Insights 리소스를 찾습니다. 개요 블레이드에 데이터가 없는 경우 1-2분 정도 기다린 후 새로 고침을 클릭합니다.

* [앱 사용량 추적][usage]
* [성능 문제 진단][detect]
* [가용성을 모니터링하도록 웹 테스트 설정][availability]



## 공개 소스

[코드를 읽고 참여하기](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=62-->