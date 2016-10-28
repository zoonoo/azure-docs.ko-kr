<properties
	pageTitle="Azure 웹앱 성능 모니터링 | Microsoft Azure"
	description="Azure 웹앱에 대한 응용 프로그램 성능 모니터링입니다. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="awills"/>

# Azure 웹앱 성능 모니터링

[Azure Portal](https://portal.azure.com)에서 [Azure 웹앱](../app-service-web/app-service-web-overview.md)의 응용 프로그램 성능 모니터링을 설정할 수 있습니다. [Visual Studio Application Insights](app-insights-overview.md)는 해당 작업에 대한 원격 분석을 저장하고 분석하는 Application Insights 서비스에 보내는 앱을 계측합니다. 여기서 메트릭 차트 및 검색 도구를 사용하여 문제를 진단하고 성능을 개선하며 사용량 평가할 수 있습니다.

## 실행 시간 또는 빌드 시간

다음과 같은 두 가지 방법 중 하나로 앱을 계측하여 모니터링을 구성할 수 있습니다.

* **런타임** - 웹앱이 이미 라이브 상태인 경우 성능 모니터링 확장을 선택할 수 있습니다. 앱을 다시 빌드하거나 설치할 필요는 없습니다. 응답 시간, 성공률, 예외, 종속성 등을 모니터링하는 패키지의 표준 집합을 얻게 됩니다.

    **Application Insights** 및 **New Relic**은 사용할 수 있는 두 가지 런타임 성능 모니터링 확장 프로그램입니다.
 
* **빌드 시간** - 개발 중인 앱에서 패키지를 설치할 수 있습니다. 이 옵션은 융통성이 뛰어납니다. 동일한 표준 패키지외에도 원격 분석 데이터를 사용자 지정하거나 고유한 원격 분석을 보내는 코드를 작성할 수 있습니다. 앱 도메인의 의미 체계에 따라 특정 작업 또는 레코드 이벤트를 기록할 수 있습니다.

    **Application Insights**는 빌드 시간 패키지를 제공합니다.


## Application Insights 패키지를 사용하여 앱 빌드...

Application Insights는 앱에 SDK를 설치하여 더 자세한 원격 분석을 제공할 수 있습니다.

Visual Studio(2013 업데이트 2 이상)에서 프로젝트에 Application Insights SDK를 추가합니다.

![웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.](./media/app-insights-azure-web-apps/03-add.png)

로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.

작업에는 두 가지 효과가 있습니다.

1. 원격 분석을 저장하고, 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 만듭니다.
2. Application Insights NuGet 패키지를 코드에 추가하고 원격 분석을 Azure 리소스로 보내도록 구성합니다.

개발 컴퓨터(F5)에서 앱을 실행하여 원격 분석을 테스트하거나 계속 진행하여 앱을 다시 게시할 수 있습니다.

SDK는 API를 제공하므로 [사용자 지정 원격 분석을 작성하여](../application-insights/app-insights-api-custom-events-metrics.md) 사용량을 추적할 수 있습니다.

### ... 또는 리소스를 수동으로 설정

Visual Studio에서 SDK를 추가하지 않은 경우 원격 분석을 저장하고 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 설정해야 합니다.

![새로 만들기, 개발자 서비스, Application Insights를 클릭합니다. ASP.NET 앱 유형을 선택합니다.](./media/app-insights-azure-web-apps/01-new.png)


## 확장 사용

1. 계측하려는 웹앱 또는 가상 컴퓨터의 제어 블레이드로 이동합니다.

2. Application Insights 또는 New Relic 확장 프로그램을 추가합니다.

    웹앱을 계측하는 경우:

![설정, 확장 프로그램, 추가, Application Insights](./media/app-insights-azure-web-apps/05-extend.png)

또는 가상 컴퓨터를 사용하는 경우:

![분석 타일을 클릭합니다.](./media/app-insights-azure-web-apps/10-vm1.png)



## 데이터 탐색

1. Application Insights 리소스를 엽니다(찾아보기에서 직접 또는 웹앱의 성능 모니터링 도구에서).

2. 차트를 클릭하여 자세한 내용을 봅니다.

    ![Application Insights 개요 블레이드에서 차트를 클릭합니다.](./media/app-insights-azure-web-apps/07-dependency.png)

    [메트릭 블레이드를 사용자 지정](../application-insights/app-insights-metrics-explorer.md)할 수 있습니다.

3. 개별 이벤트와 해당 속성을 보려면 또 클릭합니다.

    ![해당 유형에 대해 필터링된 검색을 열려면 이벤트 유형을 클릭합니다.](./media/app-insights-azure-web-apps/08-requests.png)

    "..." 링크를 확인하여 모든 속성을 엽니다.

    [검색을 사용자 지정](../application-insights/app-insights-diagnostic-search.md)할 수 있습니다.

원격 분석을 통해 좀더 강력하게 검색하려면 [분석 쿼리 언어](../application-insights/app-insights-analytics-tour.md)를 사용합니다.


## 질문과 대답

다른 Application Insights 리소스에 데이터를 보내도록 변경하려면 어떻게 해야 합니까?

* *Visual Studio의 코드에 Application Insights를 추가 하는 경우:* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights > 구성**을 선택한 다음 원하는 리소스를 선택합니다. 새 리소스를 만드는 옵션이 생깁니다. 다시 빌드하고 다시 배포합니다.
* *그렇지 않은 경우:* Azure에서 웹앱 제어 블레이드를 열고 **도구 > 확장**을 엽니다. Application Insights 확장을 삭제합니다. 그런 다음 **도구 > 성능**을 열고, '여기를 클릭'하고, Application Insights를 선택한 다음 원하는 리소스를 선택합니다. (새 Application Insights 리소스를 만들려는 경우 이 작업을 먼저 수행합니다.)


## 다음 단계

* [Azure 진단을 사용](app-insights-azure-diagnostics.md)하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../azure-portal/insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../azure-portal/insights-receive-alert-notifications.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](app-insights-web-track-usage.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* 사이트가 중단된 경우 이를 알리는 [가용성 웹 테스트를 설정](app-insights-monitor-web-app-availability.md)합니다.

<!---HONumber=AcomDC_0907_2016-->