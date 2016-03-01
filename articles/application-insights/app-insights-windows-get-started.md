<properties
	pageTitle="Windows Phone 및 스토어 앱에 대한 분석 | Microsoft Azure"
	description="Windows 장치 앱의 사용량 및 성능을 분석합니다."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# Windows Phone 및 스토어 앱에 대한 분석

Microsoft는 장치 devOps에 대해 클라이언트측 분석을 위한 [HockeyApp](http://hockeyapp.net/)과 서버측을 위한 [Application Insights](app-insights-overview.md)라는 두 가지 솔루션을 제공합니다.

[HockeyApp](http://hockeyapp.net/)은 iOS, OS X, Android 또는 Windows 장치 앱 뿐만 아니라 Xamarin, Cordova, Unity에 기반하는 플랫폼 간 앱에 대한 Mobile DevOps 솔루션일 입니다. 이를 통해 빌드를 베타 테스터에게 배포하고, 충돌 데이터를 수집하고, 사용자 메트릭 및 피드백을 얻을 수 있습니다. Visual Studio Team Services와 통합되어 있기 때문에 손쉬운 빌드 배포 및 작업 항목 통합이 가능합니다.

다음으로 이동합니다.

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp 블로그](http://hockeyapp.net/blog/)
* [Hockeyapp Preseason](http://hockeyapp.net/preseason/)을 조인하여 초기 릴리스를 얻습니다.

앱에 서버측이 있으면, [Application Insights](app-insights-overview.md)를 사용하여 [ASP.NET](app-insights-asp-net.md) 또는 [J2EE](app-insights-java-get-started.md)에서 앱의 웹 서버측을 모니터링합니다.

## Windows 장치를 위한 Application Insights SDK

HockeyApp이 권장되지만 Application Insights SDK의 이전 버전도 Windows 장치 앱의 [크래시][windowsCrash] 및 [사용][windowsUsage]을 모니터링하는 데 사용할 수 있습니다.

이전 장치 SDK에 대한 지원은 단계적으로 중단됩니다.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


이전 SDK를 설치하려면 다음이 필요 합니다.

* [Microsoft Azure][azure] 구독.
* Visual Studio 2013 이상.


### 1\. Application Insights 리소스 가져오기 

[Azure 포털][portal]에서 Application Insights 리소스를 만듭니다.

새 리소스를 만듭니다.

![새로 만들기, 개발자 서비스, Application Insights 선택](./media/app-insights-windows-get-started/01-new.png)

Azure에서 [리소스][roles]는 서비스의 인스턴스입니다. 이 리소스는 사용자에게 분석 및 제공되는 앱의 원격 분석을 하는 곳입니다.

#### 계측 키 복사

키는 리소스를 식별합니다. 리소스에 데이터를 보내도록 SDK를 구성하려면 리소스를 식별해야 합니다.

![필수 항목 드롭다운 서랍을 열고 계측 키를 선택합니다.](./media/app-insights-windows-get-started/02-props.png)


### 2\. 앱에 Application Insights SDK 추가

Visual Studio에서 프로젝트에 적합한 SDK를 추가합니다.


* C++ 앱의 경우 아래에서 설명된 NuGet 패키지 대신 [C++ SDK](https://github.com/Microsoft/ApplicationInsights-CPP)를 사용합니다.

Windows 유니버설 앱인 경우 Windows Phone 및 Windows 프로젝트 모두에 대해 아래 단계를 반복합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. "Application Insights"를 검색합니다.

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. **Windows 응용 프로그램용 Application Insights**을 선택합니다.

4. ApplicationInsights.config 파일을 프로젝트의 루트에 추가하고 포털에서 복사한 계측 키를 삽입합니다. 이 구성파일에 대한 샘플 xml는 아래와 같습니다.

	```xml

		<?xml version="1.0" encoding="utf-8" ?>
		<ApplicationInsights>
			<InstrumentationKey>YOUR COPIED INSTRUMENTATION KEY</InstrumentationKey>
		</ApplicationInsights>
	```

    ApplicationInsights.config 파일의 속성 설정: **빌드 작업** == **콘텐츠** 및 **출력 디렉터리로 복사** == **항상 복사**
	
	![](./media/app-insights-windows-get-started/AIConfigFileSettings.png)

5. 다음 초기화 코드를 추가합니다. 이 코드를 `App()` 생성자에 추가하는 것이 좋습니다. 다른 곳에서 추가하면 첫번째 pageviews의 자동 컬렉션을 누락할 수 있습니다.

```C#

	public App()
	{
	   // Add this initilization line. 
	   WindowsAppInitializer.InitializeAsync();
	
	   this.InitializeComponent();
	   this.Suspending += OnSuspending;
	}  
```

**Windows 유니버설 앱**: Phone 및 스토어 프로젝트 모두에 대해 단계를 반복합니다. [Windows 8.1 유니버설 앱의 예](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

### <a name="network"></a>3. 앱에 대한 네트워크 액세스 설정

앱이 아직 [나가는 네트워크 액세스를 요청](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)하지 않은 경우 해당 매니페스트에 [필요한 기능](https://msdn.microsoft.com/library/windows/apps/br211477.aspx)으로 추가해야 합니다.

### <a name="run"></a>4. 프로젝트 실행

[F5를 사용하여 응용 프로그램을 실행](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)하고 이를 사용하여 일부 원격 분석을 생성합니다.

Visual Studio에 수신된 이벤트의 수가 표시됩니다.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

디버그 모드에서 원격 분석은 생성되는 즉시 보내집니다. 릴리스 모드에서 원격 분석은 장치에 저장되며 앱이 다시 시작하는 경우에만 보내집니다.


### <a name="monitor"></a>5. 모니터 데이터 보기

프로젝트에서 Application Insights를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-windows-get-started/appinsights-04-openPortal.png)


처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

더 많은 데이터를 기대하는 경우 몇 초 후에 새로고침을 클릭합니다.

보다 자세한 정보를 확인하려면 원하는 차트를 클릭합니다.


### <a name="deploy"></a>5. 스토어에 응용 프로그램 게시

[응용 프로그램을 게시](http://dev.windows.com/publish)하고 사용자가 다운로드하고 사용함에 따른 데이터 누적을 관찰합니다.

### 원격 분석을 사용자 지정

#### 수집기를 선택합니다.

Application Insights SDK는 앱의 서로 다른 유형의 데이터를 자동으로 수집하는 여러 수집기를 포함합니다. 기본적으로, 모두 활성화되어있습니다. 하지만 앱 생성자에서 초기화에 사용할 수집기를 선택할 수 있습니다.

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session 
       | WindowsCollectors.UnhandledException);

#### 사용자 고유의 원격 분석 데이터 전송

[API][api]를 사용하여 Application Insights에 이벤트, 메트릭 및 진단 데이터를 보냅니다. 요약하면 다음과 같습니다:

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

자세한 내용은 [사용자 지정 이벤트 및 메트릭][api]을 참조하세요.

## 다음 작업

* [앱에서 충돌 감지 및 진단][windowsCrash]
* [매트릭에 대해 알아보기][metrics]
* [진단 검색에 대해 알아보기][diagnostic]
* [앱 사용량 추적][windowsUsage]
* [사용자 지정 원격 분석 전송에 API 사용][api]
* [문제 해결][qna]

* [앱의 충돌 분석, 베타 분포 및 피드백을 위해 HockeyApp 사용](http://hockeyapp.net/)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=AcomDC_0224_2016-->