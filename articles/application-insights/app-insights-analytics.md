---
title: "Analytics - Azure Application Insights의 강력한 검색 도구 | Microsoft Docs"
description: "Application Insights의 강력한 진단 검색 도구인 분석의 개요입니다. "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 717269a2c0e0b1a3d332e627154eacd2d2c5001e
ms.contentlocale: ko-kr
ms.lasthandoff: 04/12/2017


---
# <a name="analytics-in-application-insights"></a>Application Insights의 분석
[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다. 

* **[소개 비디오 보기](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, **[시뮬레이션된 데이터에 대한 분석을 테스트](https://analytics.applicationinsights.io/demo)**합니다.
* **[SQL 사용자 치트 시트](https://aka.ms/sql-analytics)**에서는 가장 일반적인 코드를 변환합니다.
* **[언어 참조](app-insights-analytics-reference.md)** 분석 쿼리 언어의 모든 강력한 기능을 사용하는 방법을 알아봅니다.


## <a name="queries-in-analytics"></a>분석의 쿼리
일반적인 쿼리는 *원본* 테이블이며 그 뒤에 `|`로 구분된 일련의 *연산자*가 있습니다. 

예를 들어 하이데라바드의 시민들이 하루 중 언제 웹앱을 시도하는지 살펴보겠습니다. 그리고 그곳에 있는 동안 해당 HTTP 요청에 반환되는 결과 코드를 알아보겠습니다. 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

지난 7일 동안 하루 중 시간 단위로 그룹화하여 고유 클라이언트 IP 주소의 개수를 계산합니다. 

다른 응답 코드의 결과를 누적하도록 선택하여 막대형 차트 프레젠테이션으로 결과를 표시해 보겠습니다.

![막대형 차트, X축 및 Y축 그리고 구분을 차례로 선택합니다.](./media/app-insights-analytics/020.png)

이 앱은 하이데라바드에서 점심 시간 및 취침 시간에 가장 인기 있는 것 같습니다. (또한 이러한 500개 코드를 조사해야 합니다.)

또한 강력한 통계 작업이 있습니다.

![](./media/app-insights-analytics/025.png)

언어에 다음과 같은 많은 유용한 기능이 있습니다.


* [필터링](app-insights-analytics-reference.md#where-operator) 합니다.
* [조인](app-insights-analytics-reference.md#join-operator) - 요청을 페이지 뷰, 종속성 호출, 예외 및 로그 추적과 상호 연결합니다.
* 강력한 통계 [집계](app-insights-analytics-reference.md#aggregations)기능이 있습니다.
* 복잡한 쿼리에 대해 SQL만큼 강력하지만 훨씬 쉽게 중첩문 대신 하나의 기본 연산에서 다음 연산으로 데이터를 파이프합니다.
* 즉각적이고 강력하게 시각화합니다.
* [Azure 대시보드에 차트를 고정](app-insights-analytics-using.md#pin-to-dashboard)합니다.
* [Power BI로 쿼리를 내보냅니다](app-insights-analytics-using.md#export-to-power-bi).
* 예를 들어 Powershell에서 프로그래밍 방식으로 쿼리를 실행하는 데 사용할 수 있는 [REST API](https://dev.applicationinsights.io/)가 있습니다.


## <a name="connect-to-your-application-insights-data"></a>Application Insights 데이터에 연결
Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md) 에서 분석을 엽니다. 

![portal.azure.com을 열고 Application Insights 리소스를 열고 Analytics를 클릭합니다.](./media/app-insights-analytics/001.png)


## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>다음 단계
* [언어 둘러보기](app-insights-analytics-tour.md)를 시작하는 것이 좋습니다. 

### <a name="query-examples"></a>쿼리 예제

* 다음 연습을 수행하여 분석의 유용한 기능을 알아보세요.
 1.    [요청 기간의 급증 및 단계별 증가 자동 진단](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 2.    [시계열 분석으로 성능 저하 분석](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 3.    [autocluster 및 diffpatterns로 응용 프로그램 오류 분석](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 4.    [시계열 분석으로 고급 셰이프 검색](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 5.    [슬라이딩 윈도우 작업을 사용하여 응용 프로그램 사용량 분석(롤링 MAU/DAU 등)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 6.    [디버그 로그 분석을 기준으로 서비스 중단 검색](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) 및 관련 블로그 게시물([여기](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics))
 7.    [간단한 디버그 로그를 사용하여 응용 프로그램 성능 프로파일링](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) 및 관련 블로그 게시물([여기](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/))
 8.    [간단한 디버그 로그를 사용하여 코드 흐름에서 각 단계의 지속 시간 측정](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) 및 관련 블로그 게시물([여기](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/))
 9.    [간단한 디버그 로그를 사용하여 동시성 분석](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) 및 관련 블로그 게시물([여기](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/))



