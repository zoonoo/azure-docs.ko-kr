<properties 
	pageTitle="Application Insights를 사용하여 사용량 분석" 
	description="Application Insights를 사용한 사용량 분석 개요" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2015" 
	ms.author="awills"/>
 
#Application Insights를 사용하여 사용량 분석

사람들이 사용자의 응용 프로그램을 어떻게 사용하는지 알면 사람들에게 가장 중요한 시나리오로 개발 작업을 하는 데 초점을 맞출 수 있으며, 사람들이 달성하기 더 쉽거나 어려워하는 목표에 대한 통찰력을 얻을 수 있습니다.

Application Insights는 사용자 경험을 개선하고 비즈니스 목표를 충족시키는 데 도움을 주는 응용 프로그램 활용데 대한 명확한 시각을 제공할 수 있습니다.

##즉시 분석
 
프로젝트에 [Application Insights][start]를 추가하면 부수적인 노력 없이 사용자 수를 보여주는 차트를 가져올 수 있습니다.

![Azure에서 찾아보기 > Application Insights > 프로젝트를 찾아 아래로 스크롤](./media/app-insights-overview-usage/01-overview.png)
 
특정 지점에서 수를 보려면 그래프 위의 빈 부분 위를 가리킵니다. 그 밖에도 숫자는 기간에 걸친 평균, 총계 또는 고유한 사용자의 수와 같이 기간에 걸쳐 집계된 값을 보여줍니다.

웹 응용 프로그램에서 사용자는 쿠키를 사용하여 계산됩니다. 여러 브라우저를 사용하거나, 쿠키를 지우거나, 개인정보 보호 기능을 사용하는 사람은 여러 번 계산됩니다.

웹 세션은 비활성 30분 후 계산됩니다. 휴대폰 또는 기타 장치에서 세션은 앱이 몇 초 이상 일시 중단되는 경우에 계산됩니다.

차트를 클릭하면 세부 정보를 수 있습니다. 예:

![개요 블레이드에서 세션 차트 클릭](./media/app-insights-overview-usage/02-sessions.png)
 
(이 예제는 웹사이트에서 나온 것이지만 차트 모양은 장치에서 실행되는 앱과 유사합니다.)

사항이 변경되고 있는지 확인하려면 이전 주와 비교합니다.

![단일 메트릭을 보여주는 차트를 선택하고 이전 주로 전환](./media/app-insights-overview-usage/021-prior.png)

예를들어 사용자와 새 사용자의 경우 두 메트릭을 비교합니다.

![차트를 선택하고 메트릭을 검색하여 선택 또는 선택 해제합니다.](./media/app-insights-overview-usage/031-dual.png)

브라우저, 운영체제 또는 도시와 같은 속성으로 데이터를 그룹화(구분)합니다.

![단일 메트릭을 보여주는 차트를 선택하고 그룹화로 전환한 다음 속성 선택](./media/app-insights-overview-usage/03-browsers.png)

 
##페이지 사용

가장 인기 있는 페이지의 분석과 함께 보다 확대된 버전을 가져오려면 페이지 보기 차트를 클릭합니다.


![개요 블레이드에서 페이지 보기 차트 클릭](./media/app-insights-overview-usage/05-games.png)
 
위의 예시는 게임 웹 사이트에서 나온 것입니다. 여기에서 바로 볼 수 있습니다.

* 사용량은 지난 주보다 개선되지 않았습니다. 검색 엔진 최적화에 대해 생각해볼 수 있을까요?
* 홈 페이지보다 게임 페이지를 보는 사람들이 줄어들었습니다. 왜 홈 페이지가 사람들이 게임을 플레이하도록 끌어당기지 못할까요?
* '낱말 맞추기'는 가장 인기 있는 게임입니다. 새로운 아이디어 및 개선 사항에 우선 순위를 두어야 합니다.

##사용자 지정 추적

