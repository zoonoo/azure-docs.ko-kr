응용 프로그램별(즉, 계측 키별) 메트릭 및 이벤트의 수에 몇 가지 제한이 있습니다.

선택하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/application-insights/)에 따라 제한됩니다.

**리소스** | **기본 제한** | **최대 제한**
-------- | ------------- | -------------
월별 세션 데이터 요소<sup>1, 2</sup> | 무제한 | 
요청, 이벤트, 종속성, 추적, 예외 및 페이지 보기에 대한 월별 총 데이터 요소 | 500만 | 5,000만<sup>3</sup>
[추적 및 로그](../articles/application-insights/app-insights-search-diagnostic-logs.md) 데이터 속도 | 200dp/s | 500dp/s
[예외](../articles/application-insights/app-insights-asp-net-exceptions.md) 데이터 속도 | 50dp/s | 50dp/s
요청, 이벤트, 종속성 및 페이지 보기 원격 분석에 대한 총 데이터 속도 | 200dp/s | 500dp/s
[검색](../articles/application-insights/app-insights-diagnostic-search.md) 및 [분석](../articles/application-insights/app-insights-analytics.md)에 대한 원시 데이터 보존 | 7 일
[메트릭 탐색기](../articles/application-insights/app-insights-metrics-explorer.md)에 대한 집계 데이터 보존 | 90일
[속성](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) 이름 수 | 100 |
속성 이름 길이 | 150 | 
속성 값 길이 | 8192 | 
추적 및 예외 메시지 길이 | 10000 |
[메트릭](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) 이름 수 | 100 |
메트릭 이름 길이 | 150 | 
[가용성 테스트](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> 데이터 요소는 개별 메트릭 값 또는 이벤트로, 연결된 속성 및 측정 값이 있습니다.

<sup>2</sup> 세션 데이터 요소에서 세션의 시작 또는 종료를 기록하고 사용자 ID를 기록합니다.

<sup>3</sup> 5,000만이 넘는 추가 용량을 구매할 수 있습니다.
 
[Application Insights의 가격 책정 및 할당량 정보](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->