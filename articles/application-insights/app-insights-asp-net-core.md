<properties 
	pageTitle="ASP.NET Core용 Application Insights" 
	description="응용 프로그램의 가용성, 성능 및 사용 현황을 모니터링합니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="awills"/>

# ASP.NET Core용 Application Insights

[Visual Studio Application Insights](app-insights-overview.md)를 사용하여 웹 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

![예](./media/app-insights-asp-net-core/sample.png)

[Microsoft Azure](http://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, XBox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다. 팀에서 Azure를 단체 구독할 수도 있습니다. 소유자에게 Microsoft 계정을 사용하여 추가해 달라고 요청하세요.


## 시작

[시작 가이드](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)를 따르세요.

## Application Insights 사용

[Microsoft Azure 포털](https://portal.azure.com)에 로그인하고 앱을 모니터링하려고 만든 리소스로 이동합니다.

별도의 브라우저 창에서 잠시 앱을 사용합니다. Application Insights 차트에 데이터가 표시되는 것을 볼 수 있습니다. (새로 고침을 클릭해야 할 수 있습니다.) 개발하는 동안은 소량의 데이터만 표시되지만, 앱을 게시하고 많은 사용자가 확보되면 차트가 흥미로워집니다.

개요 페이지에는 가장 관심을 가질만한 성능 차트(예: 서버 응답 시간, 페이지 로드 시간, 실패한 요청의 수)가 표시됩니다. 차트와 데이터를 더 보려면 아무 차트나 클릭합니다.

포털의 보기는 두 가지 주요 범주에 따릅니다.

* [메트릭 탐색기](app-insights-metrics-explorer.md)에는 응답 시간, 실패율 또는 사용자가 [API](app-insights-api-custom-events-metrics.md)로 만든 메트릭 및 개수의 테이블과 그래프가 표시됩니다. 속성 값에 따라 데이터를 필터링하고 분할하여 앱 및 사용자에 대한 이해를 향상시킵니다.
* [검색 탐색기](app-insights-diagnostic-search.md)에는 특정 요청, 예외, 로그 추적 또는 [API](app-insights-api-custom-events-metrics.md)로 만든 이벤트와 같은 개별적인 이벤트가 나열됩니다. 이벤트를 필터링하고 검색하여 관련된 이벤트를 탐색하여 문제를 조사합니다.
* [분석](app-insights-analytics.md)은 원격 분석에 대해 SQL 유사 쿼리를 실행할 수 있도록 하는 강력한 분석 및 진단 도구입니다.

## 경고

* 실패한 요청 속도로 나타난 비정상적인 변경에 대해 알려주는 [적응 경고](app-insights-nrt-proactive-diagnostics.md)가 자동으로 제공됩니다.
* 전 세계에서 지속적으로 웹 사이트를 테스트하고 테스트가 실패하면 즉시 전자 메일을 받으려면 [가용성 테스트](app-insights-monitor-web-app-availability.md)를 설정합니다.
* 응답 시간 또는 예외 속도 같은 메트릭이 허용 한도를 벗어나는지 파악하려면 [메트릭 경고](app-insights-monitor-web-app-availability.md)를 설정합니다.

## 더 많은 원격 분석 사용

* [웹 페이지에 원격 분석을 추가](app-insights-javascript.md)하여 페이지 사용 현황 및 성능을 모니터링합니다.
* REST, SQL 또는 기타 외부 리소스로 인해 속도가 늦어지는지를 보려면 [종속성을 모니터링](app-insights-dependencies.md)합니다.
* 앱의 성능 및 사용 현황을 보다 자세하게 표시하기 위해 사용자의 이벤트와 메트릭을 보내려면 [API를 사용](app-insights-api-custom-events-metrics.md)합니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md)는 사용자의 앱을 전 세계에서 지속적으로 확인합니다.


## 공개 소스

[코드를 읽고 참여하기](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

<!---HONumber=AcomDC_0907_2016-->