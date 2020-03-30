---
title: Azure 응용 프로그램 인사이트 로그 기반 메트릭 | 마이크로 소프트 문서
description: 이 문서에서는 지원되는 집계 및 차원이 있는 Azure 응용 프로그램 인사이트 메트릭을 나열합니다. 로그 기반 메트릭에 대한 세부 정보에는 기본 Kusto 쿼리 문이 포함됩니다.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664918"
---
# <a name="application-insights-log-based-metrics"></a>애플리케이션 인사이트 로그 기반 메트릭

Application Insights 로그 기반 메트릭을 사용하면 모니터링되는 앱의 상태를 분석하고 강력한 대시보드를 만들고 경고를 구성할 수 있습니다. 다음과 같은 두 가지 종류의 메트릭이 있습니다.

* 장면 뒤의 [로그 기반 메트릭은](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) 저장된 이벤트의 [Kusto 쿼리로](https://docs.microsoft.com/azure/kusto/query/) 변환됩니다.
* [표준 메트릭은](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) 미리 집계된 표준 표준 열로 저장됩니다.

*표준 메트릭은* 수집 중에 미리 집계되므로 쿼리 시 성능이 향상됩니다. 따라서 대시보드 및 실시간 경고를 위한 더 나은 선택이 될 수 있습니다. *로그 기반 메트릭에는* 더 많은 차원이 있어 데이터 분석 및 임시 진단을 위한 탁월한 옵션입니다. [네임스페이스 선택기를](metrics-getting-started.md#create-your-first-metric-chart) 사용하여 [메트릭 탐색기에서](metrics-getting-started.md)로그 기반 메트릭과 표준 메트릭 간에 전환합니다.

## <a name="interpret-and-use-queries-from-this-article"></a>이 문서의 쿼리 해석 및 사용

이 문서에서는 지원되는 집계 및 차원이 있는 메트릭을 나열합니다. 로그 기반 메트릭에 대한 세부 정보에는 기본 Kusto 쿼리 문이 포함됩니다. 편의를 위해 각 쿼리는 시간 세분성, 차트 유형 및 분할 차원에 대한 기본값을 사용하여 수정할 필요 없이 Log Analytics에서 쿼리를 사용하여 단순화합니다.

[메트릭 탐색기에서](metrics-getting-started.md)동일한 메트릭을 플로팅할 때 기본값이 없습니다 - 쿼리는 차트 설정에 따라 동적으로 조정됩니다.

- 선택한 **시간 범위는** 선택한 시간 범위에서만 이벤트를 선택하는 *타임스탬프 절이* 있는 추가 로 변환됩니다. 예를 들어, 최근 24시간 동안의 데이터를 보여주는 차트에는 *타임스탬프가 > 전(24시간)에 대한 |* 이 쿼리가 포함됩니다.

- 선택한 **시간 세분성은** 최종 요약에 넣어 *... bin(타임스탬프, [타임그레인]) 절로* 표시됩니다.

- 선택한 **모든 필터** 차원은 추가 *where* 절로 변환됩니다.

- 선택한 **분할 차트** 차원은 추가 요약 속성으로 변환됩니다. 예를 들어 차트를 *위치별로*분할하고 5분 시간 세분성을 사용하여 플롯하면 *요약* 절이 *요약됩니다. 빈 (타임 스탬프, 5m), 위치.*

> [!NOTE]
> Kusto 쿼리 언어를 사용하는 경우 수정하지 않고 Kusto 문을 복사하여 Log Analytics 쿼리 창에 붙여넣습니다. 기본 차트를 보려면 **실행을 클릭합니다.** 쿼리 언어의 구문을 이해하기 시작하면 약간 수정을 시작하고 변경의 영향을 확인할 수 있습니다. 사용자 고유의 데이터를 탐색하는 것은 [로그 분석](../../azure-monitor/log-query/get-started-portal.md) 및 [Azure 모니터의](../../azure-monitor/overview.md)모든 기능을 실현할 수 있는 좋은 방법입니다.

## <a name="availability-metrics"></a>가용성 메트릭

가용성 범주의 메트릭을 사용하면 전 세계 각지에서 관찰한 웹 응용 프로그램의 상태를 확인할 수 있습니다. 이 [범주의](../../azure-monitor/app/monitor-web-app-availability.md) 모든 메트릭사용을 시작하도록 가용성 테스트를 구성합니다.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>가용성(가용성결과/가용성비율)
*가용성* 메트릭에는 문제를 감지하지 못한 웹 테스트 실행비율이 표시됩니다. 가능한 가장 낮은 값은 0이며, 이는 모든 웹 테스트 실행이 실패했음을 나타냅니다. 값이 100이면 모든 웹 테스트 실행이 유효성 검사 기준을 통과했다는 의미입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|백분율|평균|실행 위치, 테스트 이름|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>가용성 테스트 기간(가용성결과/기간)

*가용성 테스트 기간* 메트릭은 웹 테스트를 실행하는 데 걸린 시간을 보여줍니다. [다단계 웹 테스트의](../../azure-monitor/app/availability-multistep.md)경우 메트릭은 모든 단계의 총 실행 시간을 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|밀리초|평균, 최소, 최대|실행 위치, 테스트 이름, 테스트 결과

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>가용성 테스트(가용성결과/수)

가용성 테스트 메트릭은 Azure Monitor에서 실행하는 웹 테스트 수를 *반영합니다.*

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|개수|개수|실행 위치, 테스트 이름, 테스트 결과|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>브라우저 메트릭

브라우저 메트릭은 실제 최종 사용자 브라우저에서 응용 프로그램 인사이트 자바 스크립트 SDK에 의해 수집됩니다. 웹 앱에 대한 사용자 경험에 대한 훌륭한 통찰력을 제공합니다. 브라우저 메트릭은 일반적으로 샘플링되지 않으므로 샘플링을 통해 왜곡될 수 있는 서버 측 메트릭에 비해 사용 수치의 정밀도가 더 높습니다.

> [!NOTE]
> 브라우저 메트릭을 수집하려면 응용 프로그램 [인사이트 JavaScript SDK를](../../azure-monitor/app/javascript.md)사용하여 응용 프로그램을 계측해야 합니다.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>브라우저 페이지 로드 시간(브라우저타이밍/총지속시간)

|측정 단위|지원되는 집계|사전 집계된 차원|
|---|---|---|
|밀리초|평균, 최소, 최대|None|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>클라이언트 처리 시간(브라우저타이밍/처리기간)

|측정 단위|지원되는 집계|사전 집계된 차원|
|---|---|---|
|밀리초|평균, 최소, 최대|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>페이지 로드 네트워크 연결 시간(브라우저타이밍/네트워크지속)

|측정 단위|지원되는 집계|사전 집계된 차원|
|---|---|---|
|밀리초|평균, 최소, 최대|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>응답 시간 수신(브라우저타이밍/수신기간)

|측정 단위|지원되는 집계|사전 집계된 차원|
|---|---|---|
|밀리초|평균, 최소, 최대|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>요청 시간 보내기(브라우저타이밍/송신지속시간)

|측정 단위|지원되는 집계|사전 집계된 차원|
|---|---|---|
|밀리초|평균, 최소, 최대|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>실패 메트릭

**실패의** 메트릭은 처리 요청, 종속성 호출 및 throw된 예외에 대한 문제를 표시합니다.

### <a name="browser-exceptions-exceptionsbrowser"></a>브라우저 예외(예외/브라우저)

이 메트릭은 브라우저에서 실행되는 응용 프로그램 코드에서 throw된 예외 수를 반영합니다. 응용 프로그램 인사이트 API ```trackException()``` 호출로 추적되는 예외만 메트릭에 포함됩니다.

|측정 단위|지원되는 집계|사전 집계된 차원|메모|
|---|---|---|---|
|개수|개수|None|로그 기반 버전은 **합계** 집계를 사용합니다.|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>종속성 호출 실패(종속성/실패)

실패한 종속성 호출 수입니다.

|측정 단위|지원되는 집계|사전 집계된 차원|메모|
|---|---|---|---|
|개수|개수|None|로그 기반 버전은 **합계** 집계를 사용합니다.|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>예외(예외/개수)

응용 프로그램 인사이트에 예외를 기록할 때마다 SDK의 [trackException() 메서드에](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) 대한 호출이 있습니다. 예외 메트릭에는 기록된 예외 수가 표시됩니다.

|측정 단위|지원되는 집계|사전 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스, 장치 유형|로그 기반 버전은 **합계** 집계를 사용합니다.|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>실패한 요청(요청/실패)

*실패한*것으로 표시된 추적된 서버 요청 수입니다. 기본적으로 응용 프로그램 인사이트 SDK는 HTTP 응답 코드 5xx 또는 4xx를 반환한 각 서버 요청을 실패한 요청으로 자동으로 표시합니다. 사용자 지정 [원격 분석 초기화에서](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)요청 원격 분석 항목의 *성공* 속성을 수정하여 이 논리를 사용자 지정할 수 있습니다.

|측정 단위|지원되는 집계|사전 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 인스턴스, 클라우드 역할 이름, 실제 또는 합성 트래픽, 요청 성능, 응답 코드|로그 기반 버전은 **합계** 집계를 사용합니다.|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>서버 예외(예외/서버)

이 메트릭은 서버 예외 수를 보여 주며

|측정 단위|지원되는 집계|사전 집계된 차원|메모|
|---|---|---|---|
|개수|개수|클라우드 역할 이름, 클라우드 역할 인스턴스|로그 기반 버전은 **합계** 집계를 사용합니다.|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>성능 카운터

성능 카운터 범주의 **메트릭을** 사용하여 응용 프로그램 인사이트 에서 [수집한 시스템 성능 카운터에](../../azure-monitor/app/performance-counters.md)액세스합니다.

### <a name="available-memory-performancecountersavailablememory"></a>사용 가능한 메모리(성능 카운터/사용 가능한 메모리)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>예외율(성능 카운터/예외속도)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 요청 실행 시간(성능 카운터/요청 실행 시간)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 요청 속도(성능 카운터/요청PerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>응용 프로그램 큐의 HTTP 요청(성능 카운터/요청InQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>프로세스 CPU(성능 카운터/프로세스CpuPercentage)

메트릭은 모니터링되는 앱을 호스팅하는 프로세스에서 소비되는 총 프로세서 용량의 양을 보여 줍니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최소, 최대|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>공정 IO 속도(성능 카운터/프로세스IOBytesPerSecond)

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|초당 바이트|평균, 최소, 최대|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>프로세스 프라이빗 바이트(성능 카운터/프로세스PrivateBy)

모니터링된 프로세스가 데이터에 할당된 공유되지 않는 메모리의 양입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|바이트|평균, 최소, 최대|클라우드 역할 인스턴스

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>프로세서 시간(성능 카운터/프로세서CpuPercent)

모니터링되는 서버 인스턴스에서 실행되는 *모든* 프로세스에 의한 CPU 소비량입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최소, 최대|클라우드 역할 인스턴스

>[!NOTE]
> Azure App 서비스에서 호스팅되는 응용 프로그램에는 프로세서 시간 메트릭을 사용할 수 없습니다. 프로세스 [CPU](#process-cpu-performancecountersprocesscpupercentage) 메트릭을 사용하여 앱 서비스에서 호스팅되는 웹 응용 프로그램의 CPU 사용률을 추적합니다.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>서버 메트릭

### <a name="dependency-calls-dependenciescount"></a>종속성 호출(종속성/개수)

이 메트릭은 종속성 호출 수와 관련이 있습니다.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>종속성 기간(종속성/기간)

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

### <a name="server-requests-requestscount"></a>서버 요청(요청/수)

이 메트릭은 웹 응용 프로그램에서 받은 들어오는 서버 요청 수를 반영합니다.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>서버 응답 시간(요청/기간)

이 메트릭은 서버가 들어오는 요청을 처리하는 데 걸린 시간을 반영합니다.

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

### <a name="page-view-load-time-pageviewsduration"></a>페이지 보기 로드 시간(페이지뷰/지속 시간)

이 측정항목은 PageView 이벤트를 로드하는 데 걸린 시간을 나타냅니다.

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

### <a name="page-views-pageviewscount"></a>페이지 조회수(페이지뷰/개수)

TrackPageView() 응용 프로그램 인사이트 API로 기록된 페이지뷰 이벤트 수입니다.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>세션(세션/수)

이 메트릭은 고유한 세션 아이디 수를 나타냅니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>추적(추적/개수)

TrackTrace() 응용 프로그램 인사이트 API 호출로 기록된 추적 문의 수입니다.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>사용자(사용자/개수)

응용 프로그램에 액세스한 고유 사용자 수입니다. 이 메트릭의 정확도는 원격 분석 샘플링 및 필터링을 사용하여 크게 영향을 받을 수 있습니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>사용자, 인증(사용자/인증)

응용 프로그램에 인증된 고유 사용자 수입니다.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
