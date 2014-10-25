<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 응용 프로그램의 상태 및 사용량 모니터

*Application Insights는 미리 보기 모드로 제공됩니다.*


응용 프로그램이 적절히 수행되며 사용자를 충족하는지 확인합니다. Application Insights는 모든 성능 문제를 경고해 주며 근본적인 원인을 찾아서 진단하도록 돕습니다. 사용자 활동을 추적하여 사용자의 요구에 맞게 앱을 조정할 수 있습니다.

Application Insights는 Windows Phone 앱, Windows 스토어 앱 및 온-프레미스로 또는 가상 컴퓨터에 호스트되는 웹 응용 프로그램뿐 아니라 Microsoft Azure 웹 사이트도 모니터할 수 있습니다.

[Visual Studio 업데이트 3][Visual Studio 업데이트 3] 및 [Microsoft Azure][Microsoft Azure]의 계정(무료 평가판 기간 있음)이 필요합니다.

1.  [Application Insights 추가][Application Insights 추가]
+ [프로젝트 실행][프로젝트 실행]
+ [모니터 데이터 보기][모니터 데이터 보기]
+ [응용 프로그램 배포][응용 프로그램 배포]
+ [다음 단계][다음 단계]

*또는 Visual Studio를 다시 배포하거나 사용하지 않으면서 기존 웹 서비스를 모니터하려는 경우 [서버에 에이전트를 설치][서버에 에이전트를 설치]할 수 있습니다.*

## <a name="add"></a> 1. Application Insights 추가

### 새 프로젝트인 경우...

Visual Studio 2013에서 새 프로젝트를 만들 때 Application Insights를 선택했는지 확인합니다.


![ASP.NET 프로젝트 만들기][ASP.NET 프로젝트 만들기]

처음인 경우 Microsoft Azure Preview 로그인을 제공하거나 등록하라는 메시지가 표시됩니다. 이 계정은 Visual Studio Online 계정과 다릅니다.

> Application Insights 옵션이 없습니까? Visual Studio 2013 업데이트 3을 사용하고 있으며, 웹, Windows 스토어 또는 Windows Phone 프로젝트를 만들고 있는지 확인합니다.

### ... 또는 기존 프로젝트가 있는 경우

새 프로젝트에 Application Insights를 추가하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다.

![Application Insights 추가 선택][Application Insights 추가 선택]


## <a name="run"></a> 2. 프로젝트 실행

F5를 사용하여 응용 프로그램을 실행하고 사용해 보세요. 여러 페이지를 열어보세요.

Visual Studio에서, 수신된 이벤트 수를 볼 수 있습니다.

![][]

## <a name="monitor"></a> 3. 모니터 데이터 보기

프로젝트에서 Application Insights를 엽니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.][프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.]


Application 상태 및 사용 현황 분석 타일에서 데이터를 찾습니다. 예를 들면 다음과 같습니다.

![클릭해 가면서 더 많은 데이터 보기][클릭해 가면서 더 많은 데이터 보기]

임의의 타일을 클릭하여 더 많은 세부 정보를 확인합니다. [타일 및 보고서에 대해 자세히 알아보세요.][타일 및 보고서에 대해 자세히 알아보세요.]

> [WACOM.NOTE] 많은 타일이 제한된 세부 정보를 이 미리 보기 버전으로 표시합니다.

## <a name="deploy"></a> 4. 응용 프로그램 배포

응용 프로그램을 배포하고 데이터 누적을 봅니다.


## <a name="next"></a> 다음 단계

[타일 및 보고서에 대해 자세히 알아보기][타일 및 보고서에 대해 자세히 알아보세요.]

[웹 테스트][웹 테스트]

[진단 로그 캡처 및 검색][진단 로그 캡처 및 검색]

[문제 해결][문제 해결]

## 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][프로젝트에 Application Insights 추가]
* [현재 라이브 웹 서버 모니터링][서버에 에이전트를 설치]
* [Application Insights의 메트릭 탐색][타일 및 보고서에 대해 자세히 알아보세요.]
* [진단 로그 검색][진단 로그 캡처 및 검색]
* [웹 테스트로 가용성 추적][웹 테스트]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][문제 해결]


<!--Link references-->

[Visual Studio 업데이트 3]: http://go.microsoft.com/fwlink/?LinkId=397827
[Microsoft Azure]: http://azure.com
[Application Insights 추가]: #add
[프로젝트 실행]: #run
[모니터 데이터 보기]: #monitor
[응용 프로그램 배포]: #deploy
[다음 단계]: #next
[서버에 에이전트를 설치]: ../app-insights-monitor-performance-live-website-now/
[ASP.NET 프로젝트 만들기]: ./media/appinsights/appinsights-01-vsnewp1.png
[Application Insights 추가 선택]: ./media/appinsights/appinsights-03-addExisting.png
[]: ./media/appinsights/appinsights-09eventcount.png
[프로젝트를 마우스 오른쪽 단추로 클릭하고 Azure 포털을 엽니다.]: ./media/appinsights/appinsights-04-openPortal.png
[클릭해 가면서 더 많은 데이터 보기]: ./media/appinsights/appinsights-05-usageTiles.png
[타일 및 보고서에 대해 자세히 알아보세요.]: ../app-insights-explore-metrics/
[웹 테스트]: ../app-insights-monitor-web-app-availability/
[진단 로그 캡처 및 검색]: ../app-insights-search-diagnostic-logs/
[문제 해결]: ../app-insights-troubleshoot-faq/
[Application Insights]: ../app-insights-get-started/
[프로젝트에 Application Insights 추가]: ../app-insights-monitor-application-health-usage/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
