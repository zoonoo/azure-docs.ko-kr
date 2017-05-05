---
title: "Azure Application Insights를 사용한 웹 응용 프로그램의 사용 현황 분석 | Microsoft Docs"
description: "Application Insights를 사용한 사용 현황 분석 개요"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Application Insights를 사용한 웹 응용 프로그램의 사용 현황 분석
사람들이 사용자의 응용 프로그램을 어떻게 사용하는지 알면 사용자에게 가장 중요한 시나리오로 개발 작업을 하는 데 초점을 맞출 수 있으며, 사람들이 달성하기 더 쉽거나 어려워하는 목표에 대한 통찰력을 얻을 수 있습니다.

[Azure Application Insights](app-insights-overview.md)는 2단계 사용 현황 추적을 제공합니다.

* **사용자, 세션 및 페이지 보기 데이터** - 독점적으로 제공합니다.  
* **사용자 지정 원격 분석** - [코드를 작성](app-insights-api-custom-events-metrics.md)하여 앱의 사용자 환경을 통해 사용자를 추적합니다. 


## <a name="get-started"></a>시작

앱 서버 코드와 웹 페이지에 모두 Application Insights를 설치하여 최상의 환경을 얻습니다. 앱의 클라이언트 및 서버 구성 요소는 분석을 위해 Azure Portal로 원격 분석을 다시 보냅니다.

1. **서버 코드:** [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) 또는 [기타](app-insights-platforms.md) 앱에 적합한 모듈을 설치합니다.

    * *서버 코드를 설치하지 않으려면 [Azure Application Insights 리소스를 만들기만](app-insights-create-new-resource.md) 하면 됩니다.*

