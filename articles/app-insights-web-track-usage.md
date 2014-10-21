<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# 사용 현황 추적

## <a name="webclient"></a>웹 사용 현황 분석 설정

아직 [웹 프로젝트에 Application Insights를 추가][웹 프로젝트에 Application Insights를 추가]하지 않은 경우 지금 추가합니다.

## <a name="usage"></a>사용 현황 분석

응용 프로그램 개요 블레이드에는 다음과 같은 사용 현황 타일이 표시됩니다.

![][]

### 브라우저별 세션

*세션*은 사용자가 웹 사이트에서 페이지를 열 때 시작되고 30분의 제한 시간 동안 웹 요청을 보내지 않으면 끝나는 기간입니다.

클릭하면 차트를 확대할 수 있습니다.

### 상위 페이지 보기

지난 24시간 동안의 총 페이지 보기 횟수가 표시됩니다.

페이지 보기 타일을 클릭하면 보다 자세한 기록을 볼 수 있습니다.

![][1]

시간 범위를 클릭하면 보다 오랜 기간의 기록(최대 7일)을 볼 수 있습니다.

그래프를 클릭하면 표시할 수 있는 다른 메트릭을 볼 수 있습니다.

![][2]

## 사용자 지정 페이지 수

기본적으로는 새 페이지를 클라이언트 브라우저로 로드할 때마다 페이지 수가 계산됩니다. 그러나 추가 페이지 보기를 계산에 포함할 수도 있습니다. 예를 들어 탭에 콘텐츠가 표시되는 페이지에서 사용자가 탭을 전환할 때 페이지 수를 계산하도록 지정할 수 있습니다. 또는 페이지의 JavaScript 코드가 브라우저 URL은 변경하지 않고 새 콘텐츠를 로드할 수도 있습니다.

클라이언트 코드의 적절한 지점에 다음과 같은 JavaScript 호출을 삽입합니다.

    appInsights.trackPageView(myPageName);

페이지 이름은 URL과 같은 문자를 포함할 수 있지만 "\#" 또는 "?" 뒤의 모든 문자는 무시됩니다.

## 개별 페이지 보기 이벤트 검사

일반적으로 페이지 보기 원격 분석은 Application Insights에서 분석하며 모든 사용자에 대해 계산된 평균이 포함된 누적 보고서만 표시됩니다. 그러나 디버깅을 위해 개별 페이지 보기 이벤트를 확인할 수도 있습니다.

진단 검색 블레이드에서 필터를 페이지 보기로 설정합니다.

![][3]

보다 자세한 정보를 확인하려면 원하는 이벤트를 선택합니다.

> [WACOM.NOTE] [검색][검색]을 사용하는 경우 단어 단위를 사용해야 합니다. 예를 들어 "Abou"와 "bout"는 "About"과 일치하지 않지만 "Abou\* "는 일치합니다. 또한 검색 용어는 와일드로 시작할 수 없습니다. 예를 들어 "\*bou"로 검색해도 "About"는 검색되지 않습니다.

> [진단 검색에 대해 자세히 알아보기][검색]

## 사용 현황 추적

> [AZURE.VIDEO tracking-usage-with-application-insights]

## 자세한 정보

-   [Application Insights - 시작][웹 프로젝트에 Application Insights를 추가]
-   [라이브 웹 서버 모니터링][라이브 웹 서버 모니터링]
-   [웹 응용 프로그램의 성능 모니터링][웹 응용 프로그램의 성능 모니터링]
-   [진단 로그 검색][검색]
-   [웹 테스트를 사용한 가용성 추적][웹 테스트를 사용한 가용성 추적]
-   [사용 현황 추적][사용 현황 추적]
-   [질문과 대답 및 문제 해결][질문과 대답 및 문제 해결]

<!--Link references-->

  [웹 프로젝트에 Application Insights를 추가]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [1]: ./media/appinsights/appinsights-49usage.png
  [2]: ./media/appinsights/appinsights-63usermetrics.png
  [3]: ./media/appinsights/appinsights-51searchpageviews.png
  [검색]: ../app-insights-search-diagnostic-logs/
  [라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
  [웹 응용 프로그램의 성능 모니터링]: ../app-insights-web-monitor-performance/
  [웹 테스트를 사용한 가용성 추적]: ../app-insights-monitor-web-app-availability/
  [사용 현황 추적]: ../app-insights-web-track-usage/
  [질문과 대답 및 문제 해결]: ../app-insights-troubleshoot-faq/
