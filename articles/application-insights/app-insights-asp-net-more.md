<properties 
	pageTitle="Application Insights를 최대한 활용하기" 
	description="Application Insights를 시작한 경우 다음 기능을 사용할 수 있습니다." 
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
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Application Insights의 추가 원격 분석

다음은 사용자가 [Visual Studio Application Insights](app-insights-overview.md)에서 사용해보지 않았을 것으로 생각되는 기능의 요약입니다. 이미 사용하기 [시작](app-insights-asp-net.md)했다고 가정하겠습니다. Application Insights를 사용하여 웹 응용 프로그램의 가용성, 성능 및 사용량을 모니터링할 수 있습니다. 앱의 성능 및 효과에 대한 생생한 피드백을 통해 충분한 정보를 바탕으로 각 개발 수명 주기의 디자인 방향을 결정할 수 있습니다.

## 추가 원격 분석

||
|---|---
|[**가용성 테스트**](app-insights-monitor-web-app-availability.md)<br/>전 세계에서 정기적으로 발생하는 웹앱 HTTP 요청을 전송합니다. 응답 속도가 느리거나 불안정한 경우 경고가 표시됩니다.| 
|[**종속성 호출**](app-insights-asp-net-dependencies.md)<br/>SQL 쿼리 및 REST 또는 기타 리소스에 대한 호출을 모니터링합니다.|
|[**예외**](app-insights-asp-net-exceptions.md)<br/>처리된 예외 및 처리되지 않은 예외를 계산하고, 스택 추적을 가져오고, 클릭하여 코드로 이동합니다.|
|[**웹 페이지**](app-insights-javascript.md)<br/>웹 페이지를 조정하여 페이지 사용 현황, 성능 및 AJAX 호출을 모니터링합니다.
|**호스트 성능: [Azure 진단](app-insights-azure-diagnostics.md), [Windows 성능 카운터](app-insights-web-monitor-performance.md)**<br/>CPU 사용량 및 기타 컨텍스트 메트릭을 참조하세요. |![](./media/app-insights-asp-net-more/04.png)
|[**SDK API - 사용자 지정 원격 분석**](app-insights-api-custom-events-metrics.md)<br/>서버 및 클라이언트 코드에서 앱의 성능 및 사용 현황을 보다 자세하게 표시할 수 있도록 자체 이벤트 및 메트릭을 보냅니다.|
|[**로그 통합**](app-insights-asp-net-trace-logs.md)<br/>로깅 프레임워크(예: Log4Net, NLog 또는 System.Diagnostics.Trace)를 사용하는 경우 다른 원격 분석과 함께 이러한 추적을 Application Insights로 전송하는 어댑터가 있습니다.|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>앱에서 SDK로부터 전송된 원격 분석을 필터링, 수정 또는 확장할 수 있습니다. |


## 강력한 분석 및 프레젠테이션

||
|---|---
|[**인스턴스 데이터에 대한 진단 검색**](app-insights-visual-studio.md)<br/>요청, 예외, 종속성 호출, 로그 추적 및 페이지 보기와 같은 이벤트를 검색하고 필터링할 수 있습니다. Visual Studio의 스택 추적에서 코드로 이동합니다.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**집계된 데이터에 대한 메트릭 탐색기**](app-insights-metrics-explorer.md)<br/>요청, 오류 및 예외의 비율과 응답 시간, 페이지 로드 시간과 같은 집계된 데이터를 탐색, 필터링 및 분할할 수 있습니다.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**대시보드**](app-insights-dashboards.md#dashboards)<br/>여러 리소스의 데이터를 매시업한 후 다른 사용자와 공유할 수 있습니다. 다중 구성 요소 응용 프로그램에서 사용하거나 단체방에 연속으로 표시하는 데 유용합니다. |![대시보드 샘플](./media/app-insights-asp-net/62.png)
|[**라이브 매트릭 스트림**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>새 빌드를 배포할 때 이러한 실시간에 가까운 성능 표시기를 확인하여 모든 항목이 예상대로 작동하는지 알 수 있습니다.|![분석 샘플](./media/app-insights-asp-net-more/050.png)
|[**분석**](app-insights-analytics.md)<br/>이 강력한 쿼리 언어를 사용하여 앱의 성능 및 사용 현황에 대한 까다로운 질문에 답변할 수 있습니다.|![분석 샘플](./media/app-insights-asp-net-more/010.png)
|[**자동 및 수동 경고**](app-insights-alerts.md)<br/>자동 경고는 앱의 일반적인 원격 분석 패턴에 맞게 조정되고, 일반적인 패턴을 벗어나는 항목이 있으면 트리거합니다. 특정 수준의 사용자 지정 또는 표준 메트릭에 대해 경고를 설정할 수도 있습니다.|![경고 샘플](./media/app-insights-asp-net-more/020.png)

## 데이터 관리

|||
|---|---|
|[**연속 내보내기**](app-insights-export-telemetry.md)<br/>원하는 방식으로 분석할 수 있도록 모든 원격 분석을 저장소에 복사합니다.|
|**데이터 액세스 API**<br/>곧 제공될 예정입니다.|
|[**샘플링**](app-insights-sampling.md)<br/>데이터 속도를 줄이고 가격 책정 계층 제한을 벗어나지 않도록 하는 데 도움이 됩니다.|![샘플링 타일](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0713_2016-->