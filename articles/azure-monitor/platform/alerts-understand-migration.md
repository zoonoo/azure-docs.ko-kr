---
title: Azure Monitor 경고에 대해 자발적 마이그레이션 도구가 작동하는 방식 이해
description: 경고 마이그레이션 도구의 작동 방식을 이해하고 문제를 해결합니다.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274815"
---
# <a name="understand-how-the-migration-tool-works"></a>마이그레이션 도구의 작동 방식 이해

[이전에 발표된](monitoring-classic-retirement.md)대로 Azure Monitor의 클래식 경고는 2019년 8월 31일(원래 2019년 6월 30일)까지 사용 중지됩니다. Azure 포털에서 클래식 경고 규칙을 사용하고 마이그레이션을 트리거하려는 고객에게 마이그레이션 도구를 사용할 수 있습니다.

이 문서에서는 자발적인 마이그레이션 도구의 작동 방식을 설명합니다. 또한 몇 가지 일반적인 문제에 대 한 구제를 설명 합니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 이 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표된 [날짜인 2019년 6월 30일부터 2019년 8월 31일로 연장되었습니다.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>마이그레이션되지 않는 클래식 경고 규칙

> [!IMPORTANT]
> 활동 로그 경고(서비스 상태 경고 포함) 및 로그 경고는 마이그레이션의 영향을 받지 않습니다. 마이그레이션은 [여기에](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)설명된 클래식 경고 규칙에만 적용됩니다.

이 도구는 거의 모든 [클래식 경고 규칙을](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)마이그레이션할 수 있지만 몇 가지 예외가 있습니다. 다음 경고 규칙은 도구를 사용하거나(또는 2019년 9월부터 자동 마이그레이션하는 동안) 마이그레이션되지 않습니다.

- 가상 컴퓨터 게스트 메트릭(Windows 및 Linux 모두)에 대한 클래식 경고 규칙입니다. 이 문서의 [후반부에서 새 메트릭 경고에서 이러한 경고 규칙을 다시 만드는 방법에 대한 지침을 참조하세요.](#guest-metrics-on-virtual-machines)
- 클래식 저장소 메트릭에 대한 클래식 경고 규칙입니다. 클래식 [저장소 계정을 모니터링하기 위한 지침을](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)참조하십시오.
- 일부 저장소 계정 메트릭에 대한 클래식 경고 규칙입니다. 이 문서의 [후반부에서 자세히 알아보십시오.](#storage-account-metrics)
- 일부 코스모스 DB 메트릭에 대한 클래식 경고 규칙입니다. 이 문서의 [후반부에서 자세히 알아보십시오.](#cosmos-db-metrics)
- 모든 클래식 가상 컴퓨터 및 클라우드 서비스 메트릭(Microsoft.ClassicCompute/가상 머신 및 Microsoft.ClassicCompute/도메인 이름/슬롯/역할)에 대한 클래식 경고 규칙입니다. 이 문서의 [후반부에서 자세히 알아보십시오.](#classic-compute-metrics)

구독에 이러한 기본 규칙이 있는 경우 수동으로 마이그레이션해야 합니다. 자동 마이그레이션을 제공할 수 없으므로 이러한 유형의 기존 클래식 메트릭 경고는 2020년 6월까지 계속 작동합니다. 이 확장을 사용하면 새 경고로 이동할 수 있는 시간이 주어드립니다. 또한 2020년 6월까지 위에 나열된 예외에 대해 새 클래식 경고를 계속 만들 수 있습니다. 그러나 다른 모든 경우 2019년 8월 이후에는 새로운 클래식 경고를 만들 수 없습니다.

> [!NOTE]
> 위에 나열된 예외 외에도 클래식 경고 규칙이 유효하지 않은 경우(예: [삭제된 더 이상 사용되지 않는 메트릭](#classic-alert-rules-on-deprecated-metrics) 또는 리소스에 있는 경우) 마이그레이션되지 않으며 서비스가 폐기된 후에는 사용할 수 없습니다.

### <a name="guest-metrics-on-virtual-machines"></a>가상 머신의 게스트 메트릭

게스트 메트릭에서 새 메트릭 경고를 만들려면 게스트 메트릭을 Azure Monitor 사용자 지정 메트릭 저장소로 보내야 합니다. 진단 설정에서 Azure 모니터 싱크를 사용 하려면 다음 지침을 따르십시오.

- [Windows VM용 게스트 메트릭 사용](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM용 게스트 메트릭 사용](collect-custom-metrics-linux-telegraf.md)

이러한 단계가 완료되면 게스트 측정항목에 새 측정항목 경고를 만들 수 있습니다. 새 메트릭 경고를 만든 후 클래식 경고를 삭제할 수 있습니다.

### <a name="storage-account-metrics"></a>스토리지 계정 메트릭

저장소 계정에 대한 모든 클래식 경고는 다음 메트릭에 대한 경고를 제외하고 마이그레이션할 수 있습니다.

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Percent 메트릭에 대한 클래식 경고 규칙은 [이전 및 새 저장소 메트릭 간의 매핑을](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)기반으로 마이그레이션되어야 합니다. 사용 가능한 새 메트릭이 절대 메트릭이기 때문에 임계값을 적절하게 수정해야 합니다.

동일한 기능을 제공하는 결합된 메트릭이 없기 때문에 익명ThrottlingError, SASThrottlingError 및 제한오류에 대한 클래식 경고 규칙은 두 개의 새 경고로 분할되어야 합니다. 임계값을 적절하게 조정해야 합니다.

### <a name="cosmos-db-metrics"></a>Cosmos DB 메트릭

Cosmos DB 메트릭의 모든 클래식 경고는 다음 메트릭에 대한 경고를 제외하고 마이그레이션할 수 있습니다.

- 초당 평균 요청
- 일관성 수준
- Http 2xx
- Http 3xx
- HTTP 400
- Http 401
- 내부 서버 오류
- 분당 최대 RUPM 소비량
- 초당 최대 루스
- 몽고 카운트 실패 요청
- 몽고 삭제 실패 요청
- 몽고 삽입 실패 요청
- Mongo 기타 실패한 요청
- 몽고 기타 요청 요금
- 몽고 기타 요청 요금
- Mongo 쿼리 실패 한 요청
- 몽고 업데이트 실패한 요청
- 관찰된 읽기 대기 시간
- 관찰된 쓰기 대기 시간
- 서비스 가용성
- 스토리지 용량
- 제한된 요청
- 총 요청 수

초당 평균 요청, 일관성 수준, 분당 최대 RUPM 소비량, 초당 최대 RUS, 관찰된 읽기 대기 시간, 관찰된 쓰기 대기 시간, 저장소 용량은 현재 [새 시스템에서](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)사용할 수 없습니다.

Http 2xx, Http 3xx, Http 400, Http 401, 내부 서버 오류, 서비스 가용성, 제한 요청 및 총 요청과 같은 요청 메트릭에 대한 경고는 기존 메트릭과 새 메트릭 간에 요청 계산 방식이 다르기 때문에 마이그레이션되지 않습니다. 이러한 경고는 임계값을 조정하여 수동으로 다시 만들어야 합니다.

동일한 기능을 제공하는 결합된 메트릭이 없기 때문에 Mongo 실패 요청 메트릭에 대한 경고는 여러 경고로 분할되어야 합니다. 임계값을 적절하게 조정해야 합니다.

### <a name="classic-compute-metrics"></a>클래식 계산 메트릭

클래식 계산 리소스가 새 경고로 아직 지원되지 않기 때문에 클래식 계산 메트릭에 대한 경고는 마이그레이션 도구를 사용하여 마이그레이션되지 않습니다. 이러한 리소스 유형에 대한 새 경고에 대한 지원은 나중에 추가될 예정입니다. 이를 사용할 수 있게 되면 고객은 2020년 6월 이전에 클래식 경고 규칙에 따라 새 등가 경고 규칙을 다시 만들어야 합니다.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>더 이상 사용되지 이상 측정된 메트릭에 대한 클래식 경고 규칙

이러한 규칙은 이전에 지원되었지만 결국 더 이상 사용되지 않았던 메트릭에 대한 고전적인 경고 규칙입니다. 소수의 고객이 이러한 측정항목에 대해 잘못된 클래식 경고 규칙을 가질 수 있습니다. 이러한 경고 규칙은 유효하지 않으므로 마이그레이션되지 않습니다.

| 리소스 유형| 더 이상 사용되지 않습니다. |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | 디도스트리거레이트 |
| Microsoft.SQL/서버/데이터베이스 | service_level_objective, storage_limit, storage_used, 조절, dtu_consumption_percent, storage_used |
| Microsoft.Web/호스팅환경/다기능 풀 | 평균 메모리 작업 집합 |
| 마이크로소프트.웹/호스팅환경/작업자 풀 | 바이트 수신, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>동등한 새 경고 규칙 및 작업 그룹을 생성하는 방법

마이그레이션 도구는 클래식 경고 규칙을 동등한 새 경고 규칙 및 작업 그룹으로 변환합니다. 대부분의 클래식 경고 규칙의 경우 동일한 새 경고 규칙은 `windowSize` 와 같은 `aggregationType`동일한 속성을 가진 동일한 메트릭에 있습니다. 그러나 새 시스템에서 동일한 메트릭이 다른 몇 가지 기본 경고 규칙이 있습니다. 다음 원칙은 아래 섹션에 명시되지 않는 한 클래식 경고 마이그레이션에 적용됩니다.

- **빈도**: 클래식 또는 새 경고 규칙이 조건을 확인하는 빈도를 정의합니다. `frequency` 클래식 경고 규칙은 사용자가 구성할 수 없으며 1분이었던 Application Insights 구성 요소를 제외한 모든 리소스 유형에 대해 항상 5분이었습니다. 따라서 동등한 규칙의 빈도도 각각 5 분과 1 분으로 설정됩니다.
- **집계 유형**: 관심 창에서 메트릭이 집계되는 방법을 정의합니다. `aggregationType` 또한 대부분의 메트릭에 대한 클래식 경고와 새 경고 간에도 동일합니다. 경우에 따라 측정항목이 클래식 경고와 새 경고 간에 `aggregationType` 다르기 `primary Aggregation Type` 때문에 메트릭에 대해 동일한 항목 또는 정의된 항목이 사용됩니다.
- **단위**: 경고가 생성되는 메트릭의 속성입니다. 일부 등가 지표는 서로 다른 단위를 가수있습니다. 임계값은 필요에 따라 적절하게 조정됩니다. 예를 들어 원래 메트릭에 초가 단위로 있지만 이에 상응하는 새 메트릭에 밀리초가 단위로 있는 경우 원래 임계값에 동일한 동작을 보장하기 위해 원래 임계값에 1000을 곱합니다.
- **창 크기**: 임계값과 비교하기 위해 집계되는 메트릭 데이터의 창을 정의합니다. 5분, 15분, 30분, 1시간, 3시간, 6시간, 12시간, 1일 과 같은 표준 `windowSize` 값의 경우 동등한 새 경고 규칙에 대해 변경이 없습니다. 다른 값의 경우 `windowSize` 가장 가까운 값이 선택됩니다. 대부분의 고객은 이러한 변경에 영향을 미치지 않습니다. 소수의 고객의 경우 동일한 동작을 얻기 위해 임계값을 조정해야 할 수 있습니다.

다음 섹션에서는 새 시스템에서 다른 등가 메트릭이 있는 메트릭을 자세히 설명합니다. 클래식 및 새 경고 규칙에 대해 동일하게 유지되는 모든 메트릭은 나열되지 않습니다. 새 시스템에서 지원되는 메트릭 목록은 여기에서 확인할 수 [있습니다.](metrics-supported.md)

### <a name="microsoftstorageaccountsservices"></a>마이크로소프트.스토리지계정/서비스

Blob, 테이블, 파일 및 큐와 같은 저장소 계정 서비스의 경우 아래와 같이 다음 메트릭이 동등한 메트릭에 매핑됩니다.

| 클래식 경고의 메트릭 | 새 경고의 등가 지표 | 주석|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 차원 "ResponseType"="AuthorizationError" 및 "인증" = "익명"을 가진 트랜잭션 메트릭| |
| AnonymousClientOtherError | 차원 "ResponseType"="ClientOtherError" 및 "인증" = "익명" 트랜잭션 메트릭 | |
| 익명 클라이언트 타임아웃 오류| 차원 "ResponseType"="ClientTimeOutError" 및 "인증" = "익명" 트랜잭션 메트릭 | |
| AnonymousNetworkError | 차원 "ResponseType"="NetworkError" 및 "인증" = "익명"이 있는 트랜잭션 메트릭 | |
| AnonymousServerOtherError | 차원 "ResponseType"="ServerOtherError" 및 "인증" = "익명" 트랜잭션 메트릭 | |
| 익명 서버 시간 아웃 오류 | 차원 "ResponseType"="ServerTimeOutError" 및 "인증" = "익명"이 있는 트랜잭션 메트릭 | |
| AnonymousSuccess | 차원 "ResponseType"="성공" 및 "인증" = "익명" 트랜잭션 메트릭 | |
| AuthorizationError | 차원 "응답 유형"="권한 오류"가 있는 트랜잭션 메트릭 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 용량 | BlobCapacity | '마지막' 대신 '평균'을 사용합니다. `aggregationType` 메트릭은 Blob 서비스에만 적용됩니다. |
| ClientOtherError | 차원 "응답 유형"="ClientOtherError"가 있는 트랜잭션 메트릭  | |
| ClientTimeoutError | 차원 "응답 유형"="ClientTimeOutError"가 있는 트랜잭션 메트릭 | |
| ContainerCount | ContainerCount | '마지막' 대신 '평균'을 사용합니다. `aggregationType` 메트릭은 Blob 서비스에만 적용됩니다. |
| NetworkError | 차원 "ResponseType"="NetworkError"가 있는 트랜잭션 메트릭 | |
| ObjectCount | BlobCount| '마지막' 대신 '평균'을 사용합니다. `aggregationType` 메트릭은 Blob 서비스에만 적용됩니다. |
| SASAuthorizationError | 차원 "ResponseType"="AuthorizationError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SASClientOtherError | 차원 "ResponseType"="ClientOtherError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SAS클라이언트타임아웃 오류 | 차원 "ResponseType"="ClientTimeOutError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SASNetworkError | 차원 "ResponseType"="NetworkError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SASServerOtherError | 차원 "ResponseType"="ServerOtherError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SASServer시간 아웃 오류 | 차원 "ResponseType"="ServerTimeOutError" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| SASSuccess | 차원 "ResponseType"="성공" 및 "인증" = "SAS"가 있는 트랜잭션 메트릭 | |
| ServerOtherError | 차원 "응답 유형"="ServerOtherError"가 있는 트랜잭션 메트릭 | |
| 서버 타임아웃 오류 | 차원 "응답 유형"="서버시간 오류"가 있는 트랜잭션 메트릭  | |
| Success | 차원 "응답 유형"="성공" 트랜잭션 메트릭 | |
| TotalBillableRequests| 의 | |
| TotalEgress | 송신 | |
| TotalIngress | 수신 | |
| TotalRequests | 의 | |

### <a name="microsoftinsightscomponents"></a>마이크로소프트.인사이트/구성 요소

애플리케이션 인사이트(Application Insights)의 경우 다음과 같은 이와 동등한 지표가 나와 있습니다.

| 클래식 경고의 메트릭 | 새 경고의 등가 지표 | 주석|
|--------------------------|---------------------------------|---------|
| 가용성.가용성메트릭.값 | 가용성결과/가용성백분율|   |
| 가용성.기간메트릭.값 | availabilityResults/duration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 기본예외브라우저.카운트 | exceptions/browser|  '합계' 대신 '카운트'를 사용합니다. `aggregationType` |
| 기본예외 서버.카운트 | exceptions/server| '합계' 대신 '카운트'를 사용합니다. `aggregationType`  |
| 클라이언트성능.클라이언트프로세스.값 | browserTimings/processingDuration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 클라이언트성능.네트워크연결.값 | browserTimings/networkDuration|  클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다. |
| 클라이언트성능.수신요청.값 | browserTimings/receiveDuration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 클라이언트성능.sendRequest.value | browserTimings/sendDuration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 클라이언트성능.총가치 | browserTimings/totalDuration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 성능카운터.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| 성능카운터.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| 성능카운터.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| 성능카운터.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| 성능카운터.percentage_processor_time.value | performanceCounters/processCpuPercentage| 원래 메트릭이 모든 코어에 걸쳐 있고 새 메트릭이 하나의 코어로 정규화됨에 따라 임계값을 적절하게 수정해야 합니다. 마이그레이션 도구는 임계값을 변경하지 않습니다.  |
| 성능카운터.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| 성능카운터.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| 성능카운터.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| 성능카운터.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| 성능카운터.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| 요청.기간 | requests/duration| 클래식 메트릭의 단위는 초단위로 곱하고 새 임계값의 경우 밀리초단위로 곱합니다.  |
| 요청.속도 | 요청/요금|   |
| 요청실패.카운트 | requests/failed| '합계' 대신 '카운트'를 사용합니다. `aggregationType`   |
| 뷰.카운트 | pageViews/count| '합계' 대신 '카운트'를 사용합니다. `aggregationType`   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

코스모스 DB의 경우 이에 상응하는 메트릭은 다음과 같습니다.

| 클래식 경고의 메트릭 | 새 경고의 등가 지표 | 주석|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 데이터 크기 | DataUsage| |
| 문서 수 | DocumentCount||
| 인덱스 크기 | IndexUsage||
| 몽고 카운트 요청 요금| 차원 "명령 이름" = "개수"가 있는 MongoRequestCharge||
| 몽고 카운트 요청 속도 | MongoRequests 차원 "명령 이름" = "개수"로 계산||
| 몽고 삭제 요청 요금 | 차원 "명령 이름" = "삭제" 밍고요청요금||
| 몽고 삭제 요청 속도 | MongoRequests차원 "명령 이름" = "삭제"로 계산||
| 몽고 삽입 요청 요금 | 차원 "명령 이름" = "삽입" 밍고 요청 요금||
| 몽고 삽입 요청 속도 | MongoRequests차원 "명령 이름" = "삽입"으로 계산||
| 몽고 쿼리 요청 요금 | 차원 "명령 이름" = "찾기"를 가진 MongoRequestCharge||
| 몽고 쿼리 요청 속도 | MongoRequests차원 "명령 이름" = "찾기"로 계산||
| 몽고 업데이트 요청 요금 | 차원 "명령 이름" = "업데이트"를 가진 MongoRequestCharge||
| 서비스를 사용할 수 없음| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>동등한 작업 그룹을 생성하는 방법

클래식 경고 규칙에는 전자 메일, 웹후크, 논리 앱 및 Runbook 작업이 경고 규칙 자체에 연결되어 있었습니다. 새 경고 규칙은 여러 경고 규칙에서 다시 사용할 수 있는 작업 그룹을 사용합니다. 마이그레이션 도구는 작업을 사용하는 경고 규칙 수에 관계없이 동일한 작업에 대해 단일 작업 그룹을 만듭니다. 마이그레이션 도구에서 만든 작업 그룹은 명명 형식 'Migrated_AG*'를 사용합니다.

> [!NOTE]
> 클래식 경고는 클래식 관리자 역할을 알리는 데 사용할 때 클래식 관리자의 로캘을 기반으로 지역화된 전자 메일을 보냈습니다. 새 경고 이메일은 작업 그룹을 통해 전송되며 영어로만 전송됩니다.

## <a name="rollout-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용하는 고객에게 단계적으로 롤아웃됩니다. 구독 소유자는 도구를 사용하여 구독을 마이그레이션할 준비가 되면 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 도구는 단계적으로 롤아웃되고 있으므로 일부 구독이 아직 초기 단계에서 마이그레이션할 준비가 되지 않은 것을 알 수 있습니다.

대부분의 구독은 현재 마이그레이션 준비가 된 것으로 표시됩니다. 다음 리소스 유형에 대한 클래식 경고가 있는 구독만 마이그레이션할 준비가 되지 않았습니다.

- 마이크로소프트.클래식컴퓨팅/도메인 이름/슬롯/역할
- 마이크로소프트.인사이트/구성 요소

## <a name="who-can-trigger-the-migration"></a>마이그레이션을 트리거할 수 있는 사람은 누구입니까?

구독 수준에서 기여자 모니터링의 기본 제공 역할이 있는 모든 사용자는 마이그레이션을 트리거할 수 있습니다. 다음 사용 권한이 있는 사용자 지정 역할이 있는 사용자는 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- 마이크로소프트.인사이트/메트릭경고/*
- 마이크로소프트.경고 관리/스마트 디텍터 경고 규칙/*

> [!NOTE]
> 위의 사용 권한이 있는 것 외에도 구독은 Microsoft.AlertsManagement 리소스 공급자에 추가로 등록해야 합니다. 이는 응용 프로그램 인사이트에서 오류 변칙 경고를 성공적으로 마이그레이션하는 데 필요합니다. 

## <a name="common-problems-and-remedies"></a>일반적인 문제 및 해결 방법

[마이그레이션을 트리거하면](alerts-using-migration-tool.md)사용자가 제공한 주소로 마이그레이션이 완료되었음을 알리거나 사용자에게 필요한 작업이 필요한 경우 전자 메일을 받게 됩니다. 이 섹션에서는 몇 가지 일반적인 문제와 이를 처리하는 방법에 대해 설명합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독의 클래식 경고 규칙이 최근에 변경되어 구독을 마이그레이션할 수 없습니다. 이 문제는 일시적입니다. 마이그레이션 상태가 며칠 후에 마이그레이션 준비 상태로 다시 이동한 후 **마이그레이션을** 다시 시작할 수 있습니다.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>범위 잠금으로 인해 규칙 마이그레이션이 방지됩니다.

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 만들어지고 클래식 경고 규칙이 삭제됩니다. 그러나 범위 잠금을 사용하면 리소스를 만들거나 삭제할 수 없습니다. 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. [마이그레이션 도구에](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)나열된 구독, 리소스 그룹 또는 리소스에 대한 범위 잠금을 제거하고 마이그레이션을 다시 트리거하여 이 문제를 해결할 수 있습니다. 범위 잠금을 사용할 수 없으며 마이그레이션 프로세스 기간 동안 제거해야 합니다. [범위 잠금 관리에 대해 자세히 알아봅니다.](../../azure-resource-manager/management/lock-resources.md#portal)

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>'거부' 효과가 있는 정책으로 인해 규칙 마이그레이션이 금지됩니다.

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 만들어지고 클래식 경고 규칙이 삭제됩니다. 그러나 정책은 리소스를 만들지 못할 수 있습니다. 정책에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. 프로세스를 차단하는 정책은 [마이그레이션 도구에](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)나열됩니다. 다음 중 하나를 통해 이 문제를 해결하십시오.

- 정책 할당에서 마이그레이션 프로세스 기간 동안 구독 또는 리소스 그룹을 제외합니다. [정책 제외 범위 관리에 대해 자세히 알아봅니다.](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)
- 효과를 '감사' 또는 '추가'로 제거하거나 변경합니다(예: 누락된 태그와 관련된 문제를 해결할 수 있음). [정책 효과 관리에 대해 자세히 알아봅니다.](../../governance/policy/concepts/definition-structure.md#policy-rule)

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구 사용 방법](alerts-using-migration-tool.md)
- [마이그레이션 준비](alerts-prepare-migration.md)
