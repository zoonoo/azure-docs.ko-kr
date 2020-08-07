---
title: Azure Monitor 경고 마이그레이션 이해
description: 경고 마이그레이션의 작동 원리를 이해 하 고 문제를 해결 합니다.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 52a74593fcfbdc2c1e464077e4ae460f6a5a9c39
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852398"
---
# <a name="understand-migration-options-to-newer-alerts"></a>최신 경고에 대 한 마이그레이션 옵션 이해

아직 새 경고를 지원 하지 않는 리소스에 대 한 사용이 제한 되어 있지만 클래식 경고는 사용이 [중지](./monitoring-classic-retirement.md)됩니다. 새 날짜는 남은 경고 마이그레이션, [Azure Government 클라우드](../../azure-government/documentation-government-welcome.md)및 [Azure 중국 21vianet](https://docs.azure.cn/)에 대해 곧 발표 될 예정입니다.

이 문서에서는 수동 마이그레이션 및 자발적 마이그레이션 도구가 작동 하는 방법을 설명 합니다 .이 도구는 남은 경고 규칙을 마이그레이션하는 데 사용 됩니다. 또한 몇 가지 일반적인 문제에 대 한 해결 방법을 설명 합니다.

> [!IMPORTANT]
> 활동 로그 경고 (서비스 상태 경고 포함) 및 로그 경고는 마이그레이션의 영향을 받지 않습니다. 마이그레이션은 [여기](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)에 설명 된 클래식 경고 규칙에만 적용 됩니다.

> [!NOTE]
> 기존 경고 규칙이 잘못 된 경우 (예: [사용 되지 않는 사용 되지 않는 메트릭](#classic-alert-rules-on-deprecated-metrics) 또는 삭제 된 리소스에 있는 경우) 마이그레이션되지 않으며 서비스가 사용 중지 된 후에는 사용할 수 없게 됩니다.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>기존 경고를 최신 경고로 수동으로 마이그레이션

나머지 경고를 수동으로 마이그레이션하는 데 관심이 있는 고객은 다음 섹션을 사용 하 여 이미이 작업을 수행할 수 있습니다. 또한이 섹션에서는 리소스 공급자가 사용 중지 하 고, 현재 직접 마이그레이션할 수 없는 메트릭을 정의 합니다.

### <a name="guest-metrics-on-virtual-machines"></a>가상 컴퓨터의 게스트 메트릭

게스트 메트릭에 대 한 새 메트릭 경고를 만들려면 게스트 메트릭을 Azure Monitor 사용자 지정 메트릭 저장소로 보내야 합니다. 진단 설정에서 Azure Monitor 싱크를 사용 하도록 설정 하려면 다음 지침을 따르세요.

- [Windows Vm에 대 한 게스트 메트릭 사용](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux Vm에 대 한 게스트 메트릭 사용](collect-custom-metrics-linux-telegraf.md)

이러한 단계를 완료 한 후에는 게스트 메트릭에 새 메트릭 경고를 만들 수 있습니다. 새 메트릭 경고를 만든 후에는 클래식 경고를 삭제할 수 있습니다.

### <a name="storage-account-metrics"></a>스토리지 계정 메트릭

다음 메트릭에 대 한 경고를 제외 하 고 저장소 계정에 대 한 모든 클래식 경고를 마이그레이션할 수 있습니다.

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

% 메트릭에 대 한 클래식 경고 규칙은 [이전 및 새 저장소 메트릭 간의 매핑에](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics)따라 마이그레이션해야 합니다. 사용할 수 있는 새 메트릭은 절대 uri 이므로 임계값을 적절 하 게 수정 해야 합니다.

동일한 기능을 제공 하는 결합 된 메트릭이 없기 때문에 AnonymousThrottlingError, SASThrottlingError 및 ThrottlingError의 클래식 경고 규칙은 두 개의 새로운 경고로 분할 되어야 합니다. 임계값을 적절 하 게 조정 해야 합니다.

### <a name="cosmos-db-metrics"></a>Cosmos DB 메트릭

다음 메트릭에 대 한 경고를 제외 하 고 Cosmos DB 메트릭에 대 한 모든 클래식 경고를 마이그레이션할 수 있습니다.

- 초당 평균 요청 수
- 일관성 수준
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- 내부 서버 오류
- 분당 사용 된 최대 RUPM
- 초당 최대 RUs
- Mongo 수 실패 한 요청
- Mongo 실패 한 요청 삭제
- Mongo 실패 한 요청 삽입
- Mongo 실패 한 요청
- Mongo 기타 요청 요금
- Mongo 기타 요청 율
- Mongo 쿼리 실패 한 요청
- Mongo 업데이트 실패 요청
- 관찰 된 읽기 대기 시간
- 관찰 된 쓰기 대기 시간
- 서비스 가용성
- 스토리지 용량
- 제한된 요청
- 총 요청 수

초당 평균 요청, 일관성 수준, 분당 사용 된 최대 RUPM, 초당 최대 RUs, 관찰 된 읽기 대기 시간, 관찰 된 쓰기 대기 시간, 관찰 된 쓰기 대기 시간, 현재 [새 시스템](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)에서 저장소 용량을 사용할 수 없습니다.

Http 2xx, Http 3xx, Http 400, Http 401, 내부 서버 오류, 서비스 가용성, 제한 된 요청 및 총 요청 등의 요청 메트릭에 대 한 경고는 클래식 메트릭과 새로운 메트릭에 따라 달라 지므로 마이그레이션되지 않습니다. 임계값을 조정한 상태에서 이러한 경고를 수동으로 다시 만들어야 합니다.

동일한 기능을 제공 하는 결합 된 메트릭이 없으므로 Mongo 실패 한 요청에 대 한 경고는 여러 개의 경고로 분할 되어야 합니다. 임계값을 적절 하 게 조정 해야 합니다.

### <a name="classic-compute-metrics"></a>클래식 계산 메트릭

클래식 계산 리소스는 새 경고에서 아직 지원 되지 않으므로 클래식 계산 메트릭에 대 한 모든 경고는 마이그레이션 도구를 사용 하 여 마이그레이션되지 않습니다. 이러한 리소스 종류에 대 한 새 경고 지원은 현재 공개 미리 보기 상태 이며, 고객은 기존 경고 규칙에 따라 동등한 새 경고 규칙을 다시 만들 수 있습니다.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>사용 되지 않는 메트릭에 대 한 클래식 경고 규칙

이러한 규칙은 이전에 지원 되었지만 결국 사용 되지 않았던 메트릭에 대 한 클래식 경고 규칙입니다. 일부 고객에 게는 이러한 메트릭에 대 한 잘못 된 클래식 경고 규칙이 있을 수 있습니다. 이러한 경고 규칙은 유효 하지 않으므로 마이그레이션되지 않습니다.

| 리소스 유형| 사용 되지 않는 메트릭 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, 제한, dtu_consumption_percent, storage_used |
| Microsoft 웹/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft 웹/hostingEnvironments/작업자 풀 | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>새 경고 규칙 및 작업 그룹을 만드는 방법

마이그레이션 도구는 기존 경고 규칙을 동등한 새 경고 규칙 및 작업 그룹으로 변환 합니다. 대부분의 기존 경고 규칙의 경우, 같은 속성을 갖는 동일한 메트릭이 및와 동일 합니다 `windowSize` `aggregationType` . 그러나 기존 경고 규칙은 새 시스템에서 동등한 다른 메트릭을 가진 메트릭에 있습니다. 다음 원칙은 아래 섹션에 지정 된 경우를 제외 하 고 클래식 경고 마이그레이션에 적용 됩니다.

- **빈도**: 기존 또는 새 경고 규칙에서 조건에 대해 확인 하는 빈도를 정의 합니다. `frequency`클래식 경고 규칙의은 사용자가 구성할 수 없으며, 1 분 이었던 Application Insights 구성 요소를 제외 하 고 모든 리소스 유형에 대해 항상 5 분 이었습니다. 따라서 동등 규칙의 빈도는 각각 5 분에서 1 분으로 설정 됩니다.
- **집계 유형**: 대상 창에서 메트릭을 집계 하는 방법을 정의 합니다. `aggregationType`또한는 기존 경고와 대부분의 메트릭에 대 한 새 경고 사이에서 동일 합니다. 일부 경우에는 메트릭이 기존 경고와 새 경고 간에 차이가 있으므로 해당 `aggregationType` `primary Aggregation Type` 메트릭에 대해 정의 된 또는가 사용 됩니다.
- **Units**: 경고가 생성 되는 메트릭의 속성입니다. 이와 동등한 메트릭에는 단위가 다릅니다. 필요에 따라 임계값을 적절 하 게 조정 합니다. 예를 들어 원래 메트릭에 시간 (초)이 있고 해당 하는 새 메트릭은 단위 밀리초 인 경우 원래 임계값에 1000을 곱하여 동일한 동작을 보장 합니다.
- **창 크기**: 임계값과 비교할 메트릭 데이터를 집계 하는 기간을 정의 합니다. `windowSize`5 분, 15 분, 30 분, 1 시간, 3 시간, 6 시간, 12 시간, 1 일 같은 표준 값의 경우 해당 하는 새 경고 규칙에 대 한 변경 내용이 없습니다. 다른 값의 경우 가장 가까운를 `windowSize` 사용 하도록 선택 합니다. 대부분의 고객에 게는이 변경 내용에 영향을 주지 않습니다. 소수의 고객에 게는 정확히 동일한 동작을 얻기 위해 임계값을 조정 해야 할 수 있습니다.

다음 섹션에서는 새 시스템에서 동등한 다른 메트릭을 가진 메트릭에 대해 자세히 설명 합니다. 클래식 및 새 경고 규칙에 대해 동일 하 게 유지 되는 메트릭은 나열 되지 않습니다. 새 시스템에서 지원 되는 메트릭 목록은 [여기](metrics-supported.md)에서 찾을 수 있습니다.

### <a name="microsoftstorageaccountsservices"></a>Microsoft StorageAccounts/서비스

Blob, 테이블, 파일 및 큐와 같은 저장소 계정 서비스의 경우 다음과 같은 메트릭이 다음과 같이 동등한 메트릭에 매핑됩니다.

| 클래식 경고의 메트릭 | 새 경고에 해당 하는 메트릭 | 주석|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| "ResponseType" = "AuthorizationError" 및 "Authentication" = "Anonymous" 차원이 포함 된 트랜잭션 메트릭| |
| AnonymousClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousClientTimeOutError| 차원이 "ResponseType" = "ClientTimeOutError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousNetworkError | "ResponseType" = "NetworkError" 및 "Authentication" = "Anonymous" 차원이 포함 된 트랜잭션 메트릭 | |
| AnonymousServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousServerTimeOutError | 차원이 "ResponseType" = "ServerTimeOutError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousSuccess | 차원이 "ResponseType" = "Success" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AuthorizationError | 차원이 "ResponseType" = "AuthorizationError" 인 트랜잭션 메트릭 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 용량 | BlobCapacity | `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| ClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 인 트랜잭션 메트릭  | |
| ClientTimeoutError | 차원이 "ResponseType" = "ClientTimeOutError" 인 트랜잭션 메트릭 | |
| ContainerCount | ContainerCount | `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| NetworkError | 차원이 "ResponseType" = "NetworkError" 인 트랜잭션 메트릭 | |
| ObjectCount | BlobCount| `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| SASAuthorizationError | "ResponseType" = "AuthorizationError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| SASClientTimeOutError | "ResponseType" = "ClientTimeOutError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASNetworkError | "ResponseType" = "NetworkError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| SASServerTimeOutError | "ResponseType" = "ServerTimeOutError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASSuccess | 차원이 "ResponseType" = "Success" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| ServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 인 트랜잭션 메트릭 | |
| ServerTimeOutError | 차원이 "ResponseType" = "ServerTimeOutError" 인 트랜잭션 메트릭  | |
| Success | 차원이 "ResponseType" = "Success" 인 트랜잭션 메트릭 | |
| TotalBillableRequests| 의 | |
| TotalEgress | 송신 | |
| TotalIngress | 수신 | |
| TotalRequests | 의 | |

### <a name="microsoftinsightscomponents"></a>Microsoft 인 사이트/구성 요소

Application Insights에 대해 다음과 같은 메트릭이 표시 됩니다.

| 클래식 경고의 메트릭 | 새 경고에 해당 하는 메트릭 | 주석|
|--------------------------|---------------------------------|---------|
| availabilityMetric | availabilityResults/availabilityPercentage|   |
| Durationmetric.value | availabilityResults/duration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| basicExceptionBrowser. 개수 | exceptions/browser|  ' `aggregationType` Sum ' 대신 ' count '를 사용 합니다. |
| basicExceptionServer. count | exceptions/server| ' `aggregationType` Sum ' 대신 ' count '를 사용 합니다.  |
| clientPerformance. Clientperformance. 값 | browserTimings/processingDuration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| clientPerformance. networkConnection. 값 | browserTimings/networkDuration|  원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다. |
| clientPerformance | browserTimings/receiveDuration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| clientPerformance | browserTimings/sendDuration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| clientPerformance. total. 값 | browserTimings/totalDuration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| available_bytes. 값 | performanceCounters/memoryAvailableBytes|   |
| io_data_bytes_per_sec. 값 | performanceCounters/processIOBytesPerSecond|   |
| number_of_exceps_thrown_per_sec. 값 | performanceCounters/exceptionsPerSecond|   |
| percentage_processor_time_normalized. 값 | performanceCounters/processCpuPercentage|   |
| percentage_processor_time. 값 | performanceCounters/processCpuPercentage| 원래 메트릭이 모든 코어에서 수행 되 고 새 메트릭이 하나의 코어로 정규화 되므로 임계값을 적절 하 게 수정 해야 합니다. 마이그레이션 도구는 임계값을 변경 하지 않습니다.  |
| percentage_processor_total. 값 | performanceCounters/processorCpuPercentage|   |
| process_private_bytes. 값 | performanceCounters/processPrivateBytes|   |
| request_execution_time. 값 | performanceCounters/requestExecutionTime|   |
| requests_in_application_queue. 값 | performanceCounters/requestsInQueue|   |
| requests_per_sec. 값 | performanceCounters/requestsPerSecond|   |
| 요청. 기간 | requests/duration| 원래 임계값을 1000에 곱하여 클래식 메트릭의 단위는 초 단위이 고 새 임계값은 밀리초 단위입니다.  |
| 요청 요금 | 요청/속도|   |
| requestFailed | requests/failed| ' `aggregationType` Sum ' 대신 ' count '를 사용 합니다.   |
| 보기 수 | pageViews/count| ' `aggregationType` Sum ' 대신 ' count '를 사용 합니다.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Cosmos DB에 대해 다음과 같은 메트릭이 표시 됩니다.

| 클래식 경고의 메트릭 | 새 경고에 해당 하는 메트릭 | 주석|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| 데이터 크기 | DataUsage| |
| 문서 수 | DocumentCount||
| 인덱스 크기 | IndexUsage||
| Mongo 수 요청 요금| 차원이 "CommandName" = "count" 인 MongoRequestCharge||
| Mongo 수 요청 빈도 | 차원이 "CommandName" = "count" 인 MongoRequestsCount||
| Mongo 삭제 요청 요금 | 차원이 "CommandName" = "delete" 인 MongoRequestCharge||
| Mongo 삭제 요청 속도 | 차원이 "CommandName" = "delete" 인 MongoRequestsCount||
| Mongo 삽입 요청 요금 | 차원이 "CommandName" = "insert" 인 MongoRequestCharge||
| Mongo 삽입 요청 속도 | 차원이 "CommandName" = "insert" 인 MongoRequestsCount||
| Mongo 쿼리 요청 요금 | 차원이 "CommandName" = "find" 인 MongoRequestCharge||
| Mongo 쿼리 요청 속도 | 차원이 "CommandName" = "find" 인 MongoRequestsCount||
| Mongo 업데이트 요청 요금 | 차원이 "CommandName" = "update" 인 MongoRequestCharge||
| 서비스를 사용할 수 없음| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>동일한 작업 그룹을 만드는 방법

클래식 경고 규칙에는 경고 규칙 자체에 연결 된 메일, webhook, 논리 앱 및 runbook 작업이 있습니다. 새 경고 규칙은 여러 경고 규칙에서 다시 사용할 수 있는 작업 그룹을 사용 합니다. 마이그레이션 도구는 작업을 사용 하는 경고 규칙의 수에 관계 없이 동일한 작업에 대 한 단일 작업 그룹을 만듭니다. 마이그레이션 도구에서 만든 작업 그룹은 명명 형식 ' Migrated_AG * '를 사용 합니다.

> [!NOTE]
> 클래식 경고는 클래식 관리자 역할에 알리기 위해 사용 하는 경우 클래식 관리자의 로캘을 기반으로 지역화 된 전자 메일을 보냈습니다. 새 경고 전자 메일은 작업 그룹을 통해 전송 되며 영어로만 전송 됩니다.

## <a name="rollout-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객에 게 단계적으로 배포 됩니다. 구독 소유자는 도구를 사용 하 여 구독을 마이그레이션할 준비가 되 면 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 도구는 단계에서 롤아웃 되기 때문에 초기 단계에서 일부 구독을 아직 마이그레이션할 준비가 되지 않은 것을 볼 수 있습니다.

대부분의 구독은 현재 마이그레이션 준비로 표시 되어 있습니다. 다음 리소스 유형에 대 한 클래식 경고가 있는 구독만 아직 마이그레이션할 준비가 되지 않았습니다.

- Microsoft.classiccompute/domainNames/슬롯/역할
- Microsoft 인 사이트/구성 요소

## <a name="who-can-trigger-the-migration"></a>누가 마이그레이션을 트리거할 수 있나요?

구독 수준에서 모니터링 참여자의 기본 제공 역할이 있는 사용자는 마이그레이션을 트리거할 수 있습니다. 다음 사용 권한으로 사용자 지정 역할이 있는 사용자는 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 위의 권한 외에도 구독을 AlertsManagement 리소스 공급자에 등록 해야 합니다. Application Insights에서 실패 이상 경고를 성공적으로 마이그레이션하려면이 작업이 필요 합니다. 

## <a name="common-problems-and-remedies"></a>일반적인 문제 및 해결 방법

[마이그레이션을 트리거한](alerts-using-migration-tool.md)후에는 마이그레이션이 완료 되었음을 알리거나 사용자에 게 필요한 조치가 있는지 알리기 위해 제공한 주소로 전자 메일을 받게 됩니다. 이 섹션에서는 몇 가지 일반적인 문제와 이러한 문제를 처리 하는 방법을 설명 합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독의 클래식 경고 규칙에 대 한 최근 변경 내용으로 인해 구독을 마이그레이션할 수 없습니다. 이 문제는 일시적입니다. 마이그레이션 상태가 마이그레이션 **준비** 를 며칠 후에 다시 시작 하면 마이그레이션을 다시 시작할 수 있습니다.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>범위 잠금-규칙을 마이그레이션하지 못하게 합니다.

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 생성 된 다음 클래식 경고 규칙이 삭제 됩니다. 그러나 범위 잠금을 통해 리소스를 만들거나 삭제 하지 못할 수 있습니다. 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션하지 못했습니다. [마이그레이션 도구](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)에 나열 된 구독, 리소스 그룹 또는 리소스에 대 한 범위 잠금을 제거 하 고 마이그레이션을 다시 트리거하는 방법으로이 문제를 해결할 수 있습니다. 범위 잠금은 사용 하지 않도록 설정할 수 없으며 마이그레이션 프로세스가 진행 되는 동안에는 제거 해야 합니다. [범위 잠금 관리에 대해 자세히 알아보세요](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>' 거부 ' 효과가 적용 된 정책을 사용 하 여 규칙을 마이그레이션하지 못함

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 생성 된 다음 클래식 경고 규칙이 삭제 됩니다. 그러나 정책을 통해 리소스를 만들지 못할 수 있습니다. 정책에 따라 일부 또는 모든 규칙을 마이그레이션하지 못했습니다. 프로세스를 차단 하는 정책은 [마이그레이션 도구](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)에 나열 됩니다. 다음 중 하나를 수행 하 여이 문제를 해결 합니다.

- 구독을 제외 하거나 정책 할당의 마이그레이션 프로세스 기간에 대 한 리소스 그룹을 제외 합니다. [정책 제외 범위 관리에 대해 자세히 알아보세요](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- ' 감사 ' 또는 ' 추가 '에 대 한 영향을 제거 하거나 변경 합니다 (예: 누락 된 태그와 관련 된 문제를 해결할 수 있음). [정책 효과 관리에 대해 자세히 알아보세요](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구 사용 방법](alerts-using-migration-tool.md)
- [마이그레이션 준비](alerts-prepare-migration.md)
