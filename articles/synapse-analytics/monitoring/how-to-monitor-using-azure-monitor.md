---
title: Azure Monitor를 사용 하 여 Synapse Analytics를 모니터링 하는 방법
description: Azure Monitor 메트릭, 경고 및 로그를 사용 하 여 Synapse Analytics 작업 영역을 모니터링 하는 방법을 알아봅니다.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 33cd19f7bb1881b17f1ca9080a9634162014808a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574299"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics 작업 영역에서 Azure Monitor 사용

클라우드 응용 프로그램은 복잡 하며 많은 부분을 이동 하 고 있습니다. 모니터는 응용 프로그램이 정상 상태로 유지 되 고 실행 되도록 하는 데 도움이 되는 데이터를 제공 합니다. 모니터는 잠재적인 문제를 방지 하 고 이전 문제를 해결 하는 데도 도움이 됩니다. 모니터링 데이터를 사용 하 여 응용 프로그램에 대 한 깊은 통찰력을 얻을 수 있습니다. 이 정보는 응용 프로그램 성능 및 유지 관리를 개선 하는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화 하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대 한 기본 수준의 인프라 메트릭, 경고 및 로그를 제공 합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. Azure Synapse Analytics는 Azure Monitor에서 진단 로그를 쓸 수 있습니다.

자세한 내용은 [Azure Monitor 개요](../../azure-monitor/overview.md)를 참조하세요.

## <a name="metrics"></a>메트릭

모니터를 사용 하 여 Azure 워크 로드의 성능 및 상태를 파악할 수 있습니다. 가장 중요 한 유형의 모니터 데이터는 메트릭 (성능 카운터 라고도 함)입니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. 모니터는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 하 고 사용 하는 여러 가지 방법을 제공 합니다.

이러한 메트릭에 액세스 하려면 [Azure Monitor data platform](../../azure-monitor/data-platform.md)의 지침을 완료 합니다.

### <a name="workspace-level-metrics"></a>작업 영역 수준 메트릭

작업 영역에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**                           | **메트릭 범주, 표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | 통합, 활동 실행 메트릭                     | 개수    | 합계 (기본값), 개수                | 1 분 기간 내에 발생 한 작업 실행의 총 수입니다. </br></br> 이 메트릭의 결과 차원을 사용 하 여 성공, 실패 또는 취소 된 최종 상태를 필터링 합니다.|
| IntegrationPipelineRunsEnded         | 통합, 파이프라인 실행 메트릭                     | 개수    | 합계 (기본값), 개수                | 1 분 기간 내에 발생 했거나 종료 된 총 파이프라인 실행 수입니다. </br></br> 이 메트릭의 결과 차원을 사용 하 여 성공, 실패 또는 취소 된 최종 상태를 필터링 합니다. |
| IntegrationTriggerRunsEnded          | 통합, 트리거 실행 메트릭                      | 개수    | 합계 (기본값), 개수                | 1 분 기간 내에 발생 했거나 종료 된 트리거 실행의 총 수입니다. </br></br> 이 메트릭의 결과 차원을 사용 하 여 성공, 실패 또는 취소 된 최종 상태를 필터링 합니다. |
| BuiltinSqlPoolDataProcessedBytes     | 기본 제공 SQL 풀, 처리 된 데이터 (바이트) | Byte | 합계 (기본값) | 기본 제공 서버를 사용 하지 않는 SQL 풀에서 처리 된 데이터의 양입니다. |
| BuiltinSqlPoolLoginAttempts          | 기본 제공 SQL 풀, 로그인 시도 | 개수 | 합계 (기본값) | 서버를 사용 하지 않는 기본 제공 SQL 풀의 로그인 시도 횟수입니다. |
| BuiltinSqlPoolDataRequestsEnded      | 기본 제공 SQL 풀, 종료 된 요청 (바이트) | 개수 | 합계 (기본값) | 기본 제공 서버를 사용 하지 않는 SQL 풀에 대해 종료 된 SQL 요청 수입니다. </br></br> 이 메트릭의 결과 차원을 사용 하 여 최종 상태별로 필터링 합니다. |

### <a name="dedicated-sql-pool-metrics"></a>전용 SQL 풀 메트릭

다음은 전용 SQL 풀에서 내보내는 메트릭 중 일부입니다.

