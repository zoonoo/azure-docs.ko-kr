응용 프로그램별(즉, 계측 키별) 메트릭 및 이벤트의 수에 몇 가지 제한이 있습니다. 

선택하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/application-insights/)에 따라 제한됩니다.

| **리소스** | **기본 제한** | **참고**
| --- | --- | --- |
| 일당 총 데이터 | 100GB | 한도를 설정하여 줄일 수 있습니다. 추가할 필요가 있으면 AIDataCap@microsoft.com으로 메일을 보내세요. 
| 월당 무료 데이터<br/> (기본 가격 요금제) | 1 GB | GB당 요금이 청구되는 추가 데이터
| 데이터 보존 | 90일 | [검색](../articles/application-insights/app-insights-diagnostic-search.md), [분석](../articles/application-insights/app-insights-analytics.md) 및 [메트릭 탐색기](../articles/application-insights/app-insights-metrics-explorer.md)의 경우
| [가용성 다단계 테스트](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) 자세한 결과 보존 | 90일 | 각 단계의 자세한 결과
| [속성](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) 및 [메트릭](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> 이름 수 | 200 | 
| 속성 및 메트릭 이름 길이 | 150 |
| 속성 값 문자열 길이 | 8192 |
| 속성에 대한 고유 값<sup>3, 4</sup> | 100 | >100 => 메트릭 탐색기에서 필터로 속성을 사용할 수 없습니다
| 추적 및 예외 메시지 길이 | 10000 |
| 앱당 [가용성 테스트](../articles/application-insights/app-insights-monitor-web-app-availability.md) 수  | 10 |

1. 이러한 모든 숫자는 계측 키 기준입니다.
2. 메트릭 이름은 기타 Track*() 호출의 TrackMetric 및 측정 매개 변수에서 정의됩니다. 메트릭 이름은 계측 키 기준으로 전역적입니다.
3. 속성은 필터링 및 그룹화에만 사용할 수 있으며 각 속성에 허용되는 고유한 값은 100개 미만입니다. 고유 값 수가 100개를 초과하는 경우 속성을 계속 검색할 수는 있지만 필터 또는 그룹화에는 더 이상 사용할 수 없습니다.
4. 요청 이름 및 페이지 URL 같은 표준 속성은 일주일에 고유한 값 1000개로 제한됩니다. 고유한 값이 1000개를 초과할 경우 초과하는 값이 "기타 값"으로 표시됩니다. 원래 값을 전체 텍스트 검색 및 필터링에 계속 사용할 수 있습니다.


[Application Insights의 가격 책정 및 할당량 정보](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Dec16_HO3-->


