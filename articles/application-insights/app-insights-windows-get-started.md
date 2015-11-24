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
	ms.date="11/11/2015"
	ms.author="awills"/>

# Windows Phone 및 스토어 앱에 대한 분석



Visual Studio Application Insights를 사용하면 게시된 응용 프로그램의 사용량 및 성능을 모니터링할 수 있습니다.


> [AZURE.NOTE]크래시 보고, 분석, 배포 및 피드백 관리를 가져오는 데 [HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps)를 사용하는 것이 좋습니다.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Windows 장치 프로젝트에 대한 Application Insights 설정

필요한 사항:

* [Microsoft Azure][azure] 구독.
* Visual Studio 2013 이상.

**C++ UAP 앱** - [Application Insights C++ 설치 가이드](https://github.com/Microsoft/ApplicationInsights-CPP)를 참조하세요.

### <a name="new"></a>새 Windows 앱 프로젝트를 만드는 경우...

**새 프로젝트** 대화 상자에서 **Application Insights**를 선택합니다.

로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>또는 기존 프로젝트의 경우...

솔루션 탐색기에서 Application Insights를 추가합니다.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Windows 유니버설 앱**: 휴대폰 및 스토어 프로젝트 모두에 반복합니다. [Windows 8.1 유니버설 앱의 예](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. 앱에 대한 네트워크 액세스 설정

앱이 아직 [인터넷 액세스를 요청](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx)하지 않은 경우 해당 매니페스트에 [필요한 기능](https://msdn.microsoft.com/library/windows/apps/br211477.aspx)으로 추가해야 합니다.

## <a name="run"></a>4. 프로젝트 실행

[F5를 사용하여 응용 프로그램을 실행](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)하고 이를 사용하여 일부 원격 분석을 생성합니다.

Visual Studio에 수신된 이벤트의 수가 표시됩니다.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

디버그 모드에서 원격 분석은 생성되는 즉시 보내집니다. 릴리스 모드에서 원격 분석은 장치에 저장되며 앱이 다시 시작하는 경우에만 보내집니다.


## <a name="monitor"></a>5. 모니터 데이터 보기

[Azure 포털](https://portal.azure.com)에서 이전에 만든 Application Insights 리소스를 엽니다.

처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

더 많은 데이터를 기대하는 경우 몇 초 후에 **새로 고침**을 클릭합니다.

보다 자세한 정보를 확인하려면 원하는 차트를 클릭합니다.


## <a name="deploy"></a>5. 스토어에 응용 프로그램 게시

[응용 프로그램을 게시](http://dev.windows.com/publish)하고 사용자가 다운로드하고 사용함에 따른 데이터 누적을 관찰합니다.

## 원격 분석을 사용자 지정

#### 수집기 선택

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

자세한 내용은 [API 개요: 사용자 지정 이벤트 및 메트릭][api]을 참조하세요.

## 다음 작업

* [앱에서 충돌 감지 및 진단][windowsCrash]
* [매트릭에 대해 알아보기][metrics]
* [진단 검색에 대해 알아보기][diagnostic]




## SDK의 새 릴리스로 업그레이드

[새 SDK 버전이 출시](app-insights-release-notes-windows.md)될 때:

* 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
* 설치된 Application Insights 패키지를 선택하고 **작업: 업그레이드**를 선택합니다.


## <a name="usage"></a>다음 단계


[앱에서 충돌 감지 및 진단][windowsCrash]

[진단 로그 캡처 및 검색][diagnostic]


[앱 사용량 추적][windowsUsage]

[사용자 지정 원격 분석 전송에 API 사용][api]

[문제 해결][qna]



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

<!---HONumber=Nov15_HO4-->