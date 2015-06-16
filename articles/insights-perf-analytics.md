<properties 
	pageTitle="응용 프로그램 성능 모니터링" 
	description="차트 부하 및 응답 시간, 종속성 정보 및 성능에 경고를 설정 합니다." 
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

그런 다음[Azure 포털](http://portal.azure.com)웹 응용 프로그램 또는 가상 컴퓨터에 응용 프로그램 종속성에 통계 및 세부 정보를 수집 하려면 모니터링을 설정할 수 있습니다.

Azure는 *확장*을 활용하여 응용 프로그램 성능 모니터링(또는 *APM*)을 지원합니다.*APM*)를 활용 하 여*확장*형식)으로 바꿔야 합니다. 이 확장은 응용 프로그램에 설치되며 데이터를 수집하고 모니터링 서비스로 다시 보고합니다. 

## 확장 추가

1. 그런 다음**찾아보기****찾아보기**를 클릭하고 계측할 웹 앱이나 가상 컴퓨터를 선택합니다.

2. 링크를 클릭합니다.**응용 프로그램 모니터링**아래의 바둑판식으로 배열**모니터링**형식)으로 바꿔야 합니다.

3. 와 같은 사용 하려는 확장 공급자를 선택 합니다.**Application Insights**또는**New Relic**형식)으로 바꿔야 합니다.

![웹 앱](./media/insights-perf-analytics/05-extend.png)

또는 가상 컴퓨터를 사용 하는 경우:

![가상 컴퓨터](./media/insights-perf-analytics/10-vm1.png)


## 코드 탐색

일부 원격 분석을 생성 하는 동안 응용 프로그램을 사용 합니다.

1. 그런 다음, 웹 앱 또는 가상 컴퓨터 블레이드에서 설치된 확장을 표시합니다.
2. 해당 공급자에 게 응용 프로그램 탐색을 나타내는 행을 클릭 합니다.![새로 고침 클릭](./media/insights-perf-analytics/06-overview.png)

사용할 수 있습니다.**찾아보기**Application Insights 구성 요소 또는 사용한 New Relic 계정에 직접 이동 합니다.

Application Insights는 블레이드로 돌아가면 등 수행할 수 있습니다:-열기 성능:

![Application Insights 개요 블레이드에서 성능 타일을 클릭 합니다.](./media/insights-perf-analytics/07-dependency.png)

- 드릴스루 하려면 개별 요청을 참조 하십시오.

![눈금에서 관련된 요청을 확인할 종속성을 클릭 합니다.](./media/insights-perf-analytics/08-requests.png)

- SQL 호출 및 평균 지속 시간 및 표준 편차와 같은 관련 된 통계의 수를 포함 하 여 SQL 종속성에 소요 된 시간을 보여주는 예제는 다음과 같습니다. 

![](./media/insights-perf-analytics/01-example.png) 

### 특정 응용 프로그램 유형에 대한 추가 요구 사항

모든 추가 계측 없이 new Relic를 자동으로 설치할 수 있지만 Application Insights에 하나의 추가 요구 사항이.

![새 프로젝트 대화 상자에서 Application Insights를 선택합니다.](./media/insights-perf-analytics/03-add.png)

로그인을 묻는 면 Azure 계정에 대 한 자격 증명을 사용 합니다.

## 다음 단계

* [모니터 서비스 상태 메트릭](insights-how-to-customize-monitoring.md)웹 서비스가 사용 가능하며 응답할 수 있는 상태인지 확인합니다.
* [모니터링 및 진단을 사용 하도록 설정](insights-how-to-use-diagnostics.md)서비스에서 자세한 고주파 메트릭을 수집 합니다.
* [경고 알림 받기](insights-receive-alert-notifications.md)작업 이벤트의 발생 때마다 또는 메트릭 임계값을 초과 합니다.
* 사용[JavaScript 응용 프로그램 및 웹 페이지에 대 한 응용 프로그램 통찰력](application-insights/app-insights-web-track-usage.md)웹 페이지를 방문 하는 브라우저에 대 한 클라이언트 분석을 가져오려고 합니다.
* [웹 사이트의 가용성 및 응답성 모니터링](application-insights/app-insights-monitor-web-app-availability.md)Application Insights를 사용한 페이지 중인지 찾을 수 있도록 작동이 중단 되었습니다.