| **메트릭**                           | **표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU 제한                       | 개수   | Max (기본값), Min, Avg | 구성 된 SQL 풀 크기 |
| DWUUsed                             | DWU 사용됨                        | 개수   | Max (기본값), Min, Avg | SQL 풀에서의 사용에 대 한 상위 수준 표시를 나타냅니다. DWU 제한 * DWU 백분율로 측정 됩니다. |
| DWUUsedPercent                      | DWU 사용 백분율             | 백분율 | Max (기본값), Min, Avg | SQL 풀에서의 사용에 대 한 상위 수준 표시를 나타냅니다. CPU 비율과 데이터 IO 비율의 최대값을 취하여 측정 됩니다. |
| ConnectionsBlockedByFirewall        | 방화벽에 의해 차단 된 연결 | 개수   | 합계 (기본값)   | 방화벽 규칙에 의해 차단 된 연결 수입니다. SQL 풀에 대 한 액세스 제어 정책을 다시 방문 하 고, 수가 높으면 이러한 연결을 모니터링 합니다. |
| AdaptiveCacheHitPercent             | 적응 캐시 적중률   | 백분율 | Max (기본값), Min, Avg | 워크 로드가 적응 캐시를 얼마나 잘 활용 하 고 있는 지를 측정 합니다. 캐시 적중률 메트릭과 함께이 메트릭을 사용 하 여 추가 용량을 조정 하거나 작업을 다시 실행 하 여 캐시를 하이드레이션 하며 나중 여부를 결정 합니다. |
| AdaptiveCacheUsedPercent            | 적응 캐시 사용 백분율  | 백분율 | Max (기본값), Min, Avg | 워크 로드가 적응 캐시를 얼마나 잘 활용 하 고 있는 지를 측정 합니다. 캐시 사용 백분율 메트릭에이 메트릭을 사용 하 여 추가 용량에 대해 크기를 조정할지 또는 작업을 다시 실행 하 여 캐시를 하이드레이션 하며 나중 여부를 결정 합니다. |
| LocalTempDBUsedPercent              | 로컬 tempdb 사용 백분율    | 백분율 | Max (기본값), Min, Avg | 모든 계산 노드의 로컬 tempdb 사용률-5 분 마다 값이 내보내집니다. |
| MemoryUsedPercent                   | 사용 되는 메모리 백분율          | 백분율 | Max (기본값), Min, Avg | SQL 풀의 모든 노드에 대 한 메모리 사용률 |
| CPUPercent                          | 사용 되는 CPU 비율             | 백분율 | Max (기본값), Min, Avg | SQL 풀의 모든 노드에 걸친 CPU 사용률 |
| Connections                         | Connections                     | 개수   | 합계 (기본값)  | SQL 풀에 대 한 총 로그인 수 |
| ActiveQueries                      | 활성 쿼리                 | 개수   | 합계 (기본값)   | 활성 쿼리입니다. 필터링 되지 않은이 메트릭과 분할 되지 않은 메트릭을 사용 하 여 시스템에서 실행 중인 모든 활성 쿼리를 표시 합니다. |
| QueuedQueries                      | 대기 중인 쿼리                  | 개수   | 합계 (기본값)   | 최대 동시성 제한에 도달한 후 큐에 대기 중인 총 요청 수 |
| WLGActiveQueries                   | 작업 그룹 활성 쿼리   | 개수   | 합계 (기본값)   | 작업 그룹 내의 활성 쿼리입니다. 필터링 되지 않은이 메트릭과 분할 되지 않은 메트릭을 사용 하 여 시스템에서 실행 중인 모든 활성 쿼리를 표시 합니다. |
| WLGActiveQueriesTimeouts           | 작업 그룹 쿼리 시간 제한   | 개수   | 합계 (기본값)   | 시간이 초과 된 작업 그룹에 대 한 쿼리입니다. 이 메트릭에 의해 보고 된 쿼리 시간 제한은 쿼리가 실행을 시작한 후에만 수행 됩니다 (잠금 또는 리소스 대기로 인 한 대기 시간을 포함 하지 않음). |
| WLGQueuedQueries                   | 작업 그룹 큐에 대기 중인 쿼리   | 개수   | 합계 (기본값)   | 최대 동시성 제한에 도달한 후 큐에 대기 중인 총 요청 수 |
| WLGAllocationBySystemPercent        | 시스템 비율별 작업 그룹 할당 | 백분율 | Max (기본값), Min, Avg, Sum | 전체 시스템에 상대적인 리소스 할당 비율 |
| WLGAllocationByEffectiveCapResourcePercent   | 최대 리소스 비율별 작업 그룹 할당 | 백분율 | Max (기본값), Min, Avg | 작업 그룹당 유효 캡 리소스 비율을 기준으로 하는 리소스의 백분율 할당을 표시 합니다. 이 메트릭은 작업 그룹의 효과적인 사용률을 제공 합니다. |
| WLGEffectiveCapResourcePercent      | 유효 상한 리소스 비율  | 백분율 | Max (기본값), Min, Avg | 작업 그룹에 대 한 유효 상한 리소스 비율입니다. Min_percentage_resource > 0 인 다른 작업 그룹이 있는 경우 effective_cap_percentage_resource 비례적으로 줄어듭니다. |
| WLGEffectiveMinResourcePercent      | 유효 최소 리소스 비율  | 백분율 | Max (기본값), Min, Avg, Sum | 서비스 수준 및 작업 그룹 설정을 고려 하 여 유효 min 리소스 비율 설정이 허용 됩니다. 낮은 서비스 수준에서 효과적인 min_percentage_resource을 더 높은 수준으로 조정할 수 있습니다. |

