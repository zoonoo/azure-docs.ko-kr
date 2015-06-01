<properties 
	pageTitle="Application Insights를 사용하여 Windows 스토어 및 Windows Phone 앱에서 사용량 모니터링" 
	description="Application Insights를 사용하여 Windows 장치 앱의 사용량을 분석합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

#  Application Insights를 사용하여 Windows 스토어 및 Windows Phone 앱에서 사용량 모니터링

*Application Insights는 미리 보기 상태입니다.*

사용자 수 및 사용자가 앱에서 보는 페이지에 대해 알아봅니다. Application Insights는 최신 데이터를 제공합니다. 또한 앱에 코드 몇 줄을 삽입하여 사용자가 앱을 입수한 경로 및 앱으로 달성하는 내용에 대해 더 알아볼 수 있습니다.

아직 실행하지 않은 경우 [앱 프로젝트에 Application Insights][windows]를 추가하고 다시 게시합니다.


## <a name="usage"></a>사용량 추적

개요 타임라인에서 사용자 및 세션 차트를 클릭하여 더 자세한 분석을 확인할 수 있습니다.


![](./media/appinsights/appinsights-d018-oview.png)

* **사용자**는 익명으로 추적되므로 다른 장치상의 동일한 사용자는 두 번으로 카운트됩니다.
* **세션**은 앱이 일시 중단될 때 카운트됩니다(실수로 발생한 일시 중단을 카운트하지 않기 위한 짧은 간격보다 긴 시간).

#### 구분

다양한 기준으로 분석 결과를 얻으려면 차트를 구분합니다. 예를들어, 앱의 각 버전을 사용하는 사용자 수를 보려면 사용자 차트를 열어 응용 프로그램 버전별로 구분합니다.

![사용자 차트에서 구분을 전환하고 응용 프로그램 버전을 선택합니다.](./media/appinsights/appinsights-d25-usage.png)


#### 페이지 보기

사용자가 앱을 따라가는 경로를 알아보려면 [페이지 보기 원격 분석][api]을 코드에 삽입합니다.

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

페이지 보기 차트의 결과 보기 및 해당 세부 정보 열기:

![](./media/appinsights/appinsights-d27-pages.png)

특정 항목의 세부 정보를 보려면 모든 페이지를 클릭합니다.

#### 사용자 지정 이벤트

앱에서 사용자 지정 이벤트를 전송할 코드를 삽입하여 사용자 동작 및 특정 기능 및 시나리오의 사용량을 추적할 수 있습니다.

예:

    telemetry.TrackEvent("GameOver");

데이터는 사용자 지정 이벤트 표에 표시됩니다. 메트릭 탐색기에서 집계 보기를 참조하거나 특정 항목을 볼 수 있는 모든 이벤트를 클릭할 수 있습니다.

![](./media/appinsights/appinsights-d28-events.png)


모든 이벤트에 문자열 및 숫자 속성을 추가할 수 있습니다.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


정의한 항목을 비롯한 세부 속성을 보려면 발생 항목을 클릭합니다.


![](./media/appinsights/appinsights-d29-eventProps.png)

사용자 지정 이벤트에 대한 자세한 내용은 [API 참조][api]를 참조하세요.



## <a name="debug"></a>디버그 및 릴리스 모드 비교

#### 디버그

디버그 모드에서 빌드하는 경우 이벤트는 생성되자마자 전송됩니다. 인터넷 연결이 손실되고 다시 연결되기 전에 앱을 종료하는 경우 오프라인 원격 분석이 삭제됩니다.

#### 릴리스

릴리스 구성에서 빌드하는 경우 이벤트는 장치에 저장되고 응용 프로그램이 다시 시작하면 전송됩니다. 데이터는 처음 사용하는 응용 프로그램에서도 전송됩니다. 시작 시 인터넷에 연결이 없는 경우 현재 수명 주기에 대한 원격 분석뿐 아니라 이전 원격 분석도 저장되며 다음 다시 시작 시 전송됩니다.

## <a name="next"></a>다음 단계

[사용자 확인][knowUsers]

[메트릭 탐색기에 대한 자세한 정보][metrics]


[문제 해결][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54-->