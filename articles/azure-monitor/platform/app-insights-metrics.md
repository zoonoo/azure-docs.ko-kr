---
title: Azure 애플리케이션 Insights 로그 기반 메트릭 | Microsoft Docs
description: 이 문서에는 지원 되는 집계 및 차원이 포함 된 Azure 애플리케이션 Insights 메트릭이 나열 됩니다. 로그 기반 메트릭에 대 한 세부 정보에는 기본 Kusto 쿼리 문이 포함 됩니다.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 847c56faae61483813286c46190764327e287783
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887247"
---
# <a name="application-insights-log-based-metrics"></a>로그 기반 메트릭 Application Insights

Application Insights 로그 기반 메트릭을 사용 하 여 모니터링 되는 앱의 상태를 분석 하 고, 강력한 대시보드를 만들고, 경고를 구성할 수 있습니다. 메트릭에는 다음과 같은 두 종류가 있습니다.

* 장면 뒤의 [로그 기반 메트릭은](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) 저장 된 이벤트에서 [kusto 쿼리로](https://docs.microsoft.com/azure/kusto/query/) 변환 됩니다.
* [표준 메트릭은](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) 미리 집계 된 시계열으로 저장 됩니다.

*표준 메트릭은* 수집 중에 미리 집계 되므로 쿼리 시 성능이 향상 됩니다. 이렇게 하면 대시보드를 보다 효과적으로 선택 하 고 실시간 경고를 만들 수 있습니다. *로그 기반 메트릭에* 는 더 많은 차원이 있습니다 .이를 통해 데이터 분석 및 임시 진단에 대 한 상위 옵션을 사용할 수 있습니다. [메트릭 탐색기](metrics-getting-started.md)에서 로그 기반 및 표준 메트릭 간을 전환 하려면 [네임 스페이스 선택기](metrics-getting-started.md#create-your-first-metric-chart) 를 사용 합니다.

## <a name="interpret-and-use-queries-from-this-article"></a>이 문서의 쿼리 해석 및 사용

이 문서에서는 집계 및 차원이 지원 되는 메트릭을 나열 합니다. 로그 기반 메트릭에 대 한 세부 정보에는 기본 Kusto 쿼리 문이 포함 됩니다. 편의를 위해 각 쿼리는 시간 세분성, 차트 종류 및 때로는 수정할 필요 없이 Log Analytics의 쿼리 사용을 간소화 하는 차원을 분할 하는 경우에 대 한 기본값을 사용 합니다.

[메트릭 탐색기](metrics-getting-started.md)에서 동일한 메트릭을 그리면 기본값이 없습니다. 쿼리는 차트 설정에 따라 동적으로 조정 됩니다.

- 선택한 시간 **범위는** 추가 *where timestamp ...* 절로 변환 되어 선택한 시간 범위 에서만 이벤트를 선택 합니다. 예를 들어, 가장 최근 24 시간 동안의 데이터를 보여 주는 차트의 경우 쿼리는 *| where timestamp > 전 (24 h)* 을 포함 합니다.

- 선택한 **시간 세분성** 이 최종 요약에 배치 됩니다.  *bin (timestamp, [time 그레인을]) 절을 기준으로* 합니다.

- 선택한 모든 **필터** 차원이 추가 *where* 절로 변환 됩니다.

- 선택한 **분할 된 차트** 차원은 추가 요약 속성으로 변환 됩니다. 예를 들어 차트를 *위치*별로 분할 하 고 5 분 시간 세분성을 사용 하 여 그리면 *요약* 절 *이 요약 됩니다. bin (타임 스탬프, 5 m), 위치를 기준으로*합니다.

> [!NOTE]
> Kusto 쿼리 언어를 처음 접하는 경우에는 수정 하지 않고 Kusto 문을 복사 하 여 Log Analytics 쿼리 창에 붙여 넣는 작업을 시작 합니다. 기본 차트를 보려면 **실행** 을 클릭 합니다. 쿼리 언어의 구문을 이해 하기 시작할 때 약간의 수정 작업을 시작 하 고 변경의 영향을 확인할 수 있습니다. 사용자 고유의 데이터를 탐색 하는 것은 [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) 및 [Azure Monitor](../../azure-monitor/overview.md)의 모든 기능을 실현 하기 시작 하는 좋은 방법입니다.

## <a name="availability-metrics"></a>가용성 메트릭

가용성 범주의 메트릭을 사용 하면 전 세계에서 관찰 된 웹 응용 프로그램의 상태를 확인할 수 있습니다. 이 범주의 메트릭을 사용 하 여 시작 하도록 [가용성 테스트를 구성](../../azure-monitor/app/monitor-web-app-availability.md) 합니다.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>가용성 (availabilityResults/availabilityPercentage)
*가용성* 메트릭은 문제를 검색 하지 못한 웹 테스트 실행의 비율을 보여 줍니다. 가능한 최소값은 0 이며,이는 모든 웹 테스트 실행이 실패 했음을 나타냅니다. 100 값은 모든 웹 테스트 실행에서 유효성 검사 조건을 통과 했음을 의미 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|백분율|평균|실행 위치, 테스트 이름|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>가용성 테스트 기간 (availabilityResults/duration)

*가용성 테스트 기간* 메트릭은 웹 테스트를 실행 하는 데 걸린 시간을 보여 줍니다. [다단계 웹 테스트](../../azure-monitor/app/availability-multistep.md)의 경우 메트릭은 모든 단계의 총 실행 시간을 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|밀리초|평균, 최소값, 최대값|실행 위치, 테스트 이름, 테스트 결과

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>가용성 테스트 (availabilityResults/count)

*가용성 테스트* 메트릭은 Azure Monitor에 의해 실행 되는 웹 테스트 수를 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|개수|개수|실행 위치, 테스트 이름, 테스트 결과|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>브라우저 메트릭

브라우저 메트릭은 실제 최종 사용자 브라우저에서 Application Insights JavaScript SDK에 의해 수집 됩니다. 웹 앱을 사용 하 여 사용자의 경험에 대 한 유용한 정보를 제공 합니다. 브라우저 메트릭은 일반적으로 샘플링 되지 않습니다. 즉, 샘플링으로 기울일 수 있는 서버 쪽 메트릭과 비교 하 여 사용 숫자의 전체 자릿수를 제공 합니다.

> [!NOTE]
> 브라우저 메트릭을 수집 하려면 [Application Insights JAVASCRIPT SDK](../../azure-monitor/app/javascript.md)를 사용 하 여 응용 프로그램을 계측 해야 합니다.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>브라우저 페이지 로드 시간 (browserTimings/totalDuration)

|측정 단위|지원 되는 집계|미리 집계 된 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>클라이언트 처리 시간 (browserTiming/processingDuration)

|측정 단위|지원 되는 집계|미리 집계 된 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>페이지 로드 네트워크 연결 시간 (browserTimings/networkDuration)

|측정 단위|지원 되는 집계|미리 집계 된 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>수신 응답 시간 (browserTimings/receiveDuration)

|측정 단위|지원 되는 집계|미리 집계 된 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>전송 요청 시간 (browserTimings/sendDuration)

|측정 단위|지원 되는 집계|미리 집계 된 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

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

**오류의** 메트릭은 처리 요청, 종속성 호출 및 throw 된 예외에 대 한 문제를 보여 줍니다.

### <a name="browser-exceptions-exceptionsbrowser"></a>브라우저 예외 (예외/브라우저)

이 메트릭은 브라우저에서 실행 중인 응용 프로그램 코드에서 throw 된 예외 수를 반영 합니다. ```trackException()``` Application Insights API 호출로 추적 되는 예외만 메트릭에 포함 됩니다.

|측정 단위|지원 되는 집계|미리 집계 된 차원|참고 사항|
|---|---|---|---|
|개수|개수|없음|로그 기반 버전은 **Sum** 집계를 사용 합니다.|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>종속성 호출 오류 (종속성/실패)

실패 한 종속성 호출 수입니다.

|측정 단위|지원 되는 집계|미리 집계 된 차원|참고 사항|
|---|---|---|---|
|개수|개수|없음|로그 기반 버전은 **Sum** 집계를 사용 합니다.|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>예외 (예외/개수)

Application Insights에 대 한 예외를 기록할 때마다 SDK의 지 수 [예외 () 메서드가](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) 호출 됩니다. 예외 메트릭에는 로깅된 예외의 수가 표시 됩니다.

|측정 단위|지원 되는 집계|미리 집계 된 차원|참고 사항|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스, 장치 유형|로그 기반 버전은 **Sum** 집계를 사용 합니다.|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>실패 한 요청 (요청/실패)

*실패로*표시 된 추적 된 서버 요청 수입니다. 기본적으로 Application Insights SDK는 HTTP 응답 코드 5xx 또는 4xx를 반환한 각 서버 요청을 실패 한 요청으로 자동으로 표시 합니다. [사용자 지정 원격 분석 이니셜라이저에서](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)요청 원격 분석 항목의 *success* 속성을 수정 하 여이 논리를 사용자 지정할 수 있습니다.

|측정 단위|지원 되는 집계|미리 집계 된 차원|참고 사항|
|---|---|---|---|
|개수|개수|클라우드 역할 인스턴스, 클라우드 역할 이름, 실제 또는 가상 트래픽, 요청 성능, 응답 코드|로그 기반 버전은 **Sum** 집계를 사용 합니다.|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>서버 예외 (예외/서버)

이 메트릭은 서버 예외의 수를 표시 합니다.

|측정 단위|지원 되는 집계|미리 집계 된 차원|참고 사항|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스|로그 기반 버전은 **Sum** 집계를 사용 합니다.|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>성능 카운터

**성능 카운터** 범주의 메트릭을 사용 하 여 Application Insights에 [의해 수집 된 시스템 성능 카운터](../../azure-monitor/app/performance-counters.md)에 액세스할 수 있습니다.

### <a name="available-memory-performancecountersavailablememory"></a>사용 가능한 메모리 (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>예외 율 (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 요청 실행 시간 (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 요청 빈도 (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>응용 프로그램 큐의 HTTP 요청 (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>프로세스 CPU (performanceCounters/processCpuPercentage)

메트릭은 모니터링 되는 앱을 호스트 하는 프로세스에서 사용 되는 총 프로세서 용량의 크기를 보여 줍니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|백분율|평균, 최소값, 최대값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>프로세스 IO 요금 (performanceCounters/processIOBytesPerSecond)

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|초당 바이트 수|평균, 최소값, 최대값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>프로세스 전용 바이트 (performanceCounters/processPrivateBytes)

모니터링 되는 프로세스가 해당 데이터에 할당 한 공유 되지 않는 메모리의 양입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|바이트|평균, 최소값, 최대값|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>프로세서 시간 (performanceCounters/processorCpuPercentage)

모니터링 되는 서버 인스턴스에서 실행 중인 *모든* 프로세스의 CPU 사용량입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|백분율|평균, 최소값, 최대값|클라우드 역할 인스턴스

>[!NOTE]
> Azure 앱 Services에서 호스트 되는 응용 프로그램에는 프로세서 시간 메트릭을 사용할 수 없습니다. [프로세스 cpu](#process-cpu-performancecountersprocesscpupercentage) 메트릭을 사용 하 여 App Services에서 호스트 되는 웹 응용 프로그램의 cpu 사용률을 추적 합니다.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>서버 메트릭

### <a name="dependency-calls-dependenciescount"></a>종속성 호출 (종속성/개수)

이 메트릭은 종속성 호출 수를 기준으로 합니다.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>종속성 기간 (종속성/기간)

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

### <a name="server-requests-requestscount"></a>서버 요청 (요청/수)

이 메트릭은 웹 응용 프로그램에서 받은 들어오는 서버 요청 수를 반영 합니다.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>서버 응답 시간 (요청/기간)

이 메트릭은 서버가 들어오는 요청을 처리 하는 데 걸린 시간을 반영 합니다.

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

## <a name="usage-metrics"></a>사용 현황 메트릭

### <a name="page-view-load-time-pageviewsduration"></a>페이지 보기 로드 시간 (pageViews/duration)

이 메트릭은 페이지 보기 이벤트를 로드 하는 데 걸린 시간을 나타냅니다.

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

### <a name="page-views-pageviewscount"></a>페이지 보기 (pageViews/count)

TrackPageView () Application Insights API로 기록 된 페이지 보기 이벤트의 수입니다.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>세션 (세션/개수)

이 메트릭은 고유한 세션 Id의 수를 나타냅니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>추적 (추적/개수)

충돌 추적 () Application Insights API 호출로 기록 된 추적 문의 수입니다.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>사용자 (사용자/수)

응용 프로그램에 액세스 한 고유 사용자 수입니다. 이 메트릭의 정확도는 원격 분석 샘플링 및 필터링을 사용 하 여 상당한 영향을 받을 수 있습니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>사용자, 인증 됨 (사용자/인증 됨)

응용 프로그램에 인증 된 고유 사용자 수입니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