별도의 웹 페이지에서 각각의 게임을 구현하는 대신, 웹 페이지에서 대부분 Javascript로 코딩된 기능을 사용하여 단일 페이지 앱으로 리펙터링하기로 결정한다고 가정해 봅니다. 이를 통해 사용자는 한 게임에서 다른 게임으로 빠르게 전환할 수 있으며 한 페이지에서 여러 게임도 할 수 있습니다.

하지만 별도의 웹 페이지에서 게임할 때와 완전히 동일한 방법으로 각각의 게임이 열리는 횟수를 로그하기 위해 여전히 Application Insights를 사용하고자 합니다. 간단합니다. 열리는 새 '페이지'를 기록하려는 JavaScript에 원격 분석 모듈에 대한 호출을 삽입하면 됩니다.

	telemetryClient.trackPageView(game.Name);

##사용자 지정 이벤트

응용 프로그램이 사용되는 방식을 이해하기 위해 다양한 방법으로 원격 분석을 사용할 수 있습니다. 하지만 페이지 보기가 포함된 메시지를 항상 혼합하고 싶지는 않습니다. 대신, 사용자 지정 이벤트를 사용합니다. 장치 앱, 웹 페이지 또는 웹 서버에서 이를 전송할 수 있습니다.

(JavaScript)

    telemetryClient.trackEvent("GameEnd");

(C#)

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

(VB)

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


가장 자주 수행하는 사용자 지정 이벤트가 개요 블레이드에 나열됩니다.

![개요 블레이드에서 아래로 스크롤하고 사용자 지정 이벤트를 클릭합니다.](./media/app-insights-overview-usage/04-events.png)

이벤트의 총 수를 확인하려면 표의 헤드를 클릭합니다. 이벤트 이름과 같은 다양한 특성으로 차트를 구분할 수 있습니다.

![하나의 메트릭만 보여주는 차트를 선택합니다. 그룹화로 전환합니다. 속성을 선택합니다. 일부 속성은 사용할 수 없습니다.](./media/app-insights-overview-usage/06-eventsSegment.png)

특별히 유용한 타임라인의 기능은 다른 메트릭 및 이벤트와 변경 내용을 연관지을 수 있다는 점입니다. 예를들어 더 많은 게임이 플레이되면 중단된 게임에도 상승 효과가 보이기를 기대하게 됩니다. 하지만 중단된 게임의 상승 효과는 균형이 맞지 않습니다. 높은 부하가 사용자가 받아들일 수 없는 문제를 유발하는지 알고 싶습니다.

##특정 이벤트 자세히 알아보기

일반적인 세션이 진행되는 방식에 대해 더 잘 이해하기 위해 특정 이벤트 종류를 포함하는 특정 사용자 세션에 초점을 맞추고자할 수 있습니다.

이 예제에서는 사용자가 실제로 게임을 시작하지 않고 로그아웃하는 경우 호출되는 "NoGame" 사용자 지정 이벤트를 코딩했습니다. 사용자는 왜 그렇게 할까요? 특정 항목을 자세히 다루면 실마리를 얻게 될 것입니다.

앱에서 수신한 사용자 지정 이벤트는 개요 블레이드에 이름별로 나열됩니다.


![개요 블레이드에서 사용자 지정 이벤트 종류 중 하나를 클릭합니다.](./media/app-insights-overview-usage/07-clickEvent.png)
 
관심 있는 이벤트를 클릭하고 최근에 발생한 특정 항목을 선택합니다.


![요약 차트 아래의 목록에서 이벤트를 클릭합니다.](./media/app-insights-overview-usage/08-searchEvents.png)
 
해당하는 특정 NoGame 이벤트가 발생한 세션에 대한 모든 원격 분석을 살펴봅시다.


!['세션에 대한 모든 원격 분석'을 클릭합니다.](./media/app-insights-overview-usage/09-relatedTelemetry.png)
 
예외 사항이 없었으므로 사용자는 일부 오류로 인해 플레이하지 못한 것입니다.
 
이 세션에 대한 페이지 보기를 제외한 모든 종류의 원격 분석을 필터링할 수 있습니다.


![](./media/app-insights-overview-usage/10-filter.png)
 
그리고 이제 단순히 최신 점수를 확인하기 위해 이 사용자가 로그인한 것을 확인할 수 있습니다. 이를 더 쉽게 하기 위해 사용자 스토리 개발을 고려해야 할 수도 있습니다. (그리고 이 특정 스토리가 발생할 때 보고할 사용자 지정 이벤트를 구현해야 합니다.)

##속성을 사용하여 데이터를 필터링, 검색 및 분할
이벤트에 임의의 태그 및 숫자 값을 연결할 수 있습니다.
 

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

동일한 방식으로 페이지 뷰에 속성을 연결합니다.

클라이언트의 JavaScript

    appInsights.trackPageView("Win", 
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});

