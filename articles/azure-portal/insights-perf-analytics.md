<properties 
	pageTitle="응용 프로그램 성능 모니터링" 
	description="차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# 응용 프로그램 성능 모니터링

[Azure 포털](http://portal.azure.com)에서 웹앱 또는 가상 컴퓨터 내 응용 프로그램 종속성에 대한 통계와 세부 정보를 수집하도록 모니터링을 설정할 수 있습니다.

Azure는 *확장*을 활용하여 응용 프로그램 성능 모니터링(또는 *APM*)을 지원합니다. 이 확장은 응용 프로그램에 설치되며 데이터를 수집하고 모니터링 서비스로 다시 보고합니다.

## 확장 사용

1. **찾아보기**를 클릭하고 계측할 웹앱이나 가상 컴퓨터를 선택합니다.

2. **모니터링** 아래 **응용 프로그램 모니터링** 타일을 클릭합니다.

3. **Application Insights** 또는 **New Relic**과 같이 사용하고자 하는 확장 공급자를 선택합니다.

![웹앱 APM](./media/insights-perf-analytics/05-extend.png)

또는 가상 컴퓨터를 사용하는 경우:

![가상 컴퓨터](./media/insights-perf-analytics/10-vm1.png)

### Application Insights의 경우: SDK로 다시 빌드

New Relic은 추가 계측 없이 자동으로 설치할 수 있지만 Application Insights는 한 가지 추가 요구사항이 있습니다.

Visual Studio에서 프로젝트에 Application Insights SDK를 추가합니다.

![웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.](./media/insights-perf-analytics/03-add.png)

로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.

개발 컴퓨터에서 앱을 실행하여 원격 분석을 테스트하거나 계속 진행하여 다시 게시할 수 있습니다.

SDK는 API를 제공하므로 [사용자 지정 원격 분석을 작성하여](../app-insights-api-custom-events-metrics.md) 사용량을 추적할 수 있습니다.

## 데이터 탐색

잠시 동안 응용 프로그램을 사용하여 일부 원격 분석을 생성합니다.

1. 그런 다음, 웹앱 또는 가상 컴퓨터 블레이드에서 설치된 확장을 표시합니다.
2. 자신의 응용 프로그램을 나타내는 행을 클릭하여 해당 공급자로 이동합니다. ![새로 고침 클릭](./media/insights-perf-analytics/06-overview.png)

또한 **찾아보기**를 사용하여 Application Insights 구성 요소 또는 사용한 New Relic 계정으로 바로 이동할 수 있습니다.

일단 블레이드로 이동하면 Application Insights의 경우 다음이 가능합니다. - 성능 열기:

![Application Insights 개요 블레이드에서 성능 타일을 클릭 합니다.](./media/insights-perf-analytics/07-dependency.png)

- 드릴스루하여 개별 요청을 참조하십시오.

![눈금에서 종속성을 클릭하여 관련된 요청을 확인합니다.](./media/insights-perf-analytics/08-requests.png)

- 다음은 SQL 호출 횟수와 평균 지속 시간 및 표준 편차와 같은 관련된 통계를 포함하여 SQL 종속성에 소요된 시간을 보여주는 예제입니다. 

![](./media/insights-perf-analytics/01-example.png)



## 다음 단계

* [서비스 상태 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* [모니터링 및 진단을 사용](insights-how-to-use-diagnostics.md)하여 서비스의 자세한 고빈도 메트릭을 수집합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](../app-insights-web-track-usage.md)를 사용하여 웹 페이지를 방문하는 브라우저에 대한 클라이언트 분석을 가져옵니다.
* 페이지가 다운된 경우 이를 찾을 수 있도록 Application Insights를 사용하여 [웹 페이지의 가용성 및 응답성을 모니터링](../app-insights-monitor-web-app-availability.md)합니다.
 

<!---HONumber=62-->