<properties 
	pageTitle="응용 프로그램 분석 - Application Insights에 대한 강력한 검색 도구" 
	description="Application Insights에 대한 강력한 검색 도구인 응용 프로그램 분석의 개요입니다." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Application Insights 분석: 언어 개요


[Application Insights 분석](app-analytics.md)은 [Application Insights](app-insights-overview.md) 원격 분석을 위한 강력한 쿼리 엔진입니다. 이 페이지에서는 Application Insights 쿼리 언어인 AIQL에 대해 설명합니다.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
일반적인 AIQL 쿼리는 *원본* 테이블이며 그 뒤에 `|`로 구분된 일련의 *연산자*가 있습니다.

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

언어에는 SQL과 같은 기능이 많이 있습니다. SQL과 마찬가지로 데이터를 필터링하고 레코드를 그룹화하며 테이블을 정렬 및 조인할 수 있습니다. 필드에서 계산을 수행할 수도 있습니다. SQL과 달리 이러한 함수는 다양한 작업으로 구분되며 중첩 쿼리 대신 다음 하나의 작업에서 매우 직관적인 방식으로 데이터를 파이프합니다. 이렇게 하면 상당히 복잡한 쿼리를 쉽게 작성할 수 있습니다.


>[AZURE.NOTE] [언어 둘러보기](app-analytics-tour.md)로 시작하는 것이 좋습니다.


## Application Insights 데이터에 연결


Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md)에서 분석을 엽니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->