### <a name="apache-spark-pool-metrics"></a>Apache Spark 풀 메트릭

Apache Spark 풀에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**                           | **메트릭 범주, 표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Apache Spark 응용 프로그램 종료  | 개수 | 합계 (기본값) | 종료 된 Apache Spark 풀 응용 프로그램 수 |
| BigDataPoolAllocatedCores     | Apache Spark 풀에 할당 된 vCores 수                 | 개수 | Max (기본값), Min, Avg | Apache Spark 풀에 할당 된 vCores |
| BigDataPoolAllocatedMemory    | Apache Spark 풀에 할당 된 메모리 양 (GB)            | 개수 | Max (기본값), Min, Avg | Apache Spark 풀에 할당 된 메모리 (GB) |
| BigDataPoolApplicationsActive | 활성 Apache Spark 응용 프로그램 | 개수 | Max (기본값), Min, Avg | 활성 Apache Spark 풀 응용 프로그램 수 |

## <a name="alerts"></a>경고

Azure Portal에 로그인 하 고   >  **경고** 모니터링을 선택 하 여 경고를 만듭니다.

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙** 을 선택하여 새 경고를 만듭니다.

1. 경고가 발생 해야 하는 시점을 지정 하는 **경고 조건을** 정의 합니다.

    > [!NOTE]
    > **리소스 유형별 필터** 드롭다운 목록에서 **모두** 를 선택 해야 합니다.

1. 경고를 구성 하는 방법을 자세히 지정 하려면 **경고 세부 정보** 를 정의 합니다.

1. **작업 그룹** 을 정의 하 여 경고를 받을 사람 (및 방법)을 결정 합니다.

## <a name="logs"></a>로그

### <a name="workspace-level-logs"></a>작업 영역 수준 로그

Azure Synapse Analytics 작업 영역에서 내보낸 로그는 다음과 같습니다.

| Log Analytics 테이블 이름 | 로그 범주 이름                 | 설명 |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | 게이트웨이 Apirequests             | Azure Synapse gateway API 요청. |
| SynapseRbacOperations         | SynapseRbacOperations          | Azure Synapse SRBAC (역할 기반 액세스 제어) 작업 |

### <a name="dedicated-sql-pool-logs"></a>전용 SQL 풀 로그

다음은 전용 SQL 풀에서 내보낸 로그입니다.

| Log Analytics 테이블 이름        | 로그 범주 이름             | 설명 |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Azure Synapse 전용 SQL 풀의 SQL 요청/쿼리에 대 한 정보입니다.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Azure Synapse 전용 SQL 풀에서 DMS 단계를 완료 하는 작업자에 대 한 정보입니다.
| SynapseSqlPoolRequestSteps  | RequestSteps | Azure Synapse 전용 SQL 풀에서 지정 된 SQL 요청/쿼리를 구성 하는 요청 단계에 대 한 정보입니다.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Azure Synapse 전용 SQL 풀에서 SQL 요청/쿼리 단계의 쿼리 배포에 대 한 정보입니다.
| SynapseSqlPoolWaits         | 대기        | 전송 큐의 잠금 및 대기를 비롯 하 여 Azure Synapse 전용 SQL 풀에서 SQL 요청/쿼리를 실행 하는 동안 발생 한 대기 상태에 대 한 정보입니다.

이러한 로그에 대 한 자세한 내용은 다음 정보를 참조 하세요.
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Apache Spark 풀 로그

Apache Spark 풀이 내보내는 로그는 다음과 같습니다.

