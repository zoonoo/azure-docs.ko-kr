<properties
	pageTitle="Azure 웹앱 성능 모니터링 | Microsoft Azure"
	description="차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다."
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="awills"/>

# Azure 웹앱 성능 모니터링

[Azure 포털](https://portal.azure.com)에서 [Azure 웹앱](../app-service-web/app-service-web-overview.md) 또는 [가상 컴퓨터](../virtual-machines/virtual-machines-linux-about.md) 내 응용 프로그램 종속성에 대한 통계와 세부 정보를 수집하도록 모니터링을 설정할 수 있습니다.

Azure는 확장을 활용하여 응용 프로그램 성능 모니터링(APM)을 지원합니다. 이 확장은 응용 프로그램에 설치되며 데이터를 수집하고 모니터링 서비스로 다시 보고합니다.

**Application Insights** 및 **New Relic**은 사용할 수 있는 두 가지 성능 모니터링 확장 프로그램입니다. 프로그램을 사용하려면 런타임에 에이전트를 설치해야 합니다. 또한 Application Insights와 함께 SDK를 사용하여 코드를 작성하는 옵션이 있습니다. SDK를 통해 앱의 사용과 성능을 보다 자세하게 모니터링하기 위한 코드를 작성할 수 있습니다.

## Application Insights

### (선택 사항) SDK와 함께 앱을 다시 빌드 중...

Application Insights는 앱에 SDK를 설치하여 더 자세한 원격 분석을 제공할 수 있습니다.

Visual Studio(2013 업데이트 2 이상)에서 프로젝트에 Application Insights SDK를 추가합니다.

![웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.](./media/insights-perf-analytics/03-add.png)

로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.

작업에는 두 가지 효과가 있습니다.

1. 원격 분석을 저장하고, 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 만듭니다.
2. Application Insights NuGet 패키지를 코드에 추가하고 원격 분석을 Azure 리소스로 보내도록 구성합니다.

개발 컴퓨터(F5)에서 앱을 실행하여 원격 분석을 테스트하거나 계속 진행하여 앱을 다시 게시할 수 있습니다.

SDK는 API를 제공하므로 [사용자 지정 원격 분석을 작성하여](../application-insights/app-insights-api-custom-events-metrics.md) 사용량을 추적할 수 있습니다.

### ... 또는 리소스를 수동으로 설정

Visual Studio에서 SDK를 추가하지 않은 경우 원격 분석을 저장하고, 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 설정해야 합니다.

![새로 만들기, 개발자 서비스, Application Insights를 클릭합니다. ASP.NET 앱 유형을 선택합니다.](./media/insights-perf-analytics/01-new.png)


## 확장 사용

1. 계측하려는 웹앱 또는 가상 컴퓨터의 제어 블레이드로 이동합니다.

2. Application Insights 또는 New Relic 확장 프로그램을 추가합니다.

    웹앱을 계측하는 경우:

![설정, 확장 프로그램, 추가, Application Insights](./media/insights-perf-analytics/05-extend.png)

또는 가상 컴퓨터를 사용하는 경우:

![분석 타일을 클릭합니다.](./media/insights-perf-analytics/10-vm1.png)



## 데이터 탐색

1. Application Insights 리소스를 엽니다(찾아보기에서 직접 또는 웹앱의 성능 모니터링 도구에서).

2. 차트를 클릭하여 자세한 내용을 봅니다.

    ![Application Insights 개요 블레이드에서 차트를 클릭합니다.](./media/insights-perf-analytics/07-dependency.png)

    [메트릭 블레이드를 사용자 지정](../application-insights/app-insights-metrics-explorer.md)할 수 있습니다.

3. 개별 이벤트와 해당 속성을 보려면 또 클릭합니다.

    ![해당 유형에 대해 필터링된 검색을 열려면 이벤트 유형을 클릭합니다.](./media/insights-perf-analytics/08-requests.png)

    "..." 링크를 확인하여 모든 속성을 엽니다.

    [검색을 사용자 지정](../application-insights/app-insights-diagnostic-search.md)할 수 있습니다.

원격 분석을 통해 좀더 강력하게 검색하려면 [분석 쿼리 언어](../application-insights/app-insights-analytics-tour.md)를 사용합니다.


## 질문과 대답

다른 Application Insights 리소스에 데이터를 보내도록 변경하려면 어떻게 해야 합니까?

* *Visual Studio의 코드에 Application Insights를 추가 하는 경우:* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights > 구성**을 선택한 다음 원하는 리소스를 선택합니다. 새 리소스를 만드는 옵션이 생깁니다. 다시 빌드하고 다시 배포합니다.
* *그렇지 않은 경우:* Azure에서 웹앱 제어 블레이드를 열고 **도구 > 확장**을 엽니다. Application Insights 확장을 삭제합니다. 그런 다음 **도구 > 성능**을 열고, '여기를 클릭'하고, Application Insights를 선택한 다음 원하는 리소스를 선택합니다. (새 Application Insights 리소스를 만들려는 경우 이 작업을 먼저 수행합니다.)


## 다음 단계

* [서비스 상태 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* [모니터링 및 진단을 사용](insights-how-to-use-diagnostics.md)하여 서비스의 자세한 고빈도 메트릭을 수집합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](../application-insights/app-insights-web-track-usage.md)를 사용하여 웹 페이지를 방문하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* 사이트가 다운된 경우 이를 알리는 [가용성 웹 테스트를 설정](../application-insights/app-insights-monitor-web-app-availability.md)합니다.

<!---HONumber=AcomDC_0803_2016-->