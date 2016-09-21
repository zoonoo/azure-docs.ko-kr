<properties 
	pageTitle="Application Insights에서 자동 관리 검색 | Microsoft Azure" 
	description="Application Insights는 앱 원격 분석의 자동 심층 분석을 수행하여 잠재적 성능 문제에 대해 경고합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="rakefetj" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="awills"/>

#  Application Insights에서 자동 관리 검색

 자동 관리 검색은 웹 응용 프로그램에서 잠재적인 성능 문제를 자동으로 경고해 줍니다. 앱에서 [Visual Studio Application Insights](app-insights-overview.md)로 보내는 원격 분석의 스마트 분석을 수행합니다. 실패율이나 클라이언트 또는 서버 성능의 비정상적인 패턴이 갑자기 증가하는 경우 경고가 발생합니다. 이 기능에는 구성이 필요하지 않습니다. 응용 프로그램에서 충분한 원격 분석을 보내는 경우 작동합니다.

사용자가 받은 전자 메일 또는 자동 관리 검색 블레이드에서 자동 관리 검색 경고에 액세스할 수 있습니다.



## 자동 관리 검색 검토

두 가지 방법으로 자동 관리 검색을 검색할 수 있습니다.

* Application Insights에서 **전자 메일을 받습니다**. 일반적인 예는 다음과 같습니다.

    ![전자 메일 경고](./media/app-insights-proactive-detection/03.png)

    포털에서 자세한 정보를 열려면 큰 단추를 클릭합니다.

* 앱의 개요 블레이드에 있는 **자동 관리 검색 타일**에 최근 경고 수가 표시됩니다. 최근 경고 목록을 보려면 타일을 클릭합니다.

![최근 검색 보기](./media/app-insights-proactive-detection/04.png)

경고를 클릭하여 세부 정보를 봅니다.


## 어떤 문제가 검색되나요?

세 가지 종류가 검색됩니다.

* [거의 실시간 실패 경고](app-insights-proactive-failure-diagnostics.md). 기계 학습을 사용하여 앱에 대해 실패한 요청의 예상 비율을 설정하고 로드 및 다른 요소와 상관 관계를 지정합니다. 실패율이 예상된 범위를 벗어나는 경우 경고를 보냅니다.
* [이상 진단](app-insights-proactive-anomaly-diagnostics.md). 응답 시간 및 실패율에서 비정상적인 패턴을 매일 검색합니다. 이러한 문제를 위치, 브라우저, 클라이언트 OS, 서버 인스턴스 및 하루 중 시간과 같은 속성과 상관 관계를 지정합니다.
* [Azure 클라우드 서비스](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). 앱이 Azure 클라우드 서비스에서 호스트되고 역할 인스턴스에 시작 실패, 빈번한 재활용 또는 런타임 충돌이 있는 경우 경고가 발생합니다.

(각 알림에서 도움말 링크를 통해 관련 문서로 이동할 수 있습니다.)


## 다음 단계

이러한 진단 도구를 사용하면 앱에서 원격 분석을 검사할 수 있습니다.

* [메트릭 탐색기](app-insights-metrics-explorer.md)
* [검색 탐색기](app-insights-diagnostic-search.md)
* [분석 - 강력한 쿼리 언어](app-insights-analytics-tour.md)

사전 감지는 완전히 자동으로 수행됩니다. 하지만 보다 많은 경고를 설정하고 싶을 수 있습니다.

* [수동으로 구성된 메트릭 경고](app-insights-alerts.md)
* [가용성 웹 테스트](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->