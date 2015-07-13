<properties 
	pageTitle="Application Insights SDK를 추가하여 ASP.NET 앱 모니터링" 
	description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다." 
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
	ms.date="05/21/2015" 
	ms.author="awills"/>


# Application Insights SDK를 추가하여 ASP.NET 앱 모니터링

*Application Insights는 미리 보기 상태입니다.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Visual Studio Application Insights는 실시간 응용 프로그램을 모니터링하여 [성능 문제 및 예외 사항을 감지 및 진단][detect]하고 [앱이 어떻게 사용되는지 검색][knowUsers]할 수 있도록 돕습니다. 다양한 응용 프로그램 종류에서 사용할 수 있습니다. Azure 웹앱뿐 아니라 온-프레미스 IIS 서버 또는 Azure VM에서 호스트된 앱에서도 작동합니다. [장치 앱 및 Java 서버에 대해서도 다룹니다][start].

![예제 성능 모니터링 차트](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

많은 응용 프로그램의 경우 대부분 공지 없이 [Visual Studio가 앱에 Application Insights를 추가할 수 있습니다](#ide). 하지만 무슨 일인지 더 잘 이해하기 위해 본 문서를 읽어 보면 수동으로 단계가 안내됩니다.

#### 시작하기 전에

다음 작업을 수행해야 합니다.

* [Microsoft Azure](http://azure.com) 구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](http://live.com)을 사용하여 사용자를 추가할 수 있습니다.
* Visual Studio 2013 이상.

## <a name="add"></a> 1. Application Insights 리소스 만들기

[Azure 포털][portal]에 로그인한 다음 새 Application Insights 리소스를 만듭니다. 응용 프로그램 종류로 ASP.NET을 선택합니다.

![새로 만들기, Application Insights 클릭](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

Azure에서 [리소스][roles]는 서비스의 인스턴스입니다. 이 리소스는 사용자에게 분석 및 제공되는 앱의 원격 분석을 하는 곳입니다.

선택하는 응용 프로그램 종류에 따라 [메트릭 탐색기][metrics]에 표시되는 리소스 블레이드 및 속성의 기본 콘텐츠가 설정됩니다.

####  계측 키를 복사합니다.

키는 리소스를 식별하며, 데이터를 리소스로 보내기 위해 SDK에서 곧 설치합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)



## <a name="sdk"></a> 2. 응용 프로그램에 SDK 설치


1. Visual Studio에서 데스크톱 앱 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. 웹앱용 Application Insights SDK를 설치합니다.

    !["Application Insights" 검색](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)


3. ApplicationInsights.config(NuGet 설치로 추가됨)를 편집합니다. 닫는 태그 바로 전에 삽입합니다.

    `<InstrumentationKey>` *복사한 계측 키* `</InstrumentationKey>`

    또는 앱에서 [일부 코드를 작성하여 키를 설정][apikey]할 수 있습니다.

#### SDK의 나중 버전으로 업그레이드하려면

[SDK의 새 릴리스](app-insights-release-notes-dotnet.md)로 업그레이드하려면, NuGet 패키지 관리자를 다시 열고 설치된 패키지를 필터링합니다. Microsoft.ApplicationInsights.Web을 선택하고 업그레이드를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 변경한 경우, 업그레이드 전에 복사본을 저장하고 나중에 변경 내용을 새 버전에 병합합니다.


## <a name="run"></a> 3. 프로젝트 실행

F5 키를 사용하여 응용 프로그램을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 전송한 이벤트 수가 표시됩니다.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. 원격 분석 보기

[Azure 포털][portal]로 돌아가 Application Insights 리소스를 찾습니다.


개요 차트에서 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다. [메트릭에 대해 자세히 알아봅니다.][perf]

이제 응용 프로그램을 배포하고 누적되는 데이터를 관찰합니다.


디버그 모드에서 실행할 때는 파이프라인을 통해 원격 분석이 신속하게 수행되므로 데이터가 몇 초 내에 표시됩니다. 앱을 배포할 때는 데이터가 더 천천히 누적됩니다.

#### 데이터가 없나요?

* [검색][diagnostic] 타일을 열고 개별 이벤트를 봅니다.
* 응용 프로그램을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* 몇 초 정도 기다렸다가 새로고침을 클릭합니다.
* [문제 해결][qna]을 참조하세요.

#### 빌드 서버에 문제가 있나요?

[이 문제 해결 항목](app-insights-troubleshoot-faq.md#NuGetBuild)을 참조하세요.


## 설치 완료

응용 프로그램의 모든 부분을 완벽하게 살펴보려면 몇 가지 할 일이 더 있습니다.


* [JavaScript SDK를 웹 페이지에 추가][client]하여 사용자, 세션 및 페이지 보기 수, 페이지 로드 시간, 스크립트 예외 사항과 같은 브라우저 기반 원격 분석을 가져오고 페이지 스크립트에서 사용자 지정 원격 분석을 작성합니다.
* 종속성 추적을 추가하여 데이터베이스 또는 앱에서 사용하는 다른 구성 요소로 인한 문제를 진단합니다.
 * [Azure 웹앱 또는 VM에서][azure]
 * [온-프레미스 IIS 서버에서][redfield]
* 즐겨찾는 로깅 프레임워크에서 [로그 추적 캡처][netlogs]
* [사용자 지정 이벤트 및 메트릭을 추적][api]하여 클라이언트나 서버 또는 둘 다에서 응용 프로그램이 어떻게 사용되는지 알아볼 수 있습니다.
* [웹 테스트를 설정][availability]하여 응용 프로그램이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.

## <a name="ide"> </a>자동화된 방법

Visual Studio를 사용하여 설정 작업을 하려는 경우 작업이 매우 간단합니다.

[Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) 이상 및 [Microsoft Azure](http://azure.com) 계정이 필요합니다.

#### 새 프로젝트의 경우

Visual Studio에서 새 프로젝트를 만들 때 Application Insights를 선택해야 합니다.


![ASP.NET 프로젝트 만들기](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

Visual Studio는 Application Insights에서 리소스를 만들고, SDK를 프로젝트에 추가하고, `.config` 파일에 키를 배치합니다.

프로젝트에 웹 페이지가 있는 경우에도 [JavaScript SDK][client]를 마스터 웹 페이지에 추가합니다.

#### ...기존 프로젝트의 경우

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.

![Application Insights 추가 선택](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

Visual Studio는 Application Insights에서 리소스를 만들고, SDK를 프로젝트에 추가하고, `.config` 파일에 키를 배치합니다.

이 경우에 [JavaScript SDK][client]는 웹 페이지에 추가하지 않습니다. 이는 다음 단계에서 수행하는 것이 좋습니다.

#### 설치 옵션

프로젝트를 처음 만드는 경우 Microsoft Azure 미리 보기에 로그인하거나 등록하라는 메시지가 표시됩니다. 해당 계정은 Visual Studio Online 계정과는 별개입니다.

이 앱이 더 큰 응용 프로그램의 일부인 경우, **구성 설정**을 사용하여 다른 구성 요소와 동일한 리스소 그룹에 넣고자 할 수 있습니다.

*Application Insights 옵션이 표시되지 않는 경우 Visual Studio 2013 업데이트 3 이상을 사용 중이고, 확장 및 업데이트에서 Application Insights Tools를 사용하도록 설정되어 있으며, 웹 프로젝트를 만들고 있는지 확인합니다.*

#### 프로젝트에서 Application Insights를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)




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
[start]: app-insights-get-started.md

 

<!---HONumber=62-->