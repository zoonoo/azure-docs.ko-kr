응용 프로그램별(즉, 계측 키별) 메트릭 및 이벤트의 수에 몇 가지 제한이 있습니다.

선택하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/application-insights/)에 따라 제한됩니다.

**리소스** | **기본 제한** | **최대 제한**
-------- | ------------- | -------------
월별 세션 데이터 요소<sup>1</sup> | 무제한 | 
월별 기타 데이터 요소 | 500만 | 5,000만<sup>2</sup>
[추적 또는 로그](../articles/application-insights/app-insights-search-diagnostic-logs.md) 데이터 속도 | 200dp/s | 500dp/s
[예외](../articles/application-insights/app-insights-asp-net-exceptions.md) 데이터 속도 | 50dp/s | 50dp/s
기타 원격 분석 데이터 속도 | 200dp/s | 500dp/s
[원시 데이터](../articles/application-insights/app-insights-diagnostic-search.md) 보존 | 7 일
[집계된 데이터](../articles/application-insights/app-insights-metrics-explorer.md) 보존 | 90일
[속성](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) 이름 수 | 100 |
속성 이름 길이 | 100 | 
속성 값 길이 | 1000 | 
추적 및 예외 메시지 길이 | 10000 |
[메트릭](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) 이름 수 | 100 |
메트릭 이름 길이 | 100 | 
[가용성 테스트](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> 데이터 요소는 개별 메트릭 값 또는 이벤트로, 연결된 속성 및 측정 값이 있습니다.

<sup>2</sup> 5,000만 이상의 추가 용량을 구매할 수 있습니다.
 
[Application Insights의 가격 책정 및 할당량 정보](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0427_2016-->