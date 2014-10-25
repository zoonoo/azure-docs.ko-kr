<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 웹앱의 가용성 및 응답성 모니터

웹 응용 프로그램을 배포한 후 가용성 및 응답성을 모니터하도록 웹 테스트를 설정할 수 있습니다. Application Insights는 전 세계에서 정기적으로 웹 요청을 보내 응용 프로그램이 느리게 응답하거나 전혀 응답하지 않을 경우 알려줄 수 있습니다.

공용 인터넷에서 액세스 가능한 모든 HTTP 끝점에 대해 웹 테스트를 설정할 수 있습니다.


1.  [새 응용 프로그램 만들기][새 응용 프로그램 만들기]
+ [웹 테스트 설정][웹 테스트 설정]
+ [모니터 보고서 보기][모니터 보고서 보기]
+ [테스트 편집 또는 사용 안 함][테스트 편집 또는 사용 안 함]
+ [다음 단계][다음 단계]



## <a name="create"></a> 1. 새 응용 프로그램 만들기

이 웹앱에 대한 [사용량 및 상태를 이미 모니터링][사용량 및 상태를 이미 모니터링]하고 있으며 동일한 위치에서 가용성 데이터를 보려는 경우 이 단계를 건너뜁니다.

그러나 이러한 결과를 별도로 유지하려면 Microsoft Azure 미리 보기에 로그인하여 Application Insights에서 새로운 응용 프로그램을 만듭니다.

![새로 만들기 \> Application Insights][새로 만들기 \> Application Insights]

## <a name="setup"></a> 2. 웹 테스트 설정

Application Insights에 있는 응용 프로그램의 홈 블레이드에서, 빈 웹 테스트 타일을 클릭합니다.

![빈 가용성 테스트 클릭][빈 가용성 테스트 클릭]

> *이미 웹 테스트가 있습니까? 웹 테스트 타일까지 클릭해 간 다음 웹 테스트 추가를 선택합니다.*

테스트의 이름과 테스트할 URL을 입력합니다. URL은 공용 인터넷에서 볼 수 있어야 합니다.

![웹 사이트의 최소 URL 채우기][웹 사이트의 최소 URL 채우기]

- 테스트 위치는 서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트의 문제와 네트워크 문제를 구분할 수 있도록 2 - 3가지를 선택합니다. 3개 이하만 선택할 수 있습니다.

- 성공 기준 - 허용 가능한 HTTP 반환 코드를 지정합니다. 보통 200을 지정합니다.

    또한 모든 올바른 응답에서 찾을 수 있는 문자열을 지정할 수도 있습니다. 와일드카드 없는 일반 문자열이어야 합니다. 내용이 변경되면 업데이트해야 할 수 있습니다.

- 알림 - 기본적으로 15분 동안 오류가 반복되는 경우 알림이 전송됩니다. 그러나 더 민감하게 변경할 수 있으며, 알림을 받는 전자 메일 주소도 변경할 수 있습니다.

### 더 많은 URL 테스트

원하는 만큼 많은 URL에 대한 테스트를 더 추가할 수 있습니다. 예를 들어 홈페이지를 테스트할 수 있을 뿐 아니라 검색을 위한 URL을 테스트하여 데이터베이스가 실행되고 있는지 확인할 수 있습니다.

![웹 테스트 블레이드에서 추가를 선택합니다.][웹 테스트 블레이드에서 추가를 선택합니다.]


## <a name="monitor"></a> 3. 모니터 보고서 보기

1 - 2분 정도 지난 후 응용 프로그램 블레이드를 닫았다가 다시 엽니다. 이 버전에서는 새로 고침이 자동으로 수행되지 않습니다.

![홈 블레이드에 대한 요약 결과][홈 블레이드에 대한 요약 결과]

위의 스크린샷은 요약입니다. 이 응용 프로그램에 대해 몇 개의 웹 테스트를 정의한 경우 여기에서 모두 결합됩니다.

웹 테스트 블레이드를 클릭해 가면서 웹 테스트 목록을 표시합니다.

특정 웹 테스트를 엽니다.

![특정 웹 테스트 클릭][특정 웹 테스트 클릭]

특정 웹 테스트 블레이드에서 **실패한 테스트**로 스크롤하여 결과를 선택합니다.

![특정 웹 테스트 클릭][1]

결과는 실패 이유를 보여 줍니다.

![웹 테스트 실행 결과][웹 테스트 실행 결과]

자세한 내용을 보려면 결과 파일을 다운로드하여 Visual Studio에서 살펴봅니다.

## <a name="edit"></a> 4. 테스트 편집 또는 사용 안 함

개별 테스트를 열어서 편집하거나 사용하지 않도록 설정합니다.

![웹 테스트 편집 또는 사용 안 함][웹 테스트 편집 또는 사용 안 함]

서비스에 대한 유지 관리를 수행하는 동안 웹 테스트를 사용하지 않도록 설정할 수 있습니다.

## <a name="next"></a> 다음 단계

[진단 로그 검색][진단 로그 검색]

[문제 해결][문제 해결]

## <a name="next"></a> 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][사용량 및 상태를 이미 모니터링]
* [현재 라이브 웹 서버 모니터링][현재 라이브 웹 서버 모니터링]
* [Application Insights의 메트릭 탐색][Application Insights의 메트릭 탐색]
* [진단 로그 검색][진단 로그 검색]
* [웹 테스트로 가용성 추적][웹 테스트로 가용성 추적]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][문제 해결]


<!--Link references-->

[새 응용 프로그램 만들기]: #create
[웹 테스트 설정]: #setup
[모니터 보고서 보기]: #monitor
[테스트 편집 또는 사용 안 함]: #edit
[다음 단계]: #next
[사용량 및 상태를 이미 모니터링]: ../app-insights-monitor-application-health-usage/
[새로 만들기 \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
[빈 가용성 테스트 클릭]: ./media/appinsights/appinsights-12avail.png
[웹 사이트의 최소 URL 채우기]: ./media/appinsights/appinsights-13availChoices.png
[웹 테스트 블레이드에서 추가를 선택합니다.]: ./media/appinsights/appinsights-16anotherWebtest.png
[홈 블레이드에 대한 요약 결과]: ./media/appinsights/appinsights-14availSummary.png
[특정 웹 테스트 클릭]: ./media/appinsights/appinsights-15webTestList.png
[1]: ./media/appinsights/appinsights-17-availViewDetails.png
[웹 테스트 실행 결과]: ./media/appinsights/appinsights-18-availDetails.png
[웹 테스트 편집 또는 사용 안 함]: ./media/appinsights/appinsights-19-availEdit.png
[진단 로그 검색]: ../app-insights-search-diagnostic-logs/
[문제 해결]: ../app-insights-troubleshoot-faq/
[Application Insights]: ../app-insights-get-started/
[현재 라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
[Application Insights의 메트릭 탐색]: ../app-insights-explore-metrics/
[웹 테스트로 가용성 추적]: ../app-insights-monitor-web-app-availability/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
