<properties 
	pageTitle="ASP.NET용 Application Insights" 
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
	ms.date="09/09/2015" 
	ms.author="awills"/>


# ASP.NET용 Application Insights

*Application Insights는 미리 보기 상태입니다.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights)는 실시간 응용 프로그램을 모니터링하여 [성능 문제 및 예외 사항을 감지 및 진단][detect]하고 [앱이 어떻게 사용되는지 검색][knowUsers]할 수 있도록 돕습니다. 다양한 응용 프로그램 종류에서 사용할 수 있습니다. Azure 웹앱뿐 아니라 온-프레미스 IIS 서버 또는 Azure VM에서 호스트된 앱에서도 작동합니다. [장치 앱 및 Java 서버에 대해서도 다룹니다][start].

![예제 성능 모니터링 차트](./media/app-insights-asp-net/10-perf.png)


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

프로젝트를 처음 만드는 경우 Microsoft Azure 미리 보기에 로그인하거나 등록하라는 메시지가 표시됩니다. 해당 계정은 Visual Studio Online 계정과는 별개입니다.

이 앱이 더 큰 응용 프로그램의 일부인 경우, **구성 설정**을 사용하여 다른 구성 요소와 동일한 리소스 그룹에 넣고자 할 수 있습니다.


####<a name="land"></a> ‘Application Insights 추가'에서 무엇을 했나요?

명령은 다음 단계를 수행했습니다(원하는 경우 수동으로 수행 가능).

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


개요 차트에서 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예:

![클릭하여 추가 데이터 확인](./media/app-insights-asp-net/12-first-perf.png)

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


## 브라우저 모니터링 추가

브라우저 또는 클라이언트쪽 모니터링은 사용자, 세션, 페이지 보기 및 브라우저에서 발생하는 충돌 혹은 예외에 데이터를 제공합니다.

![새로 만들기, 개발자 서비스, Application Insights를 선택합니다.](./media/app-insights-asp-net/16-page-views.png)

사용자는 응용 프로그램과 사용자의 고객이 함께 작동하는지 추적하는 코드를 오른쪽 아래의 클릭과 키 입력 세부 수준에서 작성할 수 있습니다.

#### 클라이언트가 웹 브라우저인 경우

앱을 웹 페이지에 표시하는 경우, 모든 페이지에 JavaScript 코드 조각을 추가합니다. Application Insights 리소스에서 코드를 가져옵니다.

![웹앱에서, 빠른 시작을 열고 '내 웹 페이지를 모니터링하는 코드를 얻기'를 클릭합니다.](./media/app-insights-asp-net/02-monitor-web-page.png)

코드는 응용 프로그램 리소스를 식별하는 계측 키를 포함한다는 것을 참고하세요.

[웹 페이지 추적에 대해 더 알아보기](app-insights-web-track-usage.md)

#### 클라이언트가 장치 앱인 경우

응용 프로그램이 휴대폰 또는 기타 장치 등의 클라이언트에 서비스를 제공하는 경우 사용자의 장치 앱에 [적절한 SDK](app-insights-platforms.md)를 추가하세요.

SDK 서버와 동일한 계측 키를 가진 SDK 클라이언트를 구성하는 경우 함께 볼 수 있도록 두 스트림은 통합됩니다.

## 사용 추적

새 사용자 스토리를 배달한 경우, 사용자들이 얼마나 이용하고 있는지와 그들이 목표를 달성하거나 목표를 달성했는지 알고자 합니다. 클라이언트와 서버에 있는 TrackEvent() 및 코드 안에 다른 호출을 삽입하여 사용자 작업에 대한 자세한 그림을 가져옵니다.

[API를 사용하여 사용량 추적 ][api]


## 진단 로그

문제 진단을 위해 즐겨찾는 로깅 프레임워크에서 [로그 추적 캡처][netlogs] 로그 항목은 Application Insights 원격 분석 이벤트를 따른 [진단 검색][diagnostic]에 나타납니다.

## 응용 프로그램 게시

앱을 (Application Insights를 추가한 이후로) 게시하지 않은 경우 지금 게시합니다. 차트에서 사용자들의 앱 사용에 따라 증가하는 데이터를 확인하세요.


#### 서버에 게시한 후 데이터가 없나요?

서버 방화벽에서 나가는 트래픽에 대해 다음 포트를 엽니다.

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`

### 개발, 테스트 및 릴리스를 위한 별도 리소스를 유지합니다.

주요 응용 프로그램에서, 원격 분석 데이터를 디버깅, 테스트, 프로덕션에서 [별도 리소스](app-insights-separate-resources.md)로 보내는 것을 권장합니다.




## 종속성 추적 및 시스템 성능 카운터 추가

[종속성 메트릭](app-insights-dependencies.md)은 성능 문제 진단을 도와줄 때 매우 유용합니다. 앱에서 데이터베이스, REST API 및 다른 외부 구성 요소로 온 호출을 측정합니다.

![](./media/app-insights-asp-net/04-dependencies.png)

이 단계를 사용하면 [성능 카운터의 보고](app-insights-web-monitor-performance.md#system-performance-counters)(예: CPU, 메모리, 네트워크 선점)도 가능합니다.

#### 앱이 IIS 서버에서 실행되는 경우

관리자 권한으로 서버에 로그인하고 [Application Insights 상태 모니터](http://go.microsoft.com/fwlink/?LinkId=506648)를 설치합니다.

[서버 방화벽에서 추가 포트가 열려 있는지](app-insights-monitor-performance-live-website-now.md#troubleshooting) 확인해야 합니다.

#### 앱이 Azure 웹앱인 경우

Azure 웹앱의 제어판에서 Application Insights 확장을 추가합니다.

![웹앱에서 설정, 확장, 추가, Application Insights](./media/app-insights-asp-net/05-extend.png)

(확장은 SDK로 빌드된 앱만 보조해 줍니다. 상태 모니터와는 달리, 기존 응용 프로그램을 계측할 수 없습니다.)

#### Azure 클라우드 서비스 역할을 모니터링하려면

[상태 모니터를 추가하기 위한 수동 절차](app-insights-cloudservices.md)가 있습니다.

## 가용성 웹 테스트

[웹 테스트 설정][availability]으로 응용 프로그램의 라이브 상태 및 응답성을 외부에서 테스트할 수 있습니다.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






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
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO3-->