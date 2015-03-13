<properties 
	pageTitle="Application Insights API를 사용한 웹 앱의 사용 현황 및 이벤트 추적" 
	description="코드를 몇 줄 삽입하여 사용 현황을 추적하고 문제를 진단합니다." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-06" 
	ms.author="awills"/>

# 웹 앱의 사용자 지정 사용 현황 이벤트 및 메트릭 추적

*Application Insights는 미리 보기 상태입니다.*

웹 응용 프로그램에 몇 줄의 코드를 삽입하여 해당 작업을 수행하는 사용자를 확인합니다. 이벤트, 메트릭 및 페이지 뷰를 추적할 수 있습니다. 모든 사용자에 대해 집계된 데이터의 차트 및 테이블을 확인할 수 있습니다. 

> [AZURE.NOTE] 현재 전체 사용자 환경이 구현된 것은 아닙니다. 사용자 지정 이벤트 및 메트릭을 Application Insights로 보내고 [진단 검색][diagnostic]에서 원시 원격 분석을 검색할 수 있습니다. 그렇지만 다이제스트 통계 차트는 아직 확인할 수 없습니다. 이 차트는 곧 제공될 예정입니다.

<!-- Sample pic -->

* [클라이언트 및 서버 추적](#clientServer)
* [시작하기 전에](#prep)
* [메트릭 추적](#metrics)
* [이벤트 추적](#events)
* [페이지 뷰 추적](#pageViews)
* [속성을 사용하여 데이터를 필터링, 검색 및 분할](#properties)
* [메트릭 및 이벤트 조합](#measurements)
* [기본 속성 값 설정](#defaults)
* [여러 컨텍스트 정의](#contexts)
* [원격 분석 켜기 및 끄기](#disable)
* [다음 단계](#next)



## <a name="clientServer"></a> 클라이언트 및 서버 추적

앱의 클라이언트 쪽(웹 페이지)이나 서버 쪽 또는 둘 다에서 원격 분석을 보낼 수 있습니다.

클라이언트 및 서버 API는 매우 비슷합니다. 사용자의 웹 브라우저와 웹 서버에서 동일한 유형의 원격 분석을 보낼 수 있습니다. 보낼 수 있는 데이터의 범위에는 차이가 있습니다.

* 웹 클라이언트에서 추적하는 방식은 활성 웹 페이지에 많은 JavaScript를 포함되어 있을 때 특히 유용합니다. 예를 들어 사용자가 특정 단추를 클릭하는 빈도나 유효성 검사 오류가 발생하는 빈도를 모니터링할 수 있습니다.
* 웹 서버에서 추적하는 방식은 고객의 장바구니에 포함된 값이나 중단된 주문 수와 같은 비즈니스 메트릭 및 이벤트를 모니터링하는 데 더 유용합니다.

일반적인 ASP.NET 웹 응용 프로그램에서는 웹 마스터 페이지에 trackPageView()에 대한 기본 JavaScript 호출이 있으며 서버 코드에서 이벤트 및 메트릭을 추적하는 일부 호출을 추가하게 됩니다. 클라이언트 쪽 코드는 매우 다양하므로 클라이언트에서 이벤트 및 메트릭을 추적하는 호출도 추가할 수 있습니다.


## <a name="prep"></a>시작하기 전에

다음 작업을 아직 수행하지 않은 경우

* ASP.NET 웹 앱에서 원격 분석 가져오려면
    [프로젝트에 Application Insights 추가][greenbrown]
    웹 서버 코드에 다음을 포함합니다.
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [웹 사용 현황 분석 설정][usage]. JavaScript 초기화 코드는 모니터링 코드를 작성하려는 모든 웹 페이지와 마스터 페이지에 포함되어야 합니다. 
    이러한 경우 개요 블레이드의 사용 현황 분석에서 데이터를 확인할 수 있습니다.

개발 컴퓨터의 앱을 디버그 모드에서 실행하는 경우 몇 초 안에 Application Insights에 결과가 표시됩니다. 앱을 배포하는 경우 서버 및 클라이언트에서 파이프라인을 통해 데이터가 이동하는 데 더 오래 걸립니다.

<!--
## <a name="metrics"></a> 메트릭 추적

페이지 뷰와 같은 기본 사용 현황 데이터를 가져오기 위해 더 이상 작업을 수행할 필요가 없습니다. 하지만 코드 줄 몇 개를 작성하여 사용자가 앱으로 수행하는 작업에 대해 자세히 알아볼 수 있습니다.

예를 들어 앱이 게임인 경우 사용자가 보관하는 평균 점수를 확인하고, 새 버전을 게시한 후에 이러한 정보를 확인하는 것이 더 쉬워졌는지 아니면 더 어려워졌는지를 알려고 할 수 있습니다.

메트릭, 즉 점수와 같은 숫자 값을 추적하려면 스크립트 줄을 앱의 적절한 위치에 삽입합니다.

클라이언트의 JavaScript

    appInsights.trackMetric("Alerts", notifications.Count);

서버의 C#

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

서버의 VB

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

앱을 테스트하고 trackMetric() 호출 실행과 관련해서 사용합니다.


Application Insights에서 응용 프로그램으로 이동한 다음 [메트릭][metrics] 타일까지 클릭합니다. 해당 메트릭을 선택하여 첫 번째 결과를 확인합니다.


그래프에는 모든 사용자가 기록한 값의 최근 평균이 표시됩니다. 


(그렇지만 메트릭이 문제 진단에 맞게 최적화되어 있지 않습니다. 최적화가 필요한 경우 [진단 로깅][diagnostic]을 확인합니다.) -->


## <a name="events"></a>이벤트 추적

이벤트는 여러 사용자에서 평균적인 발생 빈도를 알려줍니다. 예를 들어 사용자가 게임을 완료하는 빈도를 알고 싶은 경우 게임을 끝내는 코드에서 다음과 같은 줄을 삽입합니다.

클라이언트의 JavaScript

    appInsights.trackEvent("EndOfGame");

서버의 C#
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

서버의 VB


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

클라이언트 및 서버 둘 다에서 원격 분석을 보내는 경우 이벤트에 다른 이름을 지정해야 합니다.


## <a name="pageViews"></a>페이지 뷰(클라이언트만 해당)

기본적으로 웹 페이지 제목의 초기화 스크립트는 페이지 뷰를 기록하고 페이지의 상대 URL로 이벤트 이름을 지정합니다. 이러한 호출은 기본 페이지 사용 통계를 제공합니다. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### 사용자 지정 페이지 데이터

원할 경우 호출을 수정하여 이름을 변경하거나 추가 호출을 삽입할 수 있습니다. 예를 들어 단일 페이지 웹 앱에 여러 탭이 표시되는 경우 사용자가 다른 탭으로 전환할 때 페이지 뷰를 기록하려고 할 수 있습니다. 예를 들면 다음과 같습니다.

클라이언트의 JavaScript:

    appInsights.trackPageView("tab1");

여러 다른 HTML 페이지 내에 여러 탭이 있으면 URL도 지정할 수 있습니다.

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>속성을 사용하여 데이터를 필터링, 검색 및 분할

페이지 뷰 및 기타 원격 분석 데이터에 속성 및 측정을 연결할 수 있습니다. 

**속성**은 사용 현황 보고서에서 원격 분석을 필터링하는 데 사용할 수 잇는 문자열 값입니다. 예를 들어 앱이 여러 게임을 제공하는 경우 각 이벤트에 게임 이름을 연결하여 인기가 더 많은 게임을 확인할 수 있습니다.

**측정값**은 사용 현황 보고서에서 통계를 얻을 수 있는 숫자 값입니다.


클라이언트의 JavaScript

    appInsights.trackEvent("EndOfGame",
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
    telemetry.TrackEvent("endOfGame", properties, measurements);


서버의 VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


동일한 방식으로 페이지 뷰에 속성을 연결합니다.

클라이언트의 JavaScript

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] 속성에 개인 식별이 가능한 정보를 기록하지 않도록 주의해야 합니다.


## 시간 제한 페이지 뷰 및 이벤트

이벤트 및 페이지 뷰에 타이밍 데이터를 연결할 수 있습니다. trackEvent 또는 trackPageView를 호출하는 대신 다음 호출을 사용합니다.

클라이언트의 JavaScript

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

호출 시작 및 중지에서 동일한 문자열을 첫 번째 매개 변수로 사용합니다.

## <a name="defaults"></a>기본 속성 값 설정(웹 클라이언트에서는 해당되지 않음)

TelemetryContext에서 기본값을 설정할 수 있습니다. 이러한 기본값은 컨텍스트에서 전송되는 모든 메트릭 및 이벤트에 연결됩니다. 
    

서버의 C#

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
서버의 VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
개별 원격 분석에서 기본값을 재정의할 수 있습니다.

기본 속성 값 그룹 간을 전환하려는 경우 여러 컨텍스트를 설정합니다.



## <a name="next"></a>다음 단계


[검색 이벤트 및 로그][diagnostic]

[문제 해결][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
