<properties 
	pageTitle="Application Insights를 사용하여 Windows 스토어 및 Windows Phone 앱에서 사용량 모니터링" 
	description="Application Insights를 사용하여 Windows 장치 앱의 사용량을 분석합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>

#  Application Insights를 사용하여 Windows 스토어 및 Windows Phone 앱에서 사용량 모니터링

*Application Insights는 미리 보기 상태입니다.*

사용자 수 및 사용자가 앱에서 보는 페이지에 대해 알아봅니다. Application Insights는 최신 데이터를 제공합니다. 또한 앱에 코드 몇 줄을 삽입하여 사용자가 앱을 입수한 경로 및 앱으로 달성하는 내용에 대해 더 알아볼 수 있습니다.

아직 실행하지 않은 경우 [앱 프로젝트에 Application Insights][windows]를 추가하고 다시 게시합니다.


## <a name="usage"></a>사용량 추적

개요 타임라인에서 사용자 및 세션 차트를 클릭하여 더 자세한 분석을 확인할 수 있습니다.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* **사용자**는 익명으로 추적되므로 다른 장치상의 동일한 사용자는 두 번으로 카운트됩니다.
* **세션**은 앱이 일시 중단될 때 카운트됩니다(실수로 발생한 일시 중단을 카운트하지 않기 위한 짧은 간격보다 긴 시간).

#### 구분

다양한 기준으로 분석 결과를 얻으려면 차트를 구분합니다. 예를들어, 앱의 각 버전을 사용하는 사용자 수를 보려면 사용자 차트를 열어 응용 프로그램 버전별로 구분합니다.

![사용자 차트에서 구분을 전환하고 응용 프로그램 버전을 선택합니다.](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### 페이지 보기

사용자가 앱을 따라가는 경로를 알아보려면 [페이지 보기 원격 분석][api]을 코드에 삽입합니다.

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

페이지 보기 차트의 결과 보기 및 해당 세부 정보 열기:

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

특정 항목의 세부 정보를 보려면 모든 페이지를 클릭합니다.

#### 사용자 지정 이벤트

앱에서 사용자 지정 이벤트를 전송할 코드를 삽입하여 사용자 동작 및 특정 기능 및 시나리오의 사용량을 추적할 수 있습니다.

예:

    telemetry.TrackEvent("GameOver");

데이터는 사용자 지정 이벤트 표에 표시됩니다. 메트릭 탐색기에서 집계 보기를 참조하거나 특정 항목을 볼 수 있는 모든 이벤트를 클릭할 수 있습니다.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


모든 이벤트에 문자열 및 숫자 속성을 추가할 수 있습니다.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


정의한 항목을 비롯한 세부 속성을 보려면 발생 항목을 클릭합니다.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

사용자 지정 이벤트에 대한 자세한 내용은 [API 참조][api]를 참조하세요.

## 세션

세션은 Application Insights의 기본 개념이며, 충돌 또는 사용자가 직접 코딩하는 사용자 지정 이벤트 등 모든 원격 분석 이벤트를 특정 사용자 세션과 연결하려고 합니다.

장치 특성, 지리적 위치, 운영 체제 등 각 세션에 대한 풍부한 컨텍스트 정보가 수집됩니다.

[문제를 진단][diagnostic]할 때 모든 요청, 기록된 이벤트, 예외 또는 추적을 포함하여 문제가 발생한 세션과 관련된 모든 원격 분석을 찾을 수 있습니다.

세션은 장치, 운영 체제 또는 위치 같은 컨텍스트의 인기에 대한 유용한 척도를 제공합니다. 예를 들어 장치별로 그룹화된 세션의 개수를 표시하면 해당 장치가 사용자의 앱에서 사용되는 빈도를 페이지 보기를 세는 것보다 더 정확히 파악할 있습니다. 이 정보는 장치별 문제의 심사에 유용한 입력이 될 수 있습니다.


#### 세션이란?

세션은 사용자와 응용 프로그램 간의 단일 발생을 나타냅니다. 가장 간단한 형태의 세션은 앱을 시작하는 사용자로 시작하고 사용자가 앱을 떠날 때 완료됩니다. 모바일 앱의 경우, 세션은 앱이 20초 넘는 동안 일시 중단되면(백그라운드로 이동) 종료됩니다. 앱이 다시 시작하면 새 세션이 시작됩니다. 기본적으로, 사용자는 하루 또는 심지어 한 시간에도 여러 세션을 가질 수 있습니다.

**세션 기간**은 세션의 첫 번째와 마지막 원격 분석 항목 사이의 시간 범위를 표시하는 메트릭입니다. (시간 제한 기간은 포함하지 않습니다.)


특정 간격의 **세션 수**는 이 간격 중에 일부 활동이 있는 고유한 세션 수라고 정의됩니다. 지난 주에 대한 일별 세션 수와 같이 오랜 시간 범위를 보는 경우, 일반적으로 이 세션 수는 총 세션 수와 같습니다.

그러나 시간별 단위 같은 더 짧은 시간 범위를 탐색하는 경우, 여러 시간에 걸친 긴 세션은 세션이 활성화된 각 시간에 대해 계산됩니다.

## 사용자 및 사용자 수

각 사용자 세션은 앱을 사용할 때 생성되고 장치의 로컬 저장소에 보관되는 단일 사용자 ID와 연결됩니다. 여러 장치를 사용하는 사용자는 두 번 이상 계산됩니다.

특정 간격의 **사용자 수** 메트릭은 이 간격 중에 기록된 활동이 있는 고유한 사용자 수라고 정의됩니다. 따라서 단위가 한 시간보다 더 짧도록 시간 범위를 설정한 경우, 긴 세션을 가진 사용자는 여러 번 계산됩니다.

**새 사용자**는 이 간격 중에 앱에 대한 첫 번째 세션이 발생한 사용자 수를 계산합니다.


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
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=August15_HO6-->