2. **웹 페이지 코드:** [Azure Portal](https://portal.azure.com)을 열고 앱에 대한 Application Insights 리소스를 연 다음 **시작하기 > Monitor and Diagnose Client-Side(클라이언트 쪽 모니터링 및 진단)**을 엽니다. 

    ![마스터 웹 페이지의 머리글에 스크립트를 복사합니다.](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **원격 분석 가져오기:** 몇 분 동안 디버그 모드에서 프로젝트를 실행한 다음 Application Insights의 개요 블레이드에서 결과를 찾습니다.

    앱을 게시하여 앱의 성능을 모니터링하고 사용자가 앱으로 수행하는 작업을 확인합니다.

## <a name="usage-analysis-out-of-the-box"></a>즉시 사용 현황 분석
사용 현황 블레이드를 엽니다.

![사용자, 세션 및 페이지 보기 차트](./media/app-insights-web-track-usage/14-usage.png)

특정 지점에서 수를 보려면 그래프 위의 빈 부분 위를 가리킵니다. 그 밖에도 수치는 기간에 걸친 평균, 총계 또는 고유한 사용자의 수와 같이 기간에 걸쳐 집계된 값을 보여 줍니다.

이러한 차트에 표시되는 내용

* **사용자:** 차트의 시간 범위에 따른 고유한 활성 사용자 수입니다. 웹 응용 프로그램에서 사용자는 쿠키를 사용하여 계산됩니다. 여러 브라우저를 사용하거나, 쿠키를 지우거나, 개인정보 보호 기능을 사용하는 사람은 여러 번 계산됩니다.
* **인증된 사용자**는 코드에 [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) 호출을 삽입한 경우에 계산됩니다.
* **세션:** 활성 세션 수입니다. 웹 세션은 비활성 30분 후 계산됩니다. 
* **페이지 보기** trackPageView() 호출 수를 계산합니다. 일반적으로 각 웹 페이지에서 한 번 호출됩니다.

보다 자세한 정보를 확인하려면 차트를 클릭합니다. 차트의 시간 범위를 변경할 수 있습니다.

### <a name="where-do-my-users-live"></a>내 사용자는 어디에 살고 있나요?
보다 자세히 보여 주는 열려 있는 또 다른 블레이드를 보려면 사용자 차트의 빈 부분을 클릭합니다.

![사용 현황 블레이드에서 사용자 차트를 클릭 합니다.](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>어떤 브라우저 또는 운영 체제를 사용하나요?

사용자 차트에서 **편집**을 클릭하고 브라우저, 운영 체제 또는 시/군/구와 같은 속성으로 데이터를 그룹화(구분)합니다. 

![단일 메트릭을 보여주는 차트를 선택하고 그룹화로 전환한 다음 속성 선택](./media/app-insights-web-track-usage/03-browsers.png)

전체 개수를 확인하려면 차트 종류를 **표 형태**로 전환합니다. 추가 메트릭을 표시하도록 선택할 수도 있습니다.

![여러 열 표 형태 차트](./media/app-insights-web-track-usage/multi-column-grid.png)

그래픽 차트 종류의 경우 속성으로 그룹화나 여러 메트릭 선택 중에서 하나만 선택할 수 있습니다. 이 차트에서는 사용자와 [인증된 사용자](app-insights-api-custom-events-metrics.md#authenticated-users)라는 두 메트릭을 비교합니다. 

![차트를 선택하고 메트릭을 검색하여 선택 또는 선택 해제합니다.](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>페이지 보기
사용 현황 블레이드에서 가장 인기 있는 페이지의 분석과 함께 보다 자세한 버전을 가져오려면 페이지 보기 차트를 클릭합니다.

![개요 블레이드에서 페이지 보기 차트 클릭](./media/app-insights-web-track-usage/05-games.png)

위의 예제는 게임 웹 사이트에서 가져온 것입니다. 차트에서 바로 다음을 확인할 수 있습니다.

* 사용량은 지난 주보다 개선되지 않았습니다. 검색 엔진 최적화에 대해 생각해볼 수 있을까요?
* 테니스가 가장 인기 있는 게임 페이지입니다. 이 페이지의 향상된 기능을 집중적으로 살펴보겠습니다.
* 평균적으로 사용자는 주당 약 세 번 테니스 페이지를 방문합니다. 사용자보다 세션이 약 3배 더 많습니다.
* 대부분의 사용자는 미국 근무 주간 및 근무 시간에 사이트를 방문합니다. 웹 페이지에 “빠른 숨기기” 단추를 제공해야 할 것 같습니다.
* 차트의 [주석](app-insights-annotations.md)은 웹 사이트의 새 버전이 배포된 경우에 표시됩니다. 최근 배포는 사용 현황에 크게 영향을 미치지 않습니다.

## <a name="custom-tracking"></a>사용자 지정 추적
별도의 웹 페이지에서 각각의 게임을 구현하는 대신, 웹 페이지에서 대부분 Javascript로 코딩된 기능을 사용하여 단일 페이지 앱으로 리펙터링하기로 결정한다고 가정해 봅니다. 이를 통해 사용자는 한 게임에서 다른 게임으로 빠르게 전환할 수 있으며 한 페이지에서 여러 게임도 할 수 있습니다.

하지만 별도의 웹 페이지에서 게임할 때와 완전히 동일한 방법으로 각각의 게임이 열리는 횟수를 로그하기 위해 여전히 Application Insights를 사용하고자 합니다. 간단합니다. 열리는 새 '페이지'를 기록하려는 JavaScript에 원격 분석 모듈에 대한 호출을 삽입하면 됩니다.

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
이 호출은 페이지 보기를 기록하는 원격 분석을 시뮬레이트합니다.  하지만 페이지 보기가 포함된 메시지를 항상 혼합하고 싶지는 않습니다. 대신, 사용자 지정 이벤트를 사용합니다. 이러한 이벤트는 웹 페이지 또는 웹 서버에서 전송할 수 있습니다.


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

[웹 또는 서버 코드에 삽입된 사용자 지정 원격 분석](app-insights-api-custom-events-metrics.md#trackevent)은 응용 프로그램이 사용되는 방식을 여러 측면에서 이해하는 데 사용할 수 있습니다.

TrackEvent()에서 보낸 이벤트를 보려면 메트릭 탐색기를 열고 새 차트를 추가한 다음 편집합니다. 메트릭은 사용자 지정 메트릭 아래에 나타납니다. 

![사용자 지정 이벤트를 보여 주는 차트](./media/app-insights-web-track-usage/06-eventsSegment.png)

이벤트에서 [속성 값](app-insights-api-custom-events-metrics.md#properties)(차원이라고도 함)을 설정한 경우 그에 따라 그룹화하고 필터링할 수 있습니다.

다른 메트릭 및 이벤트의 변경 내용을 상호 연결하려면 여러 차트를 만듭니다. 예를들어 더 많은 게임이 플레이되면 중단된 게임에도 상승 효과가 보이기를 기대하게 됩니다. 하지만 중단된 게임의 상승 효과는 균형이 맞지 않습니다. 높은 부하가 사용자가 받아들일 수 없는 문제를 유발하는지 알고 싶습니다.

## <a name="drill-into-specific-events"></a>특정 이벤트 자세히 알아보기
일반적인 세션이 진행되는 방식에 대해 더 잘 이해하기 위해 특정 이벤트 종류를 포함하는 특정 사용자 세션에 초점을 맞추고자할 수 있습니다.

이벤트 표에서 관심 있는 이벤트를 클릭하고 최근에 발생한 특정 항목을 선택합니다.

![요약 차트 아래의 목록에서 이벤트를 클릭합니다.](./media/app-insights-web-track-usage/08-searchEvents.png)

해당하는 특정 NoGame 이벤트가 발생한 세션에 대한 모든 원격 분석을 살펴봅시다.

!['세션에 대한 모든 원격 분석'을 클릭합니다.](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

예외 사항이 없었으므로 사용자는 일부 오류로 인해 플레이하지 못한 것입니다.

이 세션에 대한 페이지 보기를 제외한 모든 종류의 원격 분석을 필터링할 수 있습니다.

![](./media/app-insights-web-track-usage/10-filter.png)

그리고 이제 단순히 최신 점수를 확인하기 위해 이 사용자가 로그인한 것을 확인할 수 있습니다. 이를 더 쉽게 하기 위해 사용자 스토리 개발을 고려해야 할 수도 있습니다. (그리고 이 특정 스토리가 발생할 때 보고할 사용자 지정 이벤트를 구현해야 합니다.)

## <a name="filter-search-and-segment-your-data-with-properties"></a>속성을 사용하여 데이터를 필터링, 검색 및 분할
이벤트에 임의의 태그 및 숫자 값을 연결할 수 있습니다.

*웹 페이지의 JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*서버의 C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*서버의 VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

동일한 방식으로 페이지 뷰에 속성을 연결합니다.

*웹 페이지의 JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

진단 검색에서 이벤트의 개별 항목을 클릭하여 속성을 봅니다.

![더 많은 속성을 보려면 이벤트 목록에서 이벤트를 열고 '...'를 클릭합니다.](./media/app-insights-web-track-usage/11-details.png)

검색 필드를 사용하여 특정 속성 값이 포함된 이벤트 항목을 볼 수 있습니다.

![검색 필드에 값 입력](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>원격 분석을 통해 쿼리 편집 및 만들기

원하는 차트에서 Azure 분석 아이콘을 클릭하여 편집할 수 있는 동등한 쿼리를 엽니다.
아래로 스크롤하여 생성된 쿼리가 두 개 이상 있는지 확인합니다. 쿼리에 커서를 놓고 **이동**을 클릭합니다. 

또는 개요 블레이드의 아이콘에서 분석을 열고 고유한 쿼리를 작성하거나, 분석 홈 탭에서 몇 가지 샘플 쿼리를 사용해 봅니다.


![생성된 쿼리가 있는 분석 창](./media/app-insights-web-track-usage/open-analytics.png)

[Azure 분석 쿼리 언어에 대해 자세히 알아봅니다](app-insights-analytics.md).

사용 현황 분석을 위해 다음과 같은 테이블에 특히 관심이 있을 수 있습니다.

* `customEvents` - [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 호출의 결과입니다.
* `pageViews` - 클라이언트 브라우저에서 연 페이지 수 또는 [trackPageView()](app-insights-api-custom-events-metrics.md#page-views)에 대한 호출입니다.


## <a name="a--b-testing"></a>A | B 테스트
기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 태그를 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 응용 프로그램이 전송하는 모든 원격 분석 메시지에 추가됩니다.

그런 다음 Application Insights 포털에서 태그로 데이터를 필터링 및 그룹화(구분)할 수 있게 되므로 다른 버전과 비교할 수 있습니다.


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

개별 원격 분석에서 기본값을 재정의할 수 있습니다.

유니버설 아니셜라이저를 설정하여 새로운 모든 TelemetryClients는 사용자 컨텍스트를 자동으로 사용할 수 있습니다.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Global.asax.cs 같은 앱 이니셜라이저에서:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>빌드 - 측정 - 학습
분석을 사용하면 문제를 해결하기 위해 생각한 무언가가 아닌 개발 주기의 통합 부분이 됩니다. 다음은 몇 가지 팁입니다.

* 응용 프로그램의 주요 메트릭을 결정합니다. 가능한 많은 사용자를 원하시나요? 아니면 매우 만족하는 소규모의 사용자를 원하시나요? 방문수 또는 매출을 최대화하겠습니까?
* 각 스토리를 측정하도록 계획합니다. 새 사용자 스토리 또는 기능을 스케치하거나 기존 내용을 업데이트할 계획을 세우면 항상 변화의 성공을 어떻게 측정할지 생각해 봅니다. 코딩을 시작하기 전에 "작동한다면 우리 메트릭에 어떠한 영향을 미칠까? 새 이벤트를 추적해야 하는가?"라고 물어봅니다.
  물론, 기능이 실시간이면 분석 내용을 자세히 보고 결과에 대한 조치를 취해야 합니다.
* 기타 메트릭을 주요 메트릭과 연결합니다. 예를들어 '즐겨찾기' 기능을 추가하는 경우 사용자가 즐겨찾기를 추가하는 빈도를 알고 싶습니다. 하지만 사용자가 즐겨찾기를 다시 방문하는 빈도를 아는 데에 더 많은 관심이 있을 수 있습니다. 또한 가장 중요한 것은 궁극적으로 즐겨찾기를 사용하는 고객이 사용자의 제품을 더 구입하느냐입니다.
* 카나리아 테스트입니다. 새로운 기능을 일부 사용자에게만 표시할 수 있는 기능 스위치를 설정합니다. Application Insights를 사용하여 새로운 기능이 예상하는 방식으로 사용되고 있는지 확인합니다. 조정한 다음 더 광범위한 대상에게 이를 릴리스합니다.
* 사용자에게 이야기하세요! 분석은 자체적으로 충분하지 않지만 좋은 고객 관계 유지를 보완합니다.

## <a name="learn-more"></a>자세한 정보
* [앱의 충돌과 성능 문제를 감지, 분류 및 진단](app-insights-detect-triage-diagnose.md)
* [많은 플랫폼에서 Application Insights 시작](app-insights-detect-triage-diagnose.md)
* [API 사용 - 개요](app-insights-api-custom-events-metrics.md)
* [JavaScript API 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