| Log Analytics 테이블 이름               | 로그 범주 이름              | 설명                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | 종료 된 Apache Spark 응용 프로그램에 대 한 정보 |

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용 하 여 비 계산 리소스에 대 한 진단 로그를 구성 합니다. 리소스 컨트롤에 대 한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정 합니다. 예를 들면 Azure storage 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송 되는 로그 범주를 지정 합니다.
* 각 로그 범주가 저장소 계정에 유지 되는 기간을 지정 합니다.
* 보존 기간이 0일이면 로그가 영구적으로 유지됩니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 모든 일 수 있습니다.
* 보존 정책이 설정 되었지만 저장소 계정에 로그를 저장 하지 않은 경우 보존 정책은 적용 되지 않습니다. 예를 들어 Event Hubs 또는 모니터 로그 옵션만 선택한 경우이 문제가 발생할 수 있습니다.
* 보존 정책은 하루에 적용 됩니다. 일 사이의 경계는 UTC (협정 세계시) 자정에 발생 합니다. 하루 끝에 보존 정책을 초과 하는 일의 로그가 삭제 됩니다. 예를 들어 1 일의 보존 정책이 있는 경우 오늘부터 어제 까지의 로그를 삭제 합니다.

Azure Monitor 진단 설정을 사용 하 여 분석을 위해 진단 로그를 서로 다른 여러 대상으로 라우팅할 수 있습니다.

* **Storage 계정**: 감사 또는 수동 검사를 위해 저장소 계정에 진단 로그를 저장 합니다. 진단 설정을 사용 하 여 보존 기간 (일)을 지정할 수 있습니다.
* **이벤트 허브**: 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 Power BI와 같은 파트너 서비스/사용자 지정 분석 솔루션에 대 한 입력이 됩니다.
* **작업 영역 Log Analytics**: Log Analytics를 사용 하 여 로그를 분석 합니다. Log Analytics와의 Azure Synapse 통합은 다음 시나리오에서 유용 합니다.
  * Log Analytics 하기 위해 Azure Synapse에서 게시 한 풍부한 메트릭 집합에 복잡 한 쿼리를 작성 하려고 합니다. Azure Monitor를 통해 이러한 쿼리에 대 한 사용자 지정 경고를 만들 수 있습니다.
  * 작업 영역에서 모니터링 하려고 합니다. 여러 작업 영역에서 단일 Log Analytics 작업 영역으로 데이터를 라우팅할 수 있습니다.

로그를 내보내는 리소스의 구독에 없는 저장소 계정 또는 이벤트 허브 네임 스페이스를 사용할 수도 있습니다. 설정을 구성 하는 사용자에 게 두 구독에 대 한 적절 한 Azure RBAC (역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

#### <a name="configure-diagnostic-settings"></a>진단 설정 구성

작업 영역, 전용 SQL 풀 또는 Apache Spark 풀에 대 한 진단 설정을 만들거나 추가 합니다.

1. 포털에서 모니터로 이동 합니다. **설정**  >  **진단 설정** 을 선택 합니다.

1. 진단 설정을 만들려는 Synapse 작업 영역, 전용 SQL 풀 또는 Apache Spark 풀을 선택 합니다.

1. 선택한 작업 영역에 진단 설정이 없는 경우 설정을 만들라는 메시지가 표시 됩니다. **진단 켜기** 를 선택합니다.

   작업 영역에 기존 진단 설정이 있으면 리소스에 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 선택합니다.

1. 설정에 이름을 지정 하 고 **Log Analytics 보내기** 를 선택한 다음 **Log Analytics 작업 영역** 에서 작업 영역을 선택 합니다.

    > [!NOTE]
    > Azure 로그 테이블에는 500 개 이상의 열을 사용할 수 없기 때문에 _리소스 특정 모드_ 를 선택 하 **는 것이 좋습니다** . 자세한 내용은 [Log Analytics 알려진 제한 사항](../../azure-monitor/essentials/resource-logs.md#column-limit-in-azurediagnostics)을 참조 하세요.

1. **저장** 을 선택합니다.

몇 분 후에 작업 영역, 전용 SQL 풀 또는 Apache Spark 풀의 설정 목록에 새 설정이 표시 됩니다. 진단 로그는 새 이벤트 데이터가 생성 되는 즉시 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 때와 Log Analytics에 표시 될 때까지 최대 15 분이 경과 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링 하는 방법에 대 한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조 하세요. 

Apache Spark 응용 프로그램 모니터링에 대 한 자세한 내용은 [Synapse Studio에서 Apache Spark 응용 프로그램 모니터링](apache-spark-applications.md) 문서를 참조 하세요.

SQL 요청 모니터링에 대 한 자세한 내용은 [Synapse Studio에서 sql 요청 모니터링](how-to-monitor-sql-requests.md) 문서를 참조 하세요.
