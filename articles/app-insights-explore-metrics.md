<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 메트릭 탐색

아직 [웹 프로젝트를 Application Insights용으로 설정][웹 프로젝트를 Application Insights용으로 설정]하지 않은 경우 지금 그렇게 하세요.

다음은 Application Insights에서 응용 프로그램의 블레이드에 표시할 수 있는 보고서입니다. Visual Studio에서 해당 페이지로 이동하려면 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 열기를 선택합니다. Microsoft Azure Preview 시작 보드에서 이동하려면 찾아보기, Application Insights, 프로젝트를 차례로 선택합니다.


+ [응용 프로그램 상태][응용 프로그램 상태]
+ [사용 현황 분석][사용 현황 분석]
+ [다음 단계][다음 단계]


## <a name="health"></a> 응용 프로그램 상태

응용 프로그램 상태는 응용 프로그램 코드를 조정하여 모니터링합니다.

![][]

### 평균 응답 시간

웹 요청이 응용 프로그램에 들어가는 시점부터 응답이 반환되는 시점까지의 시간을 측정합니다.

지점은 이동 평균을 보여 줍니다. 요청이 너무 많은 경우 그래프에 명확한 최고나 하락 없이 평균에서 벗어나는 지점이 있을 수 있습니다.

일반적인 최고 지점을 찾습니다. 보통 요청이 증가할 때 응답 시간도 증가합니다. 증가가 비례하지 않을 경우 앱이 사용하는 서비스의 용량 또는 CPU와 같은 리소스 한도에 도달했을 수 있습니다.

### 요청

지정된 기간 내에 수신된 요청 수입니다. 이 요청 수와 다른 도표의 결과를 비교하여 부하가 달라질 때 앱의 동작을 알아봅니다.

### 웹 테스트

![][1]

[웹 테스트][웹 테스트]는 전 세계 Application Insights 서버에서 일정 간격으로 사용자의 서버로 전송된 웹 요청의 결과를 보여 줍니다.

결과가 요청 수에 따라 달라지는지 확인합니다.

[웹 테스트를 설정하는 방법][웹 테스트]

### 가장 느린 요청

![][2]

성능 조정이 필요할 수 있는 요청을 보여 줍니다.

### 진단 검색

![][3]

[진단 로깅을 설정][진단 로깅을 설정]하는 경우 클릭해 가면서 최신 이벤트를 봅니다.

### 실패한 요청

![][4]

확인할 수 없는 예외를 발생시킨 요청의 수


## <a name="usage"></a> 사용 현황 분석

![][5]

사용 현황 데이터는 서버에서 일부 가져오고 [웹 페이지의 스크립트][웹 프로젝트를 Application Insights용으로 설정]에서 일부 가져옵니다.

### 브라우저당 세션 수

*세션*은 사용자가 웹 사이트의 페이지를 열 때 시작되고 사용자가 30분의 시간 제한 기간 동안 웹 요청을 보내지 않은 후 종료되는 기간입니다.

클릭해 가면서 도표에 맞게 확장/축소합니다.

### 맨 위 페이지 보기

지난 24시간 내 총 수를 보여 줍니다.

클릭해 가면서 지난 주의 페이지 보기에 대한 그래프를 표시합니다.

### 타일 다시 정렬

![설정 선택, 사용자 지정][설정 선택, 사용자 지정]

## <a name="next"></a> 다음 단계

[웹 테스트 설정][웹 테스트]

[진단 로그 캡처 및 검색][진단 로깅을 설정]

[문제 해결][문제 해결]


## 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][웹 프로젝트를 Application Insights용으로 설정]
* [현재 라이브 웹 서버 모니터링][현재 라이브 웹 서버 모니터링]
* [Application Insights의 메트릭 탐색][Application Insights의 메트릭 탐색]
* [진단 로그 검색][진단 로깅을 설정]
* [웹 테스트로 가용성 추적][웹 테스트]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][문제 해결]


<!--Link references-->

[웹 프로젝트를 Application Insights용으로 설정]: ../app-insights-monitor-application-health-usage/
[응용 프로그램 상태]: #health
[사용 현황 분석]: #usage
[다음 단계]: #next
[]: ./media/appinsights/appinsights-42reqs.png
[1]: ./media/appinsights/appinsights-43webtests.png
[웹 테스트]: ../app-insights-monitor-web-app-availability/
[2]: ./media/appinsights/appinsights-44slowest.png
[3]: ./media/appinsights/appinsights-45diagnostic.png
[진단 로깅을 설정]: ../app-insights-search-diagnostic-logs/
[4]: ./media/appinsights/appinsights-46failed.png
[5]: ./media/appinsights/appinsights-47usage.png
[설정 선택, 사용자 지정]: ./media/appinsights/appinsights-21-customizeblade.png
[문제 해결]: ../app-insights-troubleshoot-faq/
[Application Insights]: ../app-insights-get-started/
[현재 라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
[Application Insights의 메트릭 탐색]: ../app-insights-explore-metrics/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
