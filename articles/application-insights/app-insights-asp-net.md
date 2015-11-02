<properties 
	pageTitle="ASP.NET용 Application Insights" 
	description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 성능, 가용성 및 사용 패턴을 분석합니다." 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# ASP.NET용 Application Insights 설치

*Application Insights는 미리 보기 상태입니다.*

<a name="selector1"></a>

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights)는 실시간 응용 프로그램을 모니터링하여 [성능 문제 및 예외 사항을 감지 및 진단][detect]하고 [앱이 어떻게 사용되는지 검색][knowUsers]할 수 있도록 돕습니다. SDK를 앱에 설치하면 작동합니다. SDK는 앱에 대한 원격 분석을 Application Insights 서비스에 보내 앱의 동작을 분석하고 시각화합니다.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Visual Studio에서 앱에 SDK를 추가하면 서버 요청, 응답 시간 및 실패에 대한 차트를 얻게 됩니다.

![예제 성능 모니터링 차트](./media/app-insights-asp-net/10-perf.png)

또한 API를 사용하여 사용 현황을 자세히 모니터링할 수 있습니다.

#### 시작하기 전에

다음 작업을 수행해야 합니다.

* [Microsoft Azure](http://azure.com) 구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](http://live.com)을 사용하여 사용자를 추가할 수 있습니다.
* Visual Studio 2013 업데이트 3 이상

## <a name="ide"></a> Visual Studio의 프로젝트에 Application Insights 추가

#### 새 프로젝트의 경우

Visual Studio에서 새 프로젝트를 만들 때 Application Insights를 선택해야 합니다.


![ASP.NET 프로젝트 만들기](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ...기존 프로젝트의 경우

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.

![Application Insights 추가 선택](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### 설치 옵션

처음 연 경우 Microsoft Azure에 로그인하거나 등록하라는 메시지가 표시됩니다.

이 앱이 더 큰 응용 프로그램의 일부인 경우, **구성 설정**을 사용하여 다른 구성 요소와 동일한 리스소 그룹에 넣고자 할 수 있습니다.


####<a name="land"></a> ‘Application Insights 추가'에서 무엇을 했나요?

명령은 다음 단계를 수행했습니다(원하는 경우 [수동으로 수행](app-insights-start-monitoring-app-health-usage.md) 가능).

* [Azure 포털][portal]에 Application Insights 리소스를 만듭니다. 이곳에서 사용자의 데이터를 볼 수 있습니다. 리소스를 식별하는 *계측 키*를 검색합니다.
* 프로젝트에 Application Insights 웹 SDK NuGet 패키지를 추가합니다. Visual Studio에서 보려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
* `ApplicationInsights.config`에 계측 키를 배치합니다.


## <a name="run"></a> 프로젝트 실행

F5 키를 사용하여 응용 프로그램을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 전송한 이벤트 수가 표시됩니다.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Application Insights 열기

[Azure 포털][portal]에서 Application Insights 리소스를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### 메트릭: 집계된 데이터

개요 차트에서 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-asp-net/12-first-perf.png)

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다. [메트릭에 대해 자세히 알아봅니다.][perf]

* *사용자 또는 페이지 데이터가 없나요?* - [사용자 및 페이지 데이터 추가](../article/application-insights/app-insights-asp-net-client.md)

### 검색: 개별 이벤트

검색을 열어 개별 요청 및 관련된 이벤트를 조사합니다.

![](./media/app-insights-asp-net/21-search.png)

[검색에 대한 자세한 정보](app-insights-diagnostic-search.md)

* *연결된 이벤트가 없나요?* [서버 예외](../article/application-insights/app-insights-asp-net-exception-mvc.md) 및 [종속성](../article/application-insights/app-insights-asp-net-dependencies.md)을 설정합니다.

### 데이터가 없나요?

* 올바른 작업을 보고 있는지 확인하세요. [Azure 포털](https://portal.azure.com)에 로그인하고 "찾아보기 >", "Application Insights"를 클릭한 다음 앱을 선택합니다.
* 응용 프로그램을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* [검색][diagnostic] 블레이드를 열고 개별 이벤트를 봅니다. 경우에 따라 이벤트가 메트릭 파이프라인을 통해 들어오려면 시간이 약간 더 걸립니다.
* 몇 초 정도 기다렸다가 새로고침을 클릭합니다.
* [문제 해결][qna]을 참조하세요.


## 응용 프로그램 게시

이제 응용 프로그램을 배포하고 누적되는 데이터를 관찰합니다.

디버그 모드에서 실행할 때는 파이프라인을 통해 원격 분석이 신속하게 수행되므로 데이터가 몇 초 내에 표시됩니다. 앱을 배포할 때는 데이터가 더 천천히 누적됩니다.

#### 빌드 서버에 문제가 있나요?

[이 문제 해결 항목](app-insights-troubleshoot-faq.md#NuGetBuild)을 참조하세요.

## 다음 단계

- [사용자 및 페이지 데이터](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [예외](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [종속성](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## SDK의 나중 버전으로 업그레이드하려면

[SDK의 새 릴리스](app-insights-release-notes-dotnet.md)로 업그레이드하려면, NuGet 패키지 관리자를 다시 열고 설치된 패키지를 필터링합니다. Microsoft.ApplicationInsights.Web을 선택하고 업그레이드를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 변경한 경우, 업그레이드 전에 복사본을 저장하고 나중에 변경 내용을 새 버전에 병합합니다.



## <a name="video"></a>동영상

> [AZURE.VIDEO getting-started-with-application-insights]


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
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->