진단 검색에서 이벤트의 개별 항목을 클릭하여 속성을 봅니다.


![이벤트 목록에서 이벤트를 열고 '...'를 클릭하여 더 많은 속성 보기](./media/app-insights-overview-usage/11-details.png)
 
검색 필드를 사용하여 특정 속성 값이 포함된 이벤트 항목을 볼 수 있습니다.


![검색 필드에 값 입력](./media/app-insights-overview-usage/12-searchEvents.png)


##A | B 테스트

기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 태그를 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 응용 프로그램이 전송하는 모든 원격 분석 메시지에 추가됩니다.

그런 다음 Application Insights 포털에서 태그로 데이터를 필터링 및 그룹화(구분)할 수 있게 되므로 다른 버전과 비교할 수 있습니다.

서버의 C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");

서버의 VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")

개별 원격 분석에서 기본값을 재정의할 수 있습니다.

유니버설 아니셜라이저를 설정하여 새로운 모든 TelemetryClients는 사용자 컨텍스트를 자동으로 사용할 수 있습니다.

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


##빌드 - 측정 - 학습

분석을 사용하면 문제를 해결하기 위해 생각한 무언가가 아닌 개발 주기의 통합 부분이 됩니다. 다음은 몇 가지 팁입니다.

* 응용 프로그램의 주요 메트릭을 결정합니다. 가능한 많은 사용자를 원하시나요? 아니면 매우 만족하는 소규모의 사용자를 원하시나요? 방문수 또는 매출을 최대화하겠습니까?
* 각 스토리를 측정하도록 계획합니다. 새 사용자 스토리 또는 기능을 스케치하거나 기존 내용을 업데이트할 계획을 세우면 항상 변화의 성공을 어떻게 측정할지 생각해 봅니다. 코딩을 시작하기 전에 "작동한다면 우리 메트릭에 어떠한 영향을 미칠까? 새 이벤트를 추적해야 하는가?"라고 물어봅니다. 물론, 기능이 실시간이면 분석 내용을 자세히 보고 결과에 대한 조치를 취해야 합니다. 
* 기타 메트릭을 주요 메트릭과 연결합니다. 예를들어 '즐겨찾기' 기능을 추가하는 경우 사용자가 즐겨찾기를 추가하는 빈도를 알고 싶습니다. 하지만 사용자가 즐겨찾기를 다시 방문하는 빈도를 아는 데에 더 많은 관심이 있을 수 있습니다. 또한 가장 중요한 것은 궁극적으로 즐겨찾기를 사용하는 고객이 사용자의 제품을 더 구입하느냐입니다.
* 카나리아 테스트입니다. 새로운 기능을 일부 사용자에게만 표시할 수 있는 기능 스위치를 설정합니다. Application Insights를 사용하여 새로운 기능이 예상하는 방식으로 사용되고 있는지 확인합니다. 조정한 다음 더 광범위한 대상에게 이를 릴리스합니다.
* 사용자에게 이야기하세요! 분석은 자체적으로 충분하지 않지만 좋은 고객 관계 유지를 보완합니다.





<!--Link references-->

[start]: app-insights-get-started.md


<!--HONumber=54-->