---
title: Azure Monitor 경고에 대 한 자발적인 마이그레이션 도구 작동 방식을 이해합니다
description: 경고 마이그레이션 도구 작동 원리를 이해 하 고 문제를 해결 합니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295521"
---
# <a name="understand-how-the-migration-tool-works"></a>마이그레이션 도구 작동 원리를 이해합니다

로 [발표](monitoring-classic-retirement.md), Azure Monitor에서 클래식 경고 2019 년 8 월 31 일에 중단 되는 (원래 년 6 월 30 2019 년 된). 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고 마이그레이션 자체 트리거 하려는 고객에 게 Azure portal에서 사용할 수 있습니다.

이 문서는 자발적 마이그레이션 도구 작동 원리를 설명 합니다. 또한 몇 가지 일반적인 문제에 대 한 해결책을 설명합니다.

> [!NOTE]
> 클래식 경고 마이그레이션에 대 한 사용 중지 날짜 마이그레이션 도구의 롤아웃을에서 지연으로 인해 되었습니다 [2019 년 8 월 31 일에 확장](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) 2019 년 6 월 30 일의 처음 발표 된 날짜에서.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>마이그레이션할 수 없습니다는 클래식 경고 규칙

> [!IMPORTANT]
> 활동 로그 경고 (포함 하 여 서비스 상태 경고) 및 로그 경고는 마이그레이션에서 영향을 받지 않습니다. 마이그레이션 설명 하는 클래식 경고 규칙에만 적용 됩니다 [여기](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)합니다.

이 도구는 거의 모든 마이그레이션할 수도 있지만 [클래식 경고 규칙](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), 일부의 예외가 있습니다. 이 도구를 사용 하 여 (또는 2019 년 9 월부터 자동 마이그레이션 중) 다음 경고 규칙을 마이그레이션할 수 없습니다.

