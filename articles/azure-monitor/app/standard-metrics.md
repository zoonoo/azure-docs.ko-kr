---
title: Azure Application Insights 표준 메트릭 | Microsoft Docs
description: 이 문서에는 지원되는 집계 및 차원이 포함된 Azure Application Insights 메트릭이 나열됩니다.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.openlocfilehash: 494976bf09e5d1717bc60a1f12e039621061e6fa
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385465"
---
# <a name="application-insights-standard-metrics"></a>Azure Application Insights 표준 메트릭

표준 메트릭은 수집 중에 미리 집계되므로 쿼리 시 성능이 향상됩니다. 따라서 이는 대시보드 및 실시간 경고를 위한 가장 적합한 선택입니다.

## <a name="availability-metrics"></a>가용성 메트릭

가용성 범주의 메트릭을 사용하면 전 세계의 포인트에서 관찰된 웹 애플리케이션의 상태를 확인할 수 있습니다. 이 범주의 메트릭을 사용하려면 [가용성 테스트를 구성](../app/monitor-web-app-availability.md)합니다.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>가용성(availabilityResults/availabilityPercentage)
‘가용성 메트릭’은 문제를 찾지 못한 웹 테스트 실행의 비율을 보여 줍니다. 가능한 최솟값은 0이며, 이는 모든 웹 테스트 실행이 실패했음을 나타냅니다. 100 값은 모든 웹 테스트 실행이 유효성 검사 조건을 통과했음을 의미합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|백분율|평균|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>가용성 테스트 기간(availabilityResults/duration)

‘가용성 테스트 기간’ 메트릭은 웹 테스트를 실행하는 데 걸린 시간을 보여 줍니다. [다단계 웹 테스트](../app/availability-multistep.md)의 경우 메트릭은 모든 단계의 총 실행 시간을 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|밀리초|평균, 최솟값, 최댓값|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>가용성 테스트(availabilityResults/count)

‘가용성 테스트’ 메트릭은 Azure Monitor에 의한 웹 테스트 실행 수를 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|---|---|---|
|개수|개수|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>브라우저 메트릭

브라우저 메트릭은 실제 최종 사용자 브라우저에서 Application Insights JavaScript SDK에 의해 수집됩니다. 웹앱과 관련된 사용자의 경험에 대한 유용한 정보를 제공합니다. 브라우저 메트릭은 일반적으로 샘플링되지 않습니다. 즉, 샘플링으로 인해 왜곡될 수 있는 서버 측 메트릭과 비교할 때 사용량의 정확도가 높습니다.

> [!NOTE]
> 브라우저 메트릭을 수집하려면 [Application Insights JavaScript SDK](../app/javascript.md)로 애플리케이션을 계측해야 합니다.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>브라우저 페이지 로드 시간(browserTimings/totalDuration)

|측정 단위|지원되는 집계| 지원되는 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

### <a name="client-processing-time-browsertimingprocessingduration"></a>클라이언트 처리 시간(browserTiming/processingDuration)

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>페이지 로드 네트워크 연결 시간(browserTimings/networkDuration)

|측정 단위|지원되는 집계| 지원되는 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>수신 응답 시간(browserTimings/receiveDuration)

|측정 단위|지원되는 집계| 지원되는 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

### <a name="send-request-time-browsertimingssendduration"></a>요청 전송 시간(browserTimings/sendDuration)

|측정 단위|지원되는 집계| 지원되는 차원|
|---|---|---|
|밀리초|평균, 최솟값, 최댓값|없음|

## <a name="failure-metrics"></a>오류 메트릭

**오류** 의 메트릭은 요청 처리, 종속성 호출, throw된 예외에 대한 문제를 보여 줍니다.

### <a name="browser-exceptions-exceptionsbrowser"></a>브라우저 예외(exceptions/browser)

이 메트릭은 브라우저에서 실행 중인 애플리케이션 코드에서 throw된 예외의 수를 반영합니다. ```trackException()``` Application Insights API 호출로 추적되는 예외만이 메트릭에 포함됩니다.

|측정 단위|지원되는 집계 | 지원되는 차원|
|---|---|---|---|
| 개수 | 개수 | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>종속성 호출 실패(dependencies/failed)

실패한 종속성 호출 수입니다.

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
|개수|개수| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>예외(exceptions/count)

