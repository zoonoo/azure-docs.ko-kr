<properties 
	pageTitle="Application Insights로 ASP.NET에 대한 웹앱 분석" 
	description="ASP.NET 웹 사이트, 호스팅된 온-프레미스 또는 Azure에 대한 성능, 가용성 및 사용 현황 분석입니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/12/2016" 
	ms.author="awills"/>


# ASP.NET용 Application Insights 설치


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK는 라이브 웹 응용 프로그램에서 로그인하고 앱의 성능 및 사용에 대한 차트를 볼 수 있는 Azure 포털로 분석 원격 분석을 보냅니다.

![예제 성능 모니터링 차트](./media/app-insights-asp-net/10-perf.png)

또한 특정 요청, 예외 및 이벤트 로그를 조사하고 상호 연결할 수 있습니다. API를 사용하여 성능 및 사용 현황을 자세히 모니터링하는 원격 분석을 추가할 수 있습니다.

#### 시작하기 전에

다음 작업을 수행해야 합니다.

* [Microsoft Azure](http://azure.com) 구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](http://live.com)을 사용하여 사용자를 추가할 수 있습니다.
* Visual Studio 2013 업데이트 3 이상

## <a name="ide"></a> Visual Studio의 프로젝트에 Application Insights 추가

#### 새 프로젝트의 경우

Visual Studio에서 새 프로젝트를 만들 때 Application Insights를 선택해야 합니다.


![ASP.NET 프로젝트 만들기](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Azure 로그인으로 계정을 선택합니다. 자격 증명을 다시 입력하도록 초대될 수 있습니다. (또는 경우 로그인하지 않은 경우 SDK의 코드를 추가하고 나중에 구성할 수 있습니다.)


#### ...기존 프로젝트의 경우

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 추가** 또는 **Application Insights 구성**을 선택합니다.

![Application Insights 추가 선택](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### 설치 옵션

프로젝트를 처음 만드는 경우 Microsoft Azure에 로그인하거나 등록하도록 초대됩니다.

이 앱이 더 큰 응용 프로그램의 일부인 경우, **구성 설정**을 사용하여 다른 구성 요소와 동일한 리스소 그룹에 넣고자 할 수 있습니다.


####<a name="land"></a> ‘Application Insights 추가'에서 무엇을 했나요?

명령은 다음 단계를 수행했습니다(원하는 경우 [수동으로 수행](app-insights-start-monitoring-app-health-usage.md) 가능).

1. 프로젝트에 Application Insights 웹 SDK NuGet 패키지를 추가합니다. Visual Studio에서 보려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
2. [Azure 포털][portal]에 Application Insights 리소스를 만듭니다. 이곳에서 사용자의 데이터를 볼 수 있습니다. 리소스를 식별하는 *계측 키*를 검색합니다.
3. `ApplicationInsights.config`에 계측 키를 삽입하여 SDK가 포털에 원격 분석을 보낼 수 있습니다.

처음 Azure에 로그인하지 않은 경우 SDK는 리소스에 연결하지 않고 설치됩니다. 디버깅하는 동안 Visual Studio 검색 창에서 Application Insights 원격 분석을 확인하고 검색할 수 있습니다. 나중에 다른 단계를 완료할 수 있습니다.

## <a name="run"></a> 프로젝트 디버깅

F5 키를 사용하여 응용 프로그램을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 로그된 이벤트 수가 표시됩니다.

![Visual Studio에서 Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/app-insights-asp-net/appinsights-09eventcount.png)

이 단추를 클릭하여 진단 검색을 엽니다.



## 원격 분석 디버깅

### 진단 허브

진단 허브(Visual Studio 2015 이상)는 Application Insights 서버 원격 분석을 생성되는 대로 보여줍니다. Azure 포털에서 리소스에 연결하지 않고 SDK를 설치하도록 선택한 경우 작동합니다.

![진단 도구 창을 열고 Application Insights 이벤트를 검사합니다.](./media/app-insights-asp-net/31.png)


### 진단 검색

검색 창에서는 기록된 이벤트를 보여줍니다. (Azure에 로그인한 경우 Application Insights를 설정할 때 포털에서 동일한 이벤트를 검색할 수 있습니다.)

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 및 검색을 선택합니다.](./media/app-insights-asp-net/34.png)

자유 텍스트 검색은 이벤트의 필드에서 작동합니다. 예를 들어 페이지의 URL의 일부 또는 클라이언트 시티와 같은 속성의 값 또는 추적 로그에서 특정 단어를 검색합니다.

또한 관련된 항목 탭을 열어 실패한 요청 또는 예외를 진단할 수 있습니다.


![](./media/app-insights-asp-net/41.png)



### 예외

[예외 모니터링을 설정](app-insights-asp-net-exceptions.md)한 경우 예외 보고서는 검색 창에 표시됩니다.

예외를 클릭하여 스택 추적을 가져옵니다. 앱의 코드가 Visual Studio에서 열린 경우 스택 추적에서 코드의 관련된 줄까지 클릭할 수 있습니다.




### 로컬 모니터링



(Visual Studio 2015 업데이트 2에서) Application Insights 포털에 원격 분석을 보내도록 SDK를 구성하지 않은 경우(따라서 ApplicationInsights.config에 계측 키가 없음) 최신 디버깅 세션의 원격 분석이 진단 창에 표시됩니다.

이전 버전의 앱을 이미 게시한 경우에 바람직합니다. 게시된 앱의 Application Insights 포털에서 원격 분석과 디버깅 세션의 원격 분석을 혼합하려 하지 않습니다.

포털에 원격 분석을 보내기 전에 디버깅하려는 [사용자 지정 원격 분석](app-insights-api-custom-events-metrics.md)이 있는 경우에도 유용합니다.


* *우선 Application Insights를 완전히 구성하여 포털에 원격 분석을 전송했습니다. 하지만 이제 Visual Studio에서만 원격 분석을 확인하려 합니다.*

 * 검색 창 설정에서 앱이 포털에 원격 분석을 전송하는 경우 로컬 진단을 검색하는 옵션이 있습니다.
 * 포털에 전송되는 원격 분석을 중지하려면 ApplicationInsights.config에서 `<instrumentationkey>...` 줄을 주석으로 처리합니다. 원격 분석을 포털에 다시 보낼 준비가 되면 주석 처리를 제거합니다.



## <a name="monitor"></a> Application Insights 포털에서 원격 분석 보기

응용 프로그램을 게시하면 Application Insights 포털에서 원격 분석을 확인할 수 있으며 디버깅하는 동안 원격 분석을 올바르게 전송했는지 확인할 수 있습니다.

[Azure 포털][portal]에서 Application Insights 리소스를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

이 앱에 Application Ingsights를 추가했을 때 Azure에 로그인하지 않은 경우 지금 로그인합니다. **Application Insights 구성**을 선택합니다. 그렇게 하면 배포한 후에 라이브 앱에서 원격 분석을 계속 볼 수 있습니다. 원격 분석은 Application Insights 포털에 표시됩니다.

### 라이브 스트림

디버깅 시 또는 배포 직후에 원격 분석 데이터의 빠른 보기는 라이브 스트림을 사용합니다.

![개요 블레이드에서 라이브 스트림 클릭](./media/app-insights-asp-net/45.png)


배포 직후에 앱이 작동하는지 확인할 수 있도록 라이브 스트림을 설계했습니다.

라이브 스트림은 지난 몇 분간의 데이터만 보여주고 어떠한 데이터도 보존하지 않습니다.

여기에는 SDK 2.1.0-beta1 이상의 버전이 필요합니다.



### 검색: 개별 이벤트

검색을 열어 개별 요청 및 관련된 이벤트를 조사합니다.

![검색 블레이드에서 페이지 이름이나 다른 속성을 검색합니다.](./media/app-insights-asp-net/21-search.png)

[검색에 대한 자세한 정보](app-insights-diagnostic-search.md)

* *연결된 이벤트가 없나요?* [서버 예외](app-insights-asp-net-exceptions.md) 및 [종속성](app-insights-asp-net-dependencies.md)을 설정합니다.


### 메트릭: 집계된 데이터

개요 차트에서 집계된 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-asp-net/12-first-perf.png)

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다. [메트릭에 대해 자세히 알아봅니다.][perf]

* *사용자 또는 페이지 데이터가 없나요?* - [사용자 및 페이지 데이터 추가](app-insights-web-track-usage.md)

### 분석: 강력한 쿼리 언어

더 많은 데이터가 누적되면 쿼리를 실행하여 데이터를 집계하고 개별 인스턴스를 찾을 수 있습니다. [분석]()은 성능 및 사용 이해 및 진단 목적 모두에 강력한 도구입니다.

![분석 예제](./media/app-insights-asp-net/025.png)


## 데이터가 없나요?

* Visual Studio에서 앱은 원격 분석을 전송해야 합니다. 출력 창 및 진단 허브에 추적을 표시합니다.
* Azure에서 올바른 작업을 보고 있는지 확인하세요. [Azure 포털](https://portal.azure.com)에 로그인하고 "찾아보기 >", "Application Insights"를 클릭한 다음 앱을 선택합니다.
* 응용 프로그램을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* [검색][diagnostic] 블레이드를 열고 개별 이벤트를 봅니다. 경우에 따라 이벤트가 메트릭 파이프라인을 통해 들어오려면 시간이 약간 더 걸립니다.
* 몇 초 정도 기다렸다가 새로고침을 클릭합니다.
* [문제 해결][qna]을 참조하세요.



## 응용 프로그램 게시

이제 응용 프로그램을 배포하고 누적되는 데이터를 관찰합니다.

[라이브 스트림](#live-stream)을 사용하여 앱이 제대로 작동하는지를 알려주는 배포 또는 다시 배포 시 처음 몇 분간을 모니터링합니다. 특히, 이전 버전을 대체하는 경우 성능이 향상되었는지 여부를 알고 싶어 합니다. 문제가 있는 경우에는 이전 버전으로 되돌리는 것이 좋습니다.


#### 빌드 서버에 문제가 있나요?

[이 문제 해결 항목](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)을 참조하세요.

> [AZURE.NOTE] 앱에서 다양한 원격 분석을 생성하는 경우(ASP.NET SDK 버전 2.0.0-beta3 이상 사용), 적응 샘플링 모듈 이벤트의 대표적인 일부만 전송하여 포털에 전송되는 볼륨이 자동으로 줄어듭니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다. [샘플링에 대해 알아봅니다](app-insights-sampling.md).



## 다음 단계

- [사용자 및 페이지 데이터](../article/application-insights/app-insights-javascript.md#selector1)
- [예외](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
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

 

<!---HONumber=AcomDC_0518_2016-->