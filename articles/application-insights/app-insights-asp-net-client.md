<properties 
	pageTitle="Application Insights에서 종속성 추적" 
	description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Application Insights 설정: 웹 페이지


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


웹 페이지의 성능 및 사용 현황에 대해 알아봅니다. Visual Studio Application Insights를 페이지에 추가하면 사용자가 몇 명인지, 몇 명이 다시 방문하는지, 어떤 페이지를 가장 많이 사용하는지 확인할 수 있습니다. 또한 로드 시간 및 모든 예외에 대한 보고서를 얻을 수 있습니다. 몇 가지 [사용자 지정 이벤트 및 메트릭][api]을 추가하고 가장 인기 있는 기능, 가장 일반적인 실수를 자세히 분석하고 사용자와 더불어 성공적인 결과를 얻을 수 있게 페이지를 조정합니다.

![새로 만들기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-asp-net-client/16-page-views.png)

[ASP.NET](app-insights-asp-net.md) 또는 [Java](app-insights-java-get-started.md) 웹 앱에 대해 이미 서버 원격 분석을 설정한 경우 클라이언트와 서버에 대한 정보를 모두 얻을 수 있습니다. 두 스트림은 Application Insights 포털에 통합될 것입니다.

## Application Insights 리소스 열기

[Azure 포털](http://portal.azure.com)에 로그인합니다.

응용 프로그램의 서버측에 대한 모니터링을 이미 설정한 경우 리소스가 있습니다.

![찾아보기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-asp-net-client/01-find.png)

계정이 없는 경우 계정을 만듭니다.

![새로 만들기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-asp-net-client/01-create.png)


## 앱 또는 웹 페이지에 SDK 스크립트를 추가합니다.

빠른 시작에서 웹 페이지용 스크립트를 가져옵니다.

![앱 개요 블레이드에서 빠른 시작, 내 웹 페이지를 모니터링할 코드 가져오기를 선택합니다. 스크립트를 복사합니다.](./media/app-insights-asp-net-client/02-monitor-web-page.png)

추적하려는 모든 페이지의 &lt;/head&gt; 태그 바로 앞에 스크립트를 삽입합니다. 웹 사이트에 마스터 페이지가 있는 경우 이 페이지에 스크립트를 넣을 수 있습니다. 예:

* ASP.NET MVC 프로젝트에서는 View\\Shared\\_Layout.cshtml에 추가합니다.
* SharePoint 사이트의 경우 제어판에서 [사이트 설정/마스터 페이지](app-insights-sharepoint.md)를 엽니다.

스크립트에는 Application Insights 리소스에 데이터를 전달하는 계측 키가 포함됩니다.

*(잘 알려진 웹 페이지 프레임워크를 사용하는 경우 Application Insights 어댑터를 찾아보세요. 예를 들어 [AngularJS 모듈](http://ngmodules.org/modules/angular-appinsights)이 있습니다.)*


## <a name="run"></a>앱 실행

웹앱을 실행하고, 잠깐 사용하여 원격 분석을 생성하고, 잠시 기다립니다. 개발 컴퓨터에서 **F5** 키를 사용하여 실행하거나 사용자가 실행할 수 있도록 게시할 수 있습니다.

웹앱에서 Application Insights로 보내는 원격 분석을 확인하려면 브라우저의 디버깅 도구(대부분의 브라우저는 **F12** 키)를 사용합니다. 데이터가 dc.services.visualstudio.com으로 전송됩니다.

## 데이터 탐색

응용 프로그램 개요 블레이드의 상단 근처에는 페이지를 브라우저에 로드하는 평균 시간을 보여주는 차트가 있습니다.


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*아직 아무 데이터도 없나요? 페이지 위쪽에서 **새로 고침**을 클릭합니다. 여전히 아무 데이터도 없나요? [문제 해결][qna]을 참조하세요.*

해당 차트를 클릭하면 보다 자세한 버전을 가져올 수 있습니다.

![](./media/app-insights-asp-net-client/07-client-perf.png)

총 페이지 로드 시간을 [W3C에서 정의한 표준 타이밍](http://www.w3.org/TR/navigation-timing/#processing-model)으로 분류하는 누적 차트입니다.

![](./media/app-insights-asp-net-client/08-client-split.png)

*네트워크 연결* 시간은 일반적으로 예상보다 짧습니다. 브라우저에서 서버로 보내는 모든 요청의 평균값이기 때문입니다. 서버에 대한 활성 연결이 이미 있기 때문에 연결 시간이 0인 개별 요청이 많습니다.


### 페이지별 성능

세부 정보 블레이드의 더 아래쪽에는 페이지 URL에 따라 분류된 표가 있습니다.


![](./media/app-insights-asp-net-client/09-page-perf.png)

시간에 따른 페이지 성능 변화를 보려면 표를 두 번 클릭하고 차트 유형을 변경합니다.

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## 클라이언트 사용 개요

다시 개요 블레이드에서 **사용**을 클릭합니다.

![](./media/app-insights-asp-net-client/14-usage.png)

* **사용자:** 차트의 시간 범위에 따른 고유한 사용자 수입니다. 쿠키는 다시 방문하는 사용자를 식별하는 데 사용됩니다.
* **세션:** 사용자가 30분 동안 아무 요청도 하지 않으면 세션이 계산됩니다.
* **페이지 보기** trackPageView() 호출 수를 계산합니다. 일반적으로 각 웹 페이지에서 한 번 호출됩니다.


### 클릭하여 추가 데이터 확인

보다 자세한 정보를 확인하려면 차트를 클릭합니다. 차트의 시간 범위를 변경할 수 있습니다.

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


차트를 클릭하여 표시할 수 있는 다른 메트릭을 확인하거나 새 차트를 추가하고 표시되는 메트릭을 선택합니다.

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]메트릭이 몇 가지 조합으로만 표시될 수 있습니다. 메트릭을 하나 선택하면 호환되지 않는 다른 메트릭을 사용할 수 없게 됩니다.



## 사용자 지정 페이지 수

기본적으로는 새 페이지를 클라이언트 브라우저로 로드할 때마다 페이지 수가 계산됩니다. 그러나 추가 페이지 보기를 계산에 포함할 수도 있습니다. 예를 들어 탭에 콘텐츠가 표시되는 페이지에서 사용자가 탭을 전환할 때 페이지 수를 계산하도록 지정할 수 있습니다. 또는 페이지의 JavaScript 코드가 브라우저 URL은 변경하지 않고 새 콘텐츠를 로드할 수도 있습니다.

클라이언트 코드의 적절한 지점에 다음과 같은 JavaScript 호출을 삽입합니다.

    appInsights.trackPageView(myPageName);

페이지 이름은 URL과 같은 문자를 포함할 수 있지만 "#" 또는 "?" 뒤의 모든 문자는 무시됩니다.


## 개별 페이지 보기 이벤트 검사

일반적으로 페이지 보기 원격 분석은 Application Insights에서 분석하며 모든 사용자에 대해 계산된 평균이 포함된 누적 보고서만 표시됩니다. 그러나 디버깅을 위해 개별 페이지 보기 이벤트를 확인할 수도 있습니다.

진단 검색 블레이드에서 필터를 페이지 보기로 설정합니다.

![](./media/app-insights-asp-net-client/12-search-pages.png)

보다 자세한 정보를 확인하려면 원하는 이벤트를 선택합니다. 세부 정보 페이지에서 더 자세한 정보를 보려면 "..."를 클릭합니다.

> [AZURE.NOTE] [검색][diagnostic]을 사용하는 경우 전체 단어가 일치해야 합니다. "Abou"와 "bout"은 "About"과 일치하지 않지만 "Abou*"는 일치합니다. 또한 검색 용어는 와일드로 시작할 수 없습니다. 예를 들어 "*bou"로 검색해도 "About"는 검색되지 않습니다.

> [진단 검색에 대해 자세히 알아보기][diagnostic]

### 페이지 보기 속성

* **페이지 보기 기간**&#151;페이지를 로드하고 스크립트 실행을 시작하는 데 걸리는 시간입니다. 특히, 시작 페이지와 트랙 페이지 보기의 실행을 로드하는 사이의 간격입니다. 스크립트의 초기화 후 일반적인 위치에서 trackPageView를 이동한 경우 다른 값이 반영 됩니다.

## 사용자 지정 사용 추적

사용자가 앱으로 어떤 작업을 수행하려고 하는지 확인하고 싶나요? 클라이언트 및 서버 코드에 호출을 삽입하여 사용자 고유의 원격 분석을 Application Insights로 보낼 수 있습니다. 예를 들어 주문은 작성했으나 완료하지 않은 사용자의 수, 가장 자주 발생하는 유효성 검사 오류 또는 게임의 평균 점수를 확인할 수 있습니다.

* [사용자 지정 이벤트 및 메트릭 API에 대해 자세히 알아보세요][api].
* [API 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a> 비디오: 사용 현황 추적

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 다음 단계

- [SDK 설치](../article/application-insights/app-insights-asp-net.md#selector1)
- [사용자 지정 이벤트 및 메트릭으로 사용량 추적(영문)][api]
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [이미 라이브 앱](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


 

<!---HONumber=Nov15_HO4-->