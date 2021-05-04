---
title: Azure Application Insights 로그 기반 메트릭 | Microsoft Docs
description: 이 문서에는 지원되는 집계 및 차원이 포함된 Azure Application Insights 메트릭이 나열됩니다. 로그 기반 메트릭에 대한 세부 정보에는 기본 Kusto 쿼리 문이 포함됩니다.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.openlocfilehash: bae7980137c1da5e1755450863af4f9029d6ed62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583465"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights 로그 기반 메트릭

Application Insights 로그 기반 메트릭을 사용하여 모니터링되는 앱의 상태를 분석하고, 강력한 대시보드를 만들고, 경고를 구성할 수 있습니다. 두 종류의 메트릭이 있습니다.

* 뒤에 있는 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)은 저장된 이벤트에서 [Kusto 쿼리](/azure/kusto/query/)로 변환됩니다.
* [표준 메트릭](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)은 미리 집계된 시계열로 저장됩니다.

‘표준 메트릭’은 수집 중에 미리 집계되므로 쿼리 시 성능이 향상됩니다. 대시보드를 위한 더 효과적인 선택이며 실시간 경고를 만들 수 있습니다. ‘로그 기반 메트릭’에는 더 많은 차원이 있으며 이로 인해 데이터 분석 및 임시 진단을 위한 우수한 옵션이 됩니다. [메트릭 탐색기](./metrics-getting-started.md)에서 로그 기반 및 표준 메트릭 사이를 전환하려면 [네임스페이스 선택기](./metrics-getting-started.md#create-your-first-metric-chart)를 사용합니다.

## <a name="interpret-and-use-queries-from-this-article"></a>이 문서의 쿼리 해석 및 사용

이 문서는 집계 및 차원이 지원되는 메트릭을 나열합니다. 로그 기반 메트릭에 대한 세부 정보에는 기본 Kusto 쿼리 문이 포함됩니다. 편의를 위해 각 쿼리는 시간 세분성, 차트 종류, 때로는 수정할 필요 없이 Log Analytics의 쿼리 사용을 간소화하는 차원 분할에 대한 기본값을 사용합니다.

[메트릭 탐색기](./metrics-getting-started.md)에서 동일한 메트릭을 그릴 경우 기본값이 없습니다. 쿼리는 차트 설정에 따라 동적으로 조정됩니다.

- 선택한 **시간 범위** 는 선택한 시간 범위에서만 이벤트를 선택하도록 추가 *where timestamp...* 절로 변환됩니다. 예를 들어, 가장 최근 24시간 동안의 데이터를 보여 주는 차트의 경우 쿼리는 *| where timestamp > ago(24 h)* 을 포함합니다.

- 선택한 **시간 세분성** 이 최종 *summarize ... by bin(timestamp, [time grain])* 절에 배치됩니다.

- 선택한 **필터** 차원이 추가 *where* 절로 변환됩니다.

- 선택한 **분할 차트** 차원은 추가 요약 속성으로 변환됩니다. 예를 들어 차트를 ‘위치’로 분할하고 5분 단위로 그리면 *summarize* 문은 *... by bin(timestamp, 5 m), location* 로 요약됩니다.

> [!NOTE]
> Kusto 쿼리 언어를 처음 사용하는 경우 Kusto 문을 수정하지 않고 복사하여 Log Analytics 쿼리 창에 붙여넣어 시작합니다. 기본 차트를 보려면 **실행** 을 클릭합니다. 쿼리 언어의 구문을 이해하기 시작하면 약간씩 수정하면서 변경 사항의 효과를 확인할 수 있습니다. 사용자 고유의 데이터를 탐색하는 것은 [Log Analytics](../logs/log-analytics-tutorial.md)와 [Azure Monitor](../overview.md)의 모든 기능을 활용하는 좋은 방법입니다.

## <a name="availability-metrics"></a>가용성 메트릭

가용성 범주의 메트릭을 사용하면 전 세계의 포인트에서 관찰된 웹 애플리케이션의 상태를 확인할 수 있습니다. 이 범주의 메트릭을 사용하려면 [가용성 테스트를 구성](../app/monitor-web-app-availability.md)합니다.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>가용성(availabilityResults/availabilityPercentage)
‘가용성 메트릭’은 문제를 찾지 못한 웹 테스트 실행의 비율을 보여 줍니다. 가능한 최솟값은 0이며, 이는 모든 웹 테스트 실행이 실패했음을 나타냅니다. 100 값은 모든 웹 테스트 실행이 유효성 검사 조건을 통과했음을 의미합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|백분율|평균|실행 위치, 테스트 이름|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>가용성 테스트 기간(availabilityResults/duration)

‘가용성 테스트 기간’ 메트릭은 웹 테스트를 실행하는 데 걸린 시간을 보여 줍니다. [다단계 웹 테스트](../app/availability-multistep.md)의 경우 메트릭은 모든 단계의 총 실행 시간을 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|밀리초|평균, 최솟값, 최댓값|실행 위치, 테스트 이름, 테스트 결과

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>가용성 테스트(availabilityResults/count)

‘가용성 테스트’ 메트릭은 Azure Monitor에 의한 웹 테스트 실행 수를 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|개수|개수|실행 위치, 테스트 이름, 테스트 결과|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>브라우저 메트릭

브라우저 메트릭은 실제 최종 사용자 브라우저에서 Application Insights JavaScript SDK에 의해 수집됩니다. 웹앱과 관련된 사용자의 경험에 대한 유용한 정보를 제공합니다. 브라우저 메트릭은 일반적으로 샘플링되지 않습니다. 즉, 샘플링으로 인해 왜곡될 수 있는 서버 측 메트릭과 비교할 때 사용량의 정확도가 높습니다.

> [!NOTE]
> 브라우저 메트릭을 수집하려면 [Application Insights JavaScript SDK](../app/javascript.md)로 애플리케이션을 계측해야 합니다.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>브라우저 페이지 로드 시간(browserTimings/totalDuration)

사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.

|측정 단위|지원되는 집계|미리 집계된 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>클라이언트 처리 시간(browserTiming/processingDuration)

DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.

|측정 단위|지원되는 집계|미리 집계된 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>페이지 로드 네트워크 연결 시간(browserTimings/networkDuration)

사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.

|측정 단위|지원되는 집계|미리 집계된 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>수신 응답 시간(browserTimings/receiveDuration)

첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.

|측정 단위|지원되는 집계|미리 집계된 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>요청 전송 시간(browserTimings/sendDuration)

네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.

|측정 단위|지원되는 집계|미리 집계된 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>오류 메트릭

**오류** 의 메트릭은 요청 처리, 종속성 호출, throw된 예외에 대한 문제를 보여 줍니다.

### <a name="browser-exceptions-exceptionsbrowser"></a>브라우저 예외(exceptions/browser)

이 메트릭은 브라우저에서 실행 중인 애플리케이션 코드에서 throw된 예외의 수를 반영합니다. ```trackException()``` Application Insights API 호출로 추적되는 예외만이 메트릭에 포함됩니다.

|측정 단위|지원되는 집계|미리 집계된 차원|메모|
|---|---|---|---|
|개수|개수|없음|로그 기반 버전은 **Sum** 집계를 사용합니다.|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>종속성 호출 실패(dependencies/failed)

실패한 종속성 호출 수입니다.

|측정 단위|지원되는 집계|미리 집계된 차원|메모|
|---|---|---|---|
|개수|개수|없음|로그 기반 버전은 **Sum** 집계를 사용합니다.|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>예외(exceptions/count)

Application Insights에 대한 예외를 기록할 때마다 SDK의 [trackException() method](../app/api-custom-events-metrics.md#trackexception)에 대한 호출이 발생합니다. 예외 메트릭에는 로깅된 예외의 수가 표시됩니다.

|측정 단위|지원되는 집계|미리 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스, 디바이스 유형|로그 기반 버전은 **Sum** 집계를 사용합니다.|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>실패한 요청(requests/failed)

추적된 서버 요청 중 ‘실패’로 표시된 요청의 수입니다. 기본적으로 Application Insights SDK는 HTTP 응답 코드 5xx 또는 4xx를 반환한 각 서버 요청을 실패한 요청으로 자동으로 표시합니다. [사용자 지정 원격 분석 이니셜라이저](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)에서 요청 원격 분석 항목의 ‘성공’ 속성을 수정하여 이 논리를 사용자 지정할 수 있습니다.

|측정 단위|지원되는 집계|미리 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 인스턴스, 클라우드 역할 이름, 실제 또는 가상 트래픽, 요청 성능, 응답 코드|로그 기반 버전은 **Sum** 집계를 사용합니다.|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>서버 예외(exceptions/server)

이 메트릭은 서버 예외의 수를 표시합니다.

|측정 단위|지원되는 집계|미리 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스|로그 기반 버전은 **Sum** 집계를 사용합니다.|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>성능 카운터

**성능 카운터** 범주의 메트릭을 사용하여 [Application Insights에 의해 수집된 시스템 성능 카운터](../app/performance-counters.md)에 액세스합니다.

### <a name="available-memory-performancecountersavailablememory"></a>사용 가능한 메모리(performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>예외율(performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 요청 실행 시간(performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 요청 속도(performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>애플리케이션 큐의 HTTP 요청(performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU 프로세스(performanceCounters/processCpuPercentage)

메트릭은 모니터링되는 앱을 호스트하는 프로세스에 사용되는 총 프로세서 용량을 보여 줍니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최솟값, 최댓값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>프로세스 IO 속도(performanceCounters/processIOBytesPerSecond)

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|초당 바이트|평균, 최솟값, 최댓값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>프로세스 프라이빗 바이트(performanceCounters/processPrivateBytes)

공유되지 않는 메모리 중 모니터링되는 프로세스가 해당 데이터에 할당한 메모리의 양입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|바이트|평균, 최솟값, 최댓값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>프로세서 시간(performanceCounters/processorCpuPercentage)

모니터링되는 서버 인스턴스에서 실행 중인 ‘모든’ 프로세스의 CPU 소비량입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최솟값, 최댓값|클라우드 역할 인스턴스

>[!NOTE]
> Azure App Services에서 호스트되는 애플리케이션에는 프로세서 시간 메트릭을 사용할 수 없습니다. App Services에서 호스트되는 웹 애플리케이션의 CPU 사용률을 추적하려면 [CPU 프로세스](#process-cpu-performancecountersprocesscpupercentage) 메트릭을 사용하세요.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>서버 메트릭

### <a name="dependency-calls-dependenciescount"></a>종속성 호출(dependencies/count)

이 메트릭은 종속성 호출 수와 관련이 있습니다.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>종속성 기간(dependencies/duration)

이 메트릭은 종속성 호출의 기간을 나타냅니다.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>서버 요청(requests/count)

이 메트릭은 웹 애플리케이션에서 받은 수신 서버 요청 수를 반영합니다.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>서버 응답 시간(requests/duration)

이 메트릭은 서버가 수신 요청을 처리하는 데 걸린 시간을 반영합니다.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>사용량 메트릭

### <a name="page-view-load-time-pageviewsduration"></a>페이지 보기 로드 시간(pageViews/duration)

이 메트릭은 페이지 보기 이벤트를 로드하는 데 걸린 시간을 나타냅니다.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>페이지 보기(pageViews/count)

TrackPageView() Application Insights API로 기록된 페이지 보기 이벤트의 수입니다.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>세션(sessions/count)

이 메트릭은 고유한 세션 ID의 수를 나타냅니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>추적(traces/count)

TrackTrace() Application Insights API 호출로 기록된 추적 문의 수입니다.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>사용자(users/count)

애플리케이션에 액세스한 고유 사용자 수입니다. 이 메트릭의 정확도는 원격 분석 샘플링 및 필터링 사용에 상당한 영향을 받을 수 있습니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>사용자, 인증됨(users/authenticated)

애플리케이션에 인증된 고유 사용자 수입니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```