- 가상 컴퓨터 게스트 메트릭 (Windows 및 Linux)에서 클래식 경고 규칙입니다. 참조 된 [새로운 메트릭 경고에서 이러한 경고 규칙을 다시 만들기에 대 한 지침](#guest-metrics-on-virtual-machines) 이 문서의 뒷부분에 나오는.
- 클래식 저장소 메트릭에 대 한 클래식 경고 규칙입니다. 참조 된 [클래식 저장소 계정을 모니터링 하는 것에 대 한 지침](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)합니다.
- 일부 저장소 계정 메트릭에서 클래식 경고 규칙입니다. 참조 [세부 정보](#storage-account-metrics) 이 문서의 뒷부분에 나오는.
- 일부 Cosmos DB 메트릭에 대 한 클래식 경고 규칙입니다. 세부 정보는 향후 업데이트에서 추가 됩니다.

구독의 클래식 이러한 규칙에 있는 경우에 수동으로 마이그레이션해야 합니다. 자동 마이그레이션을 제공할 수 있으므로 이러한 유형의 모든 기존, 클래식 메트릭 경고 2020 년 6 월 까지는 작동 하도록 계속 됩니다. 이 확장을 통해 새 경고에 이동 시간을 제공 합니다. 그러나 2019 년 8 월 이후 새 클래식 경고를 만들 수 있습니다.

> [!NOTE]
> 위와 같이 나열 된 예외, 외에도 클래식 경고 규칙을 유효 하지 않은 경우 즉,에 있는 [메트릭을 사용 되지 않는](#classic-alert-rules-on-deprecated-metrics) 또는 삭제 된 리소스 자발적으로 마이그레이션하는 동안 마이그레이션할 수는 없습니다. 자동 마이그레이션이 발생 하는 경우 이러한 잘못 된 클래식 경고 규칙 삭제 됩니다.

### <a name="guest-metrics-on-virtual-machines"></a>가상 머신에서 게스트 메트릭

게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수 있습니다, 전에 게스트 메트릭은 Azure Monitor 메트릭 사용자 지정 저장소에 전송 되어야 합니다. 진단 설정에서 Azure Monitor 싱크에 사용 하도록 설정 하려면 이러한 지침을 따릅니다.

- [Windows Vm에 대 한 게스트 메트릭을 사용 하도록 설정](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux Vm 용 게스트 메트릭 사용](collect-custom-metrics-linux-telegraf.md)

다음이 단계를 완료 게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수 있습니다. 및 새로운 메트릭 경고를 만든 후 클래식 경고를 삭제할 수 있습니다.

### <a name="storage-account-metrics"></a>저장소 계정 메트릭

이러한 메트릭에 대 한 경고를 제외 하 고 저장소 계정에서 모든 클래식 경고를 마이그레이션할 수 있습니다.

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

백분율 기준에 대 한 규칙을 마이그레이션해야 하는 클래식 경고에 따라 [이전 및 새 storage 메트릭 간의 매핑을](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)합니다. 임계값을 사용할 수 있는 새 메트릭 항목을 절대 이므로 적절 하 게 수정 해야 합니다.

클래식 경고 규칙에 AnonymousThrottlingError를 SASThrottlingError ThrottlingError 동일한 기능을 제공 하는 결합 된 메트릭이 없습니다 있기 때문에 두 개의 새 경고도 분할 해야 합니다. 임계값을 적절 하 게 적응 해야 합니다.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>사용 되지 않는 메트릭에 대 한 클래식 경고 규칙

이들은 지원 하 던 하지만 결국 사용 되지 않는 메트릭에 대 한 클래식 경고 규칙입니다. 고객의 작은 비율 이러한 메트릭에 대 한 잘못 된 클래식 경고 규칙이 있을 수 있습니다. 잘못 된 이러한 경고 규칙 때문에 마이그레이션할 수 없습니다.

| 리소스 종류| 사용 되지 않는 메트릭 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>해당 하는 새 경고 규칙 및 작업 그룹은 생성 하는 방법

마이그레이션 도구를 해당 새 경고 규칙 및 작업 그룹에는 클래식 경고 규칙을 변환합니다. 클래식 경고 규칙의 대부분의 경우 해당 하는 새 경고 규칙에 동일한 속성을 사용 하 여 동일한 메트릭에 같은 `windowSize` 고 `aggregationType`입니다. 그러나 일부 클래식 경고 규칙은 다른, 동일한 메트릭을 새 시스템에 있는 메트릭에 대 한 가지 있습니다. 아래 섹션에 지정 되지 않은 경우에 마이그레이션을 위해 클래식 경고의 다음과 같은 원칙이 적용 됩니다.

- **빈도**: 조건에 대 한 클래식 또는 새 경고 규칙을 확인 하는 빈도 정의 합니다. `frequency` 클래식 경고 규칙에서 사용자가 구성할 수 없었으며 1 분 이었습니다 Application Insights 구성 요소를 제외한 모든 리소스 유형에 대해 5 분 동안 있었습니다. 따라서 해당 규칙의 빈도 또한 5 분에서 1 분을 각각 설정 합니다.
- **집계 유형이**: 원하는 창에 대 한 메트릭이 집계 되는 방식을 정의 합니다. `aggregationType` 클래식 경고 및 대부분의 메트릭에 대 한 새 경고 사이 같다고 이기도 합니다. 메트릭은 새 경고에 해당 하는 클래식 경고 사이의 다른 이므로 경우도 `aggregationType` 또는 `primary Aggregation Type` 메트릭 정의 합니다.
- **단위**: 경고가 생성 되는 메트릭 속성입니다. 해당 하는 몇 가지 메트릭을 다른 단위가 있습니다. 임계값은 필요에 따라 적절 하 게 조정 됩니다. 예를 들어 원래 메트릭 단위로 시간 (초)를 포함 하지만 해당 하는 새 메트릭 단위로 시간 (밀리초)을 하는 경우 원래 임계값은 1000이 곱해집니다 동일한 동작을 확인 합니다.
- **창 크기**: 메트릭이 데이터가 집계 되는 임계값과 비교 하는 창을 정의 합니다. 표준 `windowSize` 5mins, 15mins 30mins를 같은 값 1 시간, 3 시간, 6 시간, 1 일 12 시간 동안 변경 되지 않는 해당 새 경고 규칙에 대 한 합니다. 다른 값을 가장 가까운 `windowSize` 사용할 선택 됩니다. 대부분의 고객에 게이 변경으로 인해 영향이 있습니다. 고객의 작은 비율을 정확 하 게 동일한 동작을 가져오도록 임계값을 조정할 필요가 있을 수 있습니다.

다음 섹션에서는 새 시스템에서 다른, 동일한 메트릭을 사용할 수 있는 메트릭을 자세히 다룹니다. 클래식 및 새 경고 규칙에 대 한 동일 하 게 유지 하는 모든 메트릭을 나열 되지 않습니다. 새 시스템에서 지원 되는 메트릭 목록을 찾을 수 있습니다 [여기](metrics-supported.md)합니다.

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Blob, 테이블, 파일 및 큐와 같은 저장소 계정 서비스에서 다음 메트릭은 아래와 같이 해당 메트릭을에 매핑됩니다.

| 클래식 경고에 대 한 메트릭 | 새 경고에 해당 하는 메트릭 | 설명|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "AuthorizationError" 및 "인증" = = "익명"| |
| AnonymousClientOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientOtherError" 및 "인증" = = "익명" | |
| AnonymousClientTimeOutError| 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientTimeOutError" 및 "인증" = = "익명" | |
| AnonymousNetworkError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "NetworkError" 및 "인증" = = "익명" | |
| AnonymousServerOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerOtherError" 및 "인증" = = "익명" | |
| AnonymousServerTimeOutError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerTimeOutError" 및 "인증" = = "익명" | |
| AnonymousSuccess | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "Success" 및 "인증" = = "익명" | |
| AuthorizationError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "AuthorizationError" = | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 용량 | BlobCapacity | 사용 하 여 `aggregationType` '마지막' 대신 ' 평균'. 메트릭은 Blob 서비스에만 적용 됩니다. |
| ClientOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientOtherError" =  | |
| ClientTimeoutError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientTimeOutError" = | |
| ContainerCount | ContainerCount | 사용 하 여 `aggregationType` '마지막' 대신 ' 평균'. 메트릭은 Blob 서비스에만 적용 됩니다. |
| NetworkError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "NetworkError" = | |
| ObjectCount | BlobCount| 사용 하 여 `aggregationType` '마지막' 대신 ' 평균'. 메트릭은 Blob 서비스에만 적용 됩니다. |
| SASAuthorizationError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "AuthorizationError" 및 "인증" = = "SAS" | |
| SASClientOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientOtherError" 및 "인증" = = "SAS" | |
| SASClientTimeOutError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ClientTimeOutError" 및 "인증" = = "SAS" | |
| SASNetworkError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "NetworkError" 및 "인증" = = "SAS" | |
| SASServerOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerOtherError" 및 "인증" = = "SAS" | |
| SASServerTimeOutError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerTimeOutError" 및 "인증" = = "SAS" | |
| SASSuccess | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "Success" 및 "인증" = = "SAS" | |
| ServerOtherError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerOtherError" = | |
| ServerTimeOutError | 차원 "ResponseType"를 사용 하 여 트랜잭션 메트릭 "ServerTimeOutError" =  | |
| 성공 | 트랜잭션 메트릭은 차원 "ResponseType" = "Success" | |
| TotalBillableRequests| 트랜잭션 | |
| TotalEgress | 송신 | |
| TotalIngress | 수신 | |
| TotalRequests | 트랜잭션 | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Application Insights에 대 한 해당 메트릭은 아래 표시 된 대로 다음과 같습니다.

| 클래식 경고에 대 한 메트릭 | 새 경고에 해당 하는 메트릭 | 설명|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| basicExceptionBrowser.count | exceptions/browser|  사용 하 여 `aggregationType` '합계' 대신 ' count'. |
| basicExceptionServer.count | exceptions/server| 사용 하 여 `aggregationType` '합계' 대신 ' count'.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| clientPerformance.total.value | browserTimings/totalDuration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| 임계값을 원래 메트릭 모든 코어 되었으며 새 메트릭 1 개 코어로 정규화 된 대로 적절히 수정 해야 합니다. 마이그레이션 도구는 임계값을 변경 되지 않습니다.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| 클래식 메트릭에 대 한 단위 시간 (초)와 새로운 하나는 밀리초에서 같이 원래 임계값을 1000 곱하십시오.  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| 사용 하 여 `aggregationType` '합계' 대신 ' count'.   |
| view.count | pageViews/count| 사용 하 여 `aggregationType` '합계' 대신 ' count'.   |

### <a name="how-equivalent-action-groups-are-created"></a>해당 하는 작업 그룹이 생성 되는 방법

클래식 경고 규칙이 전자 메일, 웹 후크를 경고에 연결 된 논리 앱 및 runbook 작업 규칙 자체입니다. 새 경고 규칙을 여러 경고 규칙에서 다시 사용할 수 있는 작업 그룹을 사용 합니다. 마이그레이션 도구는 얼마나 많은 경고 규칙에는 작업을 사용 하는 관계 없이 동일한 작업에 대 한 단일 작업 그룹을 만듭니다. 마이그레이션 도구에서 만든 작업 그룹 명명 형식을 ' Migrated_AG *'를 사용 합니다.

## <a name="rollout-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객에 게 단계별로 출시 됩니다. 구독 소유자는 구독 도구를 사용 하 여 마이그레이션할 준비가 되 면 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 도구는 단계에서 롤아웃 되는 때문에 일부 구독 준비가 되지 않은 아직 초기 단계에서 마이그레이션할 수를 볼 수 있습니다.

구독 중 대부분은 현재 마이그레이션에 대 한 준비로 표시 됩니다. 다음 리소스 유형에 대 한 클래식 경고가 있는 구독만 여전히 마이그레이션에 대 한 준비 되지 않았습니다.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>마이그레이션을 트리거할 수는?

Monitoring Contributor의 기본 제공 역할은 구독 수준에 있는 사용자가 마이그레이션을 트리거할 수 있습니다. 다음 권한이 있는 사용자 지정 역할이 있는 사용자 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> 위의 권한 외 또한 Microsoft.AlertsManagement 리소스 공급자를 사용 하 여 대 구독 등록 되어야 합니다. Application Insights에 대 한 오류 변칙 경고를 성공적으로 마이그레이션하려면이 필요 합니다. 

## <a name="common-problems-and-remedies"></a>일반적인 문제 및 해결책

한 후 [마이그레이션 트리거](alerts-using-migration-tool.md), 마이그레이션이 완료 되었는지 또는 모든 작업에서 필요한 경우 사용자에 게 알려 주기 위해 제공 된 주소로 전자 메일을 받게 됩니다. 이 섹션에서는 몇 가지 일반적인 문제 및 처리 하는 방법을 설명 합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독에서 클래식 경고 규칙에 몇 가지 최근 변경 내용으로 인해 구독을 마이그레이션할 수 없습니다. 이 문제는 일시적입니다. 마이그레이션 상태를 다시 이동한 후 마이그레이션을 다시 시작할 수 있습니다 **마이그레이션에 대 한 준비** 몇 일 내에 있습니다.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>정책 또는 범위 잠금 규칙을 마이그레이션에서 못하고 있습니다.

마이그레이션의 일부로, 새 메트릭 경고 및 새 작업 그룹에 만들어지고 클래식 경고 규칙 삭제 됩니다. 그러나 정책이 나 범위 잠금 리소스를 만들지 못하게 하지 못하고 있습니다. 정책 또는 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. 범위 잠금이 또는 정책을 일시적으로 제거 하 고 마이그레이션을 다시 트리거이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구를 사용 하는 방법](alerts-using-migration-tool.md)
- [마이그레이션을 위한 준비](alerts-prepare-migration.md)
