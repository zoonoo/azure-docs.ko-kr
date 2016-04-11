<properties 
	pageTitle="분석 - Application Insights의 강력한 검색 도구" 
	description="Application Insights의 강력한 진단 검색 도구인 분석의 개요입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>


# Application Insights의 분석


[분석](app-analytics.md)을 사용하면 [Application Insights](app-insights-overview.md)에서 수집된 앱의 원격 분석을 통해 유용한 쿼리를 실행할 수 있습니다. 다음 페이지에서는 해당 쿼리 언어를 설명합니다.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
일반적인 쿼리는 *원본* 테이블이며 그 뒤에 `|`로 구분된 일련의 *연산자*가 있습니다.

예를 들어 하이데라바드의 시민들이 하루 중 언제 웹앱을 시도하는지 살펴보겠습니다. 그리고 그곳에 있는 동안 해당 HTTP 요청에 반환되는 결과 코드를 알아보겠습니다.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

지난 30일 동안 하루 중 시간 단위로 그룹화하여 고유 클라이언트 IP 주소의 개수를 계산합니다.

다른 응답 코드의 결과를 누적하도록 선택하여 막대형 차트 프레젠테이션으로 결과를 표시해 보겠습니다.

![막대형 차트, X축 및 Y축 그리고 구분을 차례로 선택합니다.](./media/app-analytics/020.png)

이 앱은 하이데라바드에서 점심 시간 및 취침 시간에 가장 인기 있는 것 같습니다. (또한 이러한 500개 코드를 조사해야 합니다.)


또한 강력한 통계 작업이 있습니다.

![](./media/app-analytics/025.png)


언어에 다음과 같은 많은 유용한 기능이 있습니다.

* 사용자 지정 속성 및 메트릭을 포함하여 필드를 기준으로 원시 앱 원격 분석을 [필터링](app-analytics-queries.md)합니다.
* 다중 테이블 [조인](app-analytics-queries.md#join-operator) - 요청을 페이지 뷰, 종속성 호출, 예외 및 로그 추적과 상호 연결합니다.
* 강력한 통계 [집계](app-analytics-aggregations.md) 기능이 있습니다.
* 복잡한 쿼리에 대해 SQL만큼 강력하지만 훨씬 쉽게 중첩문 대신 하나의 기본 연산에서 다음 연산으로 데이터를 파이프합니다.
* 즉각적이고 강력하게 시각화합니다.



>[AZURE.NOTE] [언어 둘러보기](app-analytics-tour.md)로 시작하는 것이 좋습니다.




## Application Insights 데이터에 연결


Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md)에서 분석을 엽니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-analytics/001.png)


## 제한

현재, 쿼리 결과는 지난 주의 데이터로 제한됩니다.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->