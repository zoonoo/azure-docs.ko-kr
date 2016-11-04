---
title: Application Insights로 ASP.NET에 대한 웹앱 분석 설정 | Microsoft Docs
description: ASP.NET 웹 사이트, 호스팅된 온-프레미스 또는 Azure에 대한 성능, 가용성 및 사용 현황 분석을 구성합니다.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: daviste

---
# ASP.NET용 Application Insights 설치
[Visual Studio Application Insights](app-insights-overview.md)는 실시간 응용 프로그램을 모니터링하여 [성능 문제 및 예외 사항을 감지 및 진단](app-insights-detect-triage-diagnose.md)하고 [앱이 어떻게 사용되는지 검색](app-insights-overview-usage.md)할 수 있도록 돕습니다. Azure 웹앱뿐 아니라 온-프레미스 IIS 서버 또는 클라우드 VM에서 호스트된 앱에서도 작동합니다.

## 시작하기 전에
다음 작업을 수행해야 합니다.

* Visual Studio 2013 업데이트 3 이상 나중일수록 좋습니다.
* [Microsoft Azure](http://azure.com) 구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](http://live.com)을 사용하여 사용자를 추가할 수 있습니다.

관심이 있는 경우 확인할 대체 문서가 있습니다.

* [런타임 시 웹앱 계측](app-insights-monitor-performance-live-website-now.md)
* [Azure 클라우드 서비스](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Application Insights SDK 추가
### 새 프로젝트의 경우
Visual Studio에서 새 프로젝트를 만들 경우 Application Insights를 선택해야 합니다.

![ASP.NET 프로젝트 만들기](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### ...기존 프로젝트의 경우
솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights 원격 분석 추가** 또는 **Application Insights 구성**을 선택합니다.

![Application Insights 추가 선택](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core 프로젝트? - [다음 지침에 따라 몇 줄의 코드를 수정합니다](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs).

## <a name="run"></a> 2. 앱 실행
F5 키를 사용하여 응용 프로그램을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 로그된 이벤트 수가 표시됩니다.

![Visual Studio에서 Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/app-insights-asp-net/54.png)

## 3\. 원격 분석을 확인합니다.
### Visual Studio에서
Visual Studio에서 Application Insights 창을 열고 Application Insights 단추를 클릭하거나 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.

![Visual Studio에서 Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/app-insights-asp-net/55.png)

이 보기에서는 앱의 서버 쪽에서 생성된 원격 분석을 보여 줍니다. 필터를 테스트하고 이벤트를 클릭하여 자세한 정보를 확인합니다.

[Visual Studio의 Application Insights 도구에 대해 자세히 알아봅니다](app-insights-visual-studio.md).

<a name="monitor"></a>

### 포털에서...
*SDK 설치만*을 선택하지 않은 경우 Application Insights 웹 포털에서 원격 분석을 확인할 수도 있습니다.

포털에는 Visual Studio 보다 많은 차트, 분석 도구 및 대시보드가 있습니다.

[Azure 포털](https://portal.azure.com/)에서 Application Insights 리소스를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

포털은 앱에서 원격 분석의 보기를 엽니다. ![](./media/app-insights-asp-net/66.png)

* [라이브 메트릭 스트림](app-insights-metrics-explorer.md#live-metrics-stream)에 첫 번째 원격 분석이 표시됩니다.
* **검색**(1)에 개별 이벤트가 표시됩니다. 데이터가 표시되는 데 몇 분이 걸릴 수 있습니다. 이벤트를 클릭하여 속성을 확인합니다.
* 집계된 메트릭은 차트(2)에 표시됩니다. 데이터를 여기에 표시하려면 1-2분 정도 걸릴 수 있습니다. 차트를 클릭하여 자세한 내용이 있는 블레이드를 엽니다.

[Azure 포털에서 Application Insights를 사용하는 방법에 대해 자세히 알아봅니다](app-insights-dashboards.md).

## 4\. 앱 게시
IIS 서버 또는 Azure에 앱을 게시합니다. [라이브 메트릭 스트림](app-insights-metrics-explorer.md#live-metrics-stream)을 보고 모두 문제 없이 실행되고 있는지 확인합니다.

Application Insights 포털에서 원격 분석이 구축되고 있는 것을 볼 수 있으며, 메트릭을 모니터링하고 원격 분석을 검색하고 [대시보드](app-insights-dashboards.md)를 설정할 수 있습니다. 또한 강력한 [분석 쿼리 언어](app-insights-analytics.md)를 사용하여 사용량 및 성능을 분석하거나 특정 이벤트를 찾을 수 있습니다.

[Visual Studio](app-insights-visual-studio.md)에서 진단 검색 및 [추세](app-insights-visual-studio-trends.md) 등의 도구를 사용하여 원격 분석을 계속 수행할 수도 있습니다.

> [!NOTE]
> 앱에서 너무 많은 원격 분석을 보내 [제한 한도](app-insights-pricing.md#limits-summary)에 도달할 경우 자동 [샘플링](app-insights-sampling.md)이 켜집니다. 샘플링은 앱에서 보내는 원격 분석의 양을 줄이고 진단을 위해 상호 관련된 데이터를 유지합니다.
> 
> 

## <a name="land"></a> 'Application Insights 추가'에서 무엇을 했나요?
Application Insights는 앱에서 Microsoft Azure에서 호스팅되는 Application Insights 포털에 원격 분석을 보냅니다.

![](./media/app-insights-asp-net/01-scheme.png)

따라서 이 명령은 다음 세 가지를 수행했습니다.

1. 프로젝트에 Application Insights 웹 SDK NuGet 패키지를 추가합니다. Visual Studio에서 보려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다.
2. [Azure 포털](https://portal.azure.com/)에서 Application Insights 리소스를 만듭니다. 이곳에서 사용자의 데이터를 볼 수 있습니다. 리소스를 식별하는 *계측 키*를 검색합니다.
3. `ApplicationInsights.config`에 계측 키를 삽입하여 SDK가 포털에 원격 분석을 보낼 수 있습니다.

원하는 경우 [ASP.NET 4](app-insights-windows-services.md) 또는 [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)에 다음 단계를 수동으로 수행할 수 있습니다.

### SDK의 나중 버전으로 업그레이드하려면
[SDK의 새 릴리스](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)로 업그레이드하려면 NuGet 패키지 관리자를 다시 열고 설치된 패키지를 필터링합니다. Microsoft.ApplicationInsights.Web을 선택하고 업그레이드를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 변경한 경우, 업그레이드 전에 복사본을 저장하고 나중에 변경 내용을 새 버전에 병합합니다.

## 다음 단계
|  |
| --- | --- |
| **[Visual Studio에서 Application Insights로 작업](app-insights-visual-studio.md)**<br/>원격 분석, 진단 검색을 디버깅하여 코드에 드릴스루합니다. |
| **[Application Insights 포털로 작업](app-insights-dashboards.md)**<br/>대시보드, 강력한 분석 및 진단 도구, 경고, 응용 프로그램의 라이브 종속성 맵 및 원격 분석 내보내기입니다. |
| **[더 많은 데이터](app-insights-asp-net-more.md)**<br/>사용량, 가용성, 종속성, 예외를 모니터링합니다. 로깅 프레임 워크의 추적을 통합합니다. 사용자 지정 원격 분석을 작성합니다. |

<!---HONumber=AcomDC_0907_2016-->