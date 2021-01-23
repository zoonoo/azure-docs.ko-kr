---
title: Azure 애플리케이션 Insights 표준 메트릭 | Microsoft Docs
description: 이 문서에는 지원 되는 집계 및 차원이 포함 된 Azure 애플리케이션 Insights 메트릭이 나열 됩니다.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 8da719f399c0c49efd478f05a0114d85233c880f
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732631"
---
# <a name="application-insights-standard-metrics"></a>Application Insights 표준 메트릭

표준 메트릭은 수집 중에 미리 집계 되며, 쿼리 시 성능이 향상 됩니다. 이렇게 하면 대시보드 및 실시간 경고에 대해 가장 적합 한 선택이 됩니다.

## <a name="availability-metrics"></a>가용성 메트릭

가용성 범주의 메트릭을 사용 하면 전 세계에서 관찰 된 웹 응용 프로그램의 상태를 확인할 수 있습니다. 이 범주의 메트릭을 사용 하 여 시작 하도록 [가용성 테스트를 구성](../app/monitor-web-app-availability.md) 합니다.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>가용성 (availabilityResults/availabilityPercentage)
*가용성* 메트릭은 문제를 검색 하지 못한 웹 테스트 실행의 비율을 보여 줍니다. 가능한 최소값은 0 이며,이는 모든 웹 테스트 실행이 실패 했음을 나타냅니다. 100 값은 모든 웹 테스트 실행에서 유효성 검사 조건을 통과 했음을 의미 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|백분율|평균|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>가용성 테스트 기간 (availabilityResults/duration)

*가용성 테스트 기간* 메트릭은 웹 테스트를 실행 하는 데 걸린 시간을 보여 줍니다. [다단계 웹 테스트](../app/availability-multistep.md)의 경우 메트릭은 모든 단계의 총 실행 시간을 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|밀리초|평균, 최소값, 최대값|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>가용성 테스트 (availabilityResults/count)

*가용성 테스트* 메트릭은 Azure Monitor에 의해 실행 되는 웹 테스트 수를 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|---|---|---|
|개수|개수|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>브라우저 메트릭

브라우저 메트릭은 실제 최종 사용자 브라우저에서 Application Insights JavaScript SDK에 의해 수집 됩니다. 웹 앱을 사용 하 여 사용자의 경험에 대 한 유용한 정보를 제공 합니다. 브라우저 메트릭은 일반적으로 샘플링 되지 않습니다. 즉, 샘플링으로 기울일 수 있는 서버 쪽 메트릭과 비교 하 여 사용 숫자의 전체 자릿수를 제공 합니다.

> [!NOTE]
> 브라우저 메트릭을 수집 하려면 [Application Insights JAVASCRIPT SDK](../app/javascript.md)를 사용 하 여 응용 프로그램을 계측 해야 합니다.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>브라우저 페이지 로드 시간 (browserTimings/totalDuration)

|측정 단위|지원 되는 집계| 지원 되는 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

### <a name="client-processing-time-browsertimingprocessingduration"></a>클라이언트 처리 시간 (browserTiming/processingDuration)

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>페이지 로드 네트워크 연결 시간 (browserTimings/networkDuration)

|측정 단위|지원 되는 집계| 지원 되는 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>수신 응답 시간 (browserTimings/receiveDuration)

|측정 단위|지원 되는 집계| 지원 되는 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

### <a name="send-request-time-browsertimingssendduration"></a>전송 요청 시간 (browserTimings/sendDuration)

|측정 단위|지원 되는 집계| 지원 되는 차원|
|---|---|---|
|밀리초|평균, 최소값, 최대값|없음|

## <a name="failure-metrics"></a>오류 메트릭

**오류의** 메트릭은 처리 요청, 종속성 호출 및 throw 된 예외에 대 한 문제를 보여 줍니다.

### <a name="browser-exceptions-exceptionsbrowser"></a>브라우저 예외 (예외/브라우저)

이 메트릭은 브라우저에서 실행 중인 응용 프로그램 코드에서 throw 된 예외 수를 반영 합니다. Application Insights API 호출로 추적 되는 예외만 ```trackException()``` 메트릭에 포함 됩니다.

|측정 단위|지원 되는 집계 | 지원 되는 차원|
|---|---|---|---|
| 개수 | 개수 | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>종속성 호출 오류 (종속성/실패)

실패 한 종속성 호출 수입니다.

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
|개수|개수| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>예외 (예외/개수)

Application Insights에 대 한 예외를 기록할 때마다 SDK의 지 수 [예외 () 메서드가](../app/api-custom-events-metrics.md#trackexception) 호출 됩니다. 예외 메트릭에는 로깅된 예외의 수가 표시 됩니다.

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>실패 한 요청 (요청/실패)

*실패로* 표시 된 추적 된 서버 요청 수입니다. 기본적으로 Application Insights SDK는 HTTP 응답 코드 5xx 또는 4xx를 반환한 각 서버 요청을 실패 한 요청으로 자동으로 표시 합니다. [사용자 지정 원격 분석 이니셜라이저에서](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)요청 원격 분석 항목의 *success* 속성을 수정 하 여이 논리를 사용자 지정할 수 있습니다.


|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>서버 예외 (예외/서버)

이 메트릭은 서버 예외의 수를 표시 합니다.

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
|개수|개수|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>성능 카운터

**성능 카운터** 범주의 메트릭을 사용 하 여 Application Insights에 [의해 수집 된 시스템 성능 카운터](../app/performance-counters.md)에 액세스할 수 있습니다.

### <a name="available-memory-performancecountersavailablememory"></a>사용 가능한 메모리 (performanceCounters/availableMemory)

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
|데이터 종속: 메가바이트, Gb|평균, 최대값, 최소값|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>예외 율 (performanceCounters/exceptionRate)

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
| 개수 | 평균, 최대값, 최소값 | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 요청 실행 시간 (performanceCounters/requestExecutionTime)

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
| 밀리초 | 평균, 최대값, 최소값 | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 요청 빈도 (performanceCounters/requestsPerSecond)

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
| 초당 요청 | 평균, 최대값, 최소값 | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>응용 프로그램 큐의 HTTP 요청 (performanceCounters/requestsInQueue)

|측정 단위|지원 되는 집계 | 지원 되는 차원 |
|---|---|---|---|
| 개수 | 평균, 최대값, 최소값 | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>프로세스 CPU (performanceCounters/processCpuPercentage)

메트릭은 모니터링 되는 앱을 호스트 하는 프로세스에서 사용 되는 총 프로세서 용량의 크기를 보여 줍니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|백분율|평균, 최대값, 최소값| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>프로세스 IO 요금 (performanceCounters/processIOBytesPerSecond)

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|초당 바이트 수|평균, 최소값, 최대값|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>프로세스 전용 바이트 (performanceCounters/processPrivateBytes)

모니터링 되는 프로세스가 해당 데이터에 할당 한 공유 되지 않는 메모리의 양입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|바이트|평균, 최소값, 최대값|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>프로세서 시간 (performanceCounters/processorCpuPercentage)

모니터링 되는 서버 인스턴스에서 실행 중인 *모든* 프로세스의 CPU 사용량입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
|백분율|평균, 최소값, 최대값|`Cloud role instance` |

>[!NOTE]
> Azure 앱 Services에서 호스트 되는 응용 프로그램에는 프로세서 시간 메트릭을 사용할 수 없습니다. [프로세스 cpu](#process-cpu-performancecountersprocesscpupercentage) 메트릭을 사용 하 여 App Services에서 호스트 되는 웹 응용 프로그램의 cpu 사용률을 추적 합니다.

## <a name="server-metrics"></a>서버 메트릭

### <a name="dependency-calls-dependenciescount"></a>종속성 호출 (종속성/개수)

이 메트릭은 종속성 호출 수를 기준으로 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>종속성 기간 (종속성/기간)

이 메트릭은 종속성 호출의 기간을 나타냅니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 시간 | 평균, 최소값, 최대값 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>서버 요청 빈도 (요청/수)

이 메트릭은 웹 응용 프로그램에서 받은 들어오는 서버 요청 수를 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 개수 | 평균 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>서버 요청 (요청/수)

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>서버 응답 시간 (요청/기간)

이 메트릭은 서버가 들어오는 요청을 처리 하는 데 걸린 시간을 반영 합니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 시간 | 평균, 최소값, 최대값 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>사용량 메트릭

### <a name="page-view-load-time-pageviewsduration"></a>페이지 보기 로드 시간 (pageViews/duration)

이 메트릭은 페이지 보기 이벤트를 로드 하는 데 걸린 시간을 나타냅니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 시간 | 평균, 최소값, 최대값 | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>페이지 보기 (pageViews/count)

TrackPageView () Application Insights API로 기록 된 페이지 보기 이벤트의 수입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>추적 (추적/개수)

충돌 추적 () Application Insights API 호출로 기록 된 추적 문의 수입니다.

|측정 단위|지원 되는 집계|지원 되는 차원|
|---|---|---|
| 개수 | 개수 | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>다음 단계

* [로그 기반 및 미리 집계 된 메트릭에](./pre-aggregated-metrics-log-metrics.md)대해 알아봅니다.
* [로그 기반 메트릭 쿼리 및 정의](../platform/app-insights-metrics.md).