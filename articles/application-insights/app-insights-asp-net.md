---
title: "Application Insights로 ASP.NET에 대한 웹앱 분석 설정 | Microsoft Docs"
description: "ASP.NET 웹 사이트, 호스팅된 온-프레미스 또는 Azure에 대한 성능, 가용성 및 사용 현황 분석을 구성합니다."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dc95c922b71d18cf791ea98f4ab1a02d2bac2c3b
ms.openlocfilehash: 5103c28047e6d5e7be5f4f3b7933196de7045eeb


---
# <a name="set-up-application-insights-for-aspnet"></a>ASP.NET용 Application Insights 설치
[Azure Application Insights](app-insights-overview.md)는 실시간 응용 프로그램을 모니터링하여 [성능 문제 및 예외 사항을 감지 및 진단](app-insights-detect-triage-diagnose.md)하고 [앱이 어떻게 사용되는지 검색](app-insights-overview-usage.md)할 수 있도록 돕습니다.  Azure 웹앱뿐 아니라 온-프레미스 IIS 서버 또는 클라우드 VM에서 호스트된 앱에서도 작동합니다.

## <a name="before-you-start"></a>시작하기 전에
다음 작업을 수행해야 합니다.

* Visual Studio 2013 업데이트 3 이상 나중일수록 좋습니다.
* [Microsoft Azure](http://azure.com)구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](http://live.com)을 사용하여 사용자를 추가할 수 있습니다. 

관심이 있는 경우 확인할 대체 문서가 있습니다.

* [런타임 시 웹앱 계측](app-insights-monitor-performance-live-website-now.md)
* [Azure 클라우드 서비스](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Application Insights SDK 추가
### <a name="if-its-a-new-project"></a>새 프로젝트의 경우
Visual Studio에서 새 프로젝트를 만들 경우 Application Insights를 선택해야 합니다. 

![ASP.NET 프로젝트 만들기](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>...기존 프로젝트의 경우
솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 원격 분석 추가** 또는 **Application Insights 구성**을 선택합니다.

![Application Insights 추가 선택](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core 프로젝트? - [다음 지침에 따라 몇 줄의 코드를 수정합니다](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. 앱 실행
F5 키를 사용하여 응용 프로그램을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 로그된 이벤트 수가 표시됩니다. 

![Visual Studio에서 Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. 원격 분석을 확인합니다.
### <a name="-in-visual-studio"></a>Visual Studio에서
Visual Studio에서 Application Insights 창을 열고 Application Insights 단추를 클릭하거나 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.

![Visual Studio에서 Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/app-insights-asp-net/55.png)

이 보기('디버그 세션의 데이터')에서는 앱의 서버 쪽에서 생성된 원격 분석을 보여 줍니다. 필터를 테스트하고 이벤트를 클릭하여 자세한 정보를 확인합니다.

* *데이터가 없나요? 시간 범위가 올바른지 확인하고 [검색] 아이콘을 클릭하세요.*

[Visual Studio의 Application Insights 도구에 대해 자세히 알아봅니다](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>포털에서...
*SDK 설치만* 을 선택하지 않은 경우 Application Insights 웹 포털에서 원격 분석을 확인할 수도 있습니다. 

포털에는 Visual Studio 보다 많은 차트, 분석 도구 및 대시보드가 있습니다. 

Application Insights 리소스를 엽니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 해당 위치에서 찾거나 Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하면 됩니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

* *액세스 오류인가요? Microsoft 자격 증명 집합이 둘 이상인 경우 잘못된 집합으로 로그인했을 수 있습니다. 포털에서 로그아웃하고 다시 로그인하세요.*

포털이 앱의 원격 분석 보기에서 열립니다. ![Application Insights 개요 페이지](./media/app-insights-asp-net/66.png)

자세한 정보를 확인하려면 원하는 타일 또는 차트를 클릭합니다.

### <a name="more-detail-in-the-portal"></a>포털의 자세한 정보

* [**라이브 메트릭 스트림**](app-insights-metrics-explorer.md#live-metrics-stream)은 거의 즉시로 원격 분석을 표시합니다.

    ![[개요] 블레이드에서 [라이브 스트림] 클릭](./media/app-insights-asp-net/livestream.png)

    앱이 실행되는 동시에 [라이브 스트림]을 열어 연결을 허용합니다.

    [라이브 스트림]은 전송된 후 1분 동안 원격 분석만 표시합니다. 자세한 기록 조사를 수행하려면 [검색], [메트릭 탐색기] 및 [분석]을 사용합니다. 이러한 위치에 데이터가 표시되려면 몇 분 정도 걸릴 수 있습니다.

* [**검색**](app-insights-diagnostic-search.md)은 요청, 예외 및 페이지 보기와 같은 개별 이벤트를 표시합니다. 이벤트 유형, 용어 일치 및 속성 값을 기준으로 필터링할 수 있습니다. 이벤트를 클릭하여 속성 및 관련 이벤트를 확인합니다. 

    ![[개요] 블레이드에서 [검색] 클릭](./media/app-insights-asp-net/search.png)

 * 개발 모드에서는 종속성(AJAX) 이벤트를 많이 볼 수 있습니다. 이러한 이벤트는 브라우저와 서버 에뮬레이터 간의 동기화입니다. 이를 숨기려면 [종속성] 필터를 클릭합니다.
* [**집계된 메트릭**](app-insights-metrics-explorer.md)(예: 요청 및 실패율)이 차트에 표시됩니다. 차트를 클릭하여 자세한 내용이 있는 블레이드를 엽니다. 차트의 **편집** 태그를 클릭하여 필터, 크기 등을 설정합니다.
    
    ![[개요] 블레이드에서 차트 클릭](./media/app-insights-asp-net/metrics.png)

[Azure 포털에서 Application Insights를 사용하는 방법에 대해 자세히 알아봅니다](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. 앱 게시
IIS 서버 또는 Azure에 앱을 게시합니다. [라이브 메트릭 스트림](app-insights-metrics-explorer.md#live-metrics-stream) 을 보고 모두 문제 없이 실행되고 있는지 확인합니다.

Application Insights 포털에서 원격 분석이 구축되고 있는 것을 볼 수 있으며, 메트릭을 모니터링하고 원격 분석을 검색하고 [대시보드](app-insights-dashboards.md)를 설정할 수 있습니다. 또한 강력한 [분석 쿼리 언어](app-insights-analytics.md) 를 사용하여 사용량 및 성능을 분석하거나 특정 이벤트를 찾을 수 있습니다. 

[Visual Studio](app-insights-visual-studio.md)에서 진단 검색 및 [추세](app-insights-visual-studio-trends.md) 등의 도구를 사용하여 원격 분석을 계속 수행할 수도 있습니다.

> [!NOTE]
> 앱에서 너무 많은 원격 분석을 보내 [제한 한도](app-insights-pricing.md#limits-summary)에 도달할 경우 자동 [샘플링](app-insights-sampling.md)이 켜집니다. 샘플링은 앱에서 보내는 원격 분석의 양을 줄이고 진단을 위해 상호 관련된 데이터를 유지합니다.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> 'Application Insights 추가'에서 무엇을 했나요?
Application Insights는 앱에서 Microsoft Azure에서 호스팅되는 Application Insights 포털에 원격 분석을 보냅니다.

![](./media/app-insights-asp-net/01-scheme.png)

따라서 이 명령은 다음 세 가지를 수행했습니다.

1. 프로젝트에 Application Insights 웹 SDK NuGet 패키지를 추가합니다. Visual Studio에서 보려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
2. [Azure 포털](https://portal.azure.com/)에서 Application Insights 리소스를 만듭니다. 이곳에서 사용자의 데이터를 볼 수 있습니다. 리소스를 식별하는 *계측 키* 를 검색합니다.
3. `ApplicationInsights.config`에 계측 키를 삽입하여 SDK가 포털에 원격 분석을 보낼 수 있습니다.

원하는 경우 [ASP.NET 4](app-insights-windows-services.md) 또는 [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)에 다음 단계를 수동으로 수행할 수 있습니다.

### <a name="to-upgrade-to-future-sdk-versions"></a>SDK의 나중 버전으로 업그레이드하려면
[SDK의 새 릴리스](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)로 업그레이드하려면 NuGet 패키지 관리자를 다시 열고 설치된 패키지를 필터링합니다. Microsoft.ApplicationInsights.Web을 선택하고 업그레이드를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 변경한 경우, 업그레이드 전에 복사본을 저장하고 나중에 변경 내용을 새 버전에 병합합니다.

## <a name="add-more-telemetry"></a>원격 분석 더 추가
### <a name="web-pages-and-single-page-apps"></a>웹 페이지와 단일 페이지 앱
1. 웹 페이지에 [JavaScript 코드 조각을 추가](app-insights-javascript.md)하여 브라우저 및 사용량 블레이드를 페이지 보기, 로드 시간, 브라우저 예외, AJAX 호출 성능, 사용자 및 세션 수에 대한 데이터로 명확하게 합니다.
2. [사용자 지정 이벤트를 코딩](app-insights-api-custom-events-metrics.md)하여 사용자 작업의 수와 시간을 측정하고 평가합니다.

### <a name="dependencies-exceptions-and-performance-counters"></a>종속성, 예외 및 성능 카운터
서버 컴퓨터 각각에 대한 [상태 모니터를 설치](app-insights-monitor-performance-live-website-now.md)하여 앱에 대한 추가 원격 분석을 가져옵니다. 결과는 다음과 같습니다.

* [성능 카운터](app-insights-performance-counters.md) - 
  CPU, 메모리, 디스크 및 응용 프로그램에 관련된 다른 성능 카운터입니다. 
* [예외](app-insights-asp-net-exceptions.md) - 자세한 몇 가지 예외에 대한 원격 분석입니다.
* [종속성](app-insights-asp-net-dependencies.md) - REST API 또는 SQL 서비스를 호출합니다. 외부 구성 요소에서 느린 응답이 앱에서 성능 문제를 유발하는지 여부에 대해 알아봅니다. (응용 프로그램에서 .NET 4.6을 실행하는 경우 원격 분석을 가져올 때 상태 모니터가 필요 없습니다.)

### <a name="diagnostic-code"></a>진단 코드
문제가 있습니까? 문제를 진단하기 위해 앱에 코드를 삽입 하려는 경우 몇 가지 옵션이 있습니다.

* [로그 추적 캡처](app-insights-asp-net-trace-logs.md): 이미 Log4N, NLog 또는 System.Diagnostics.Trace를 사용하여 추적 이벤트를 기록하고 있다면 출력을 Application Insights로 전송하여 요청과 연결하고 검색하고 분석합니다. 
* [사용자 지정 이벤트 및 메트릭](app-insights-api-custom-events-metrics.md): 서버 또는 웹 페이지 코드에 있는 Trackevent() 및 Trackmetric()을 사용합니다.
* [추가 속성이 있는 태그 원격 분석](app-insights-api-filtering-sampling.md#add-properties)

[Search](app-insights-diagnostic-search.md)를 사용하여 특정 이벤트를 찾아 상관 관계를 분석하고 [Analytics](app-insights-analytics.md)를 사용하여 더 강력한 쿼리를 수행합니다.

## <a name="alerts"></a>경고
응용 프로그램에 문제가 있는지 신속히 알려줍니다. (사용자가 알리기 전에 알려주세요!) 

* [웹 테스트를 만들어](app-insights-monitor-web-app-availability.md) 사이트가 웹에 표시되는지 확인합니다.
* [사전 진단](app-insights-proactive-diagnostics.md)은 자동으로 실행됩니다(앱에 최소 일정량의 트래픽이 있는 경우). 설정 작업을 수행할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고를 설정](app-insights-alerts.md)하여 메트릭이 임계값을 초과할 경우 경고 메시지를 표시합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.

기본적으로 Azure 구독 소유자에게 경고 알림이 전송됩니다. 

![경고 전자 메일 샘플](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>버전 및 릴리스 추적
### <a name="track-application-version"></a>추적 응용 프로그램 버전
MS 빌드 프로세스에서 `buildinfo.config` 가 생성되도록 합니다. .csproj 파일에서 다음을 추가합니다.  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

빌드 정보가 있는 경우 Application Insights 웹 모듈에서 원격 분석의 모든 항목에 **응용 프로그램 버전** 을 속성으로 자동으로 추가합니다. 이렇게 하면 [진단 검색](app-insights-diagnostic-search.md)을 수행하거나 [메트릭을 탐색](app-insights-metrics-explorer.md)할 때 버전을 기준으로 필터링할 수 있습니다. 

그러나 빌드 버전 번호가 Visual Studio의 개발자 빌드가 아닌 MS 빌드에서 생성되도록 합니다.

### <a name="release-annotations"></a>릴리스 주석
Visual Studio Team Services를 사용하는 경우 새 버전을 릴리스할 때마다 [주석 표식](app-insights-annotations.md)이 차트에 추가됩니다.

![릴리스 주석 샘플](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>다음 단계
|  |
| --- | --- |
| **[Visual Studio Online에서 Application Insights로 작업](app-insights-visual-studio.md)**<br/>원격 분석, 진단 검색을 디버깅하여 코드에 드릴스루합니다. |
| **[Application Insights 포털 사용](app-insights-dashboards.md)**<br/>대시보드, 강력한 분석 및 진단 도구, 경고, 응용 프로그램의 라이브 종속성 맵 및 원격 분석 내보내기입니다. |
| **[더 많은 데이터 추가](app-insights-asp-net-more.md)**<br/>사용량, 가용성, 종속성, 예외를 모니터링합니다. 로깅 프레임 워크의 추적을 통합합니다. 사용자 지정 원격 분석을 작성합니다. |




<!--HONumber=Dec16_HO3-->