Application Insights에 대한 예외를 기록할 때마다 SDK의 [trackException() method](../app/api-custom-events-metrics.md#trackexception)에 대한 호출이 발생합니다. 예외 메트릭에는 로깅된 예외의 수가 표시됩니다.

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>실패한 요청(requests/failed)

추적된 서버 요청 중 ‘실패’로 표시된 요청의 수입니다. 기본적으로 Application Insights SDK는 HTTP 응답 코드 5xx 또는 4xx를 반환한 각 서버 요청을 실패한 요청으로 자동으로 표시합니다. [사용자 지정 원격 분석 이니셜라이저](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)에서 요청 원격 분석 항목의 ‘성공’ 속성을 수정하여 이 논리를 사용자 지정할 수 있습니다.


|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>서버 예외(exceptions/server)

이 메트릭은 서버 예외의 수를 표시합니다.

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>성능 카운터

**성능 카운터** 범주의 메트릭을 사용하여 [Application Insights에 의해 수집된 시스템 성능 카운터](../app/performance-counters.md)에 액세스합니다.

### <a name="available-memory-performancecountersavailablememory"></a>사용 가능한 메모리(performanceCounters/availableMemory)

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
|데이터 종속: 메가바이트, 기가바이트|평균, 최대, 최소|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>예외율(performanceCounters/exceptionRate)

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
| 개수 | 평균, 최대, 최소 | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 요청 실행 시간(performanceCounters/requestExecutionTime)

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
| 밀리초 | 평균, 최대, 최소 | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 요청 속도(performanceCounters/requestsPerSecond)

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
| 초당 요청 | 평균, 최대, 최소 | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>애플리케이션 큐의 HTTP 요청(performanceCounters/requestsInQueue)

|측정 단위|지원되는 집계 | 지원되는 차원 |
|---|---|---|---|
| 개수 | 평균, 최대, 최소 | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU 프로세스(performanceCounters/processCpuPercentage)

메트릭은 모니터링되는 앱을 호스트하는 프로세스에 사용되는 총 프로세서 용량을 보여 줍니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최대, 최소| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>프로세스 IO 속도(performanceCounters/processIOBytesPerSecond)

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|초당 바이트|평균, 최솟값, 최댓값|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>프로세스 프라이빗 바이트(performanceCounters/processPrivateBytes)

공유되지 않는 메모리 중 모니터링되는 프로세스가 해당 데이터에 할당한 메모리의 양입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|바이트|평균, 최솟값, 최댓값|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>프로세서 시간(performanceCounters/processorCpuPercentage)

모니터링되는 서버 인스턴스에서 실행 중인 ‘모든’ 프로세스의 CPU 소비량입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
|백분율|평균, 최솟값, 최댓값|`Cloud role instance` |

>[!NOTE]
> Azure App Services에서 호스트되는 애플리케이션에는 프로세서 시간 메트릭을 사용할 수 없습니다. App Services에서 호스트되는 웹 애플리케이션의 CPU 사용률을 추적하려면 [CPU 프로세스](#process-cpu-performancecountersprocesscpupercentage) 메트릭을 사용하세요.

## <a name="server-metrics"></a>서버 메트릭

### <a name="dependency-calls-dependenciescount"></a>종속성 호출(dependencies/count)

이 메트릭은 종속성 호출 수와 관련이 있습니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>종속성 기간(dependencies/duration)

이 메트릭은 종속성 호출의 기간을 나타냅니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 시간 | 평균, 최솟값, 최댓값 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>서버 요청 빈도(요청/개수)

이 메트릭은 웹 애플리케이션에서 받은 수신 서버 요청 수를 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 개수 | 평균 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>서버 요청(requests/count)

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>서버 응답 시간(requests/duration)

이 메트릭은 서버가 수신 요청을 처리하는 데 걸린 시간을 반영합니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 시간 | 평균, 최솟값, 최댓값 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>사용량 메트릭

### <a name="page-view-load-time-pageviewsduration"></a>페이지 보기 로드 시간(pageViews/duration)

이 메트릭은 페이지 보기 이벤트를 로드하는 데 걸린 시간을 나타냅니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 시간 | 평균, 최솟값, 최댓값 | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>페이지 보기(pageViews/count)

TrackPageView() Application Insights API로 기록된 페이지 보기 이벤트의 수입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>추적(traces/count)

TrackTrace() Application Insights API 호출로 기록된 추적 문의 수입니다.

|측정 단위|지원되는 집계|지원되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>다음 단계

* [로그 기반 및 미리 집계된 메트릭](./pre-aggregated-metrics-log-metrics.md)에 관해 알아봅니다.
* [로그 기반 메트릭 쿼리 및 정의](../essentials/app-insights-metrics.md).