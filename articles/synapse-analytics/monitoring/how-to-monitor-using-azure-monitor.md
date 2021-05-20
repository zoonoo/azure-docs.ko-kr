---
title: Azure Monitor를 사용하여 Synapse Analytics를 모니터링하는 방법
description: Azure Monitor 메트릭, 경고 및 로그를 사용하여 Synapse Analytics 작업 영역을 모니터링하는 방법 알아보기
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ba730adaa1e3b111586012ba1849c6dc84d6db00
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553994"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics 작업 영역에서 Azure Monitor 사용

클라우드 애플리케이션은 복잡하며 이동하는 부분이 많습니다. 모니터는 애플리케이션을 유지하고 정상 상태로 실행하는 데 도움이 되는 데이터를 제공합니다. 또한 모니터는 잠재적인 문제를 방지하고 이전 문제를 해결하는 데도 도움이 됩니다. 모니터링 데이터를 사용하여 애플리케이션에 대해 깊이 이해할 수도 있습니다. 이 정보는 애플리케이션 성능 및 유지 관리를 개선하는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대한 기본 수준의 인프라 메트릭, 경고 및 로그를 제공합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. Azure Synapse Analytics는 Azure Monitor에서 진단 로그를 작성할 수 있습니다.

자세한 내용은 [Azure Monitor 개요](../../azure-monitor/overview.md)를 참조하세요.

## <a name="metrics"></a>메트릭

Monitor를 사용하면 Azure 워크로드의 성능과 상태에 대한 가시성을 확보할 수 있습니다. 가장 중요한 유형의 Monitor 데이터는 메트릭이며 성능 카운터라고도 합니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.

이러한 메트릭에 액세스하려면 [Azure Monitor 데이터 플랫폼](../../azure-monitor/data-platform.md)의 지침을 완료합니다.

### <a name="workspace-level-metrics"></a>작업 영역 수준 메트릭

작업 영역에서 내보내는 메트릭 몇 가지는 다음과 같습니다.

| **메트릭**                           | **메트릭 범주, 표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | 통합, 활동 실행 메트릭                     | 개수    | Sum(기본값), Count                | 1분 동안 발생하거나 종료된 활동 실행의 총 수입니다. </br></br> 이 메트릭의 결과 차원을 사용하여 성공, 실패 또는 취소됨 최종 상태로 필터링합니다.|
| IntegrationPipelineRunsEnded         | 통합, 파이프라인 실행 메트릭                     | 개수    | Sum(기본값), Count                | 1분 동안 발생하거나 종료된 파이프라인 실행의 총 수입니다. </br></br> 이 메트릭의 결과 차원을 사용하여 성공, 실패 또는 취소됨 최종 상태로 필터링합니다. |
| IntegrationTriggerRunsEnded          | 통합, 트리거 실행 메트릭                      | 개수    | Sum(기본값), Count                | 1분 동안 발생하거나 종료된 트리거 실행의 총 수입니다. </br></br> 이 메트릭의 결과 차원을 사용하여 성공, 실패 또는 취소됨 최종 상태로 필터링합니다. |
| BuiltinSqlPoolDataProcessedBytes     | 기본 제공 SQL 풀, 처리된 데이터(바이트) | Byte | Sum(기본값) | 기본 제공 서버리스 SQL 풀에서 처리한 데이터의 양입니다. |
| BuiltinSqlPoolLoginAttempts          | 기본 제공 SQL 풀, 로그인 시도 | 개수 | Sum(기본값) | 기본 제공 서버리스 SQL 풀의 로그인 시도 횟수입니다. |
| BuiltinSqlPoolDataRequestsEnded      | 기본 제공 SQL 풀, 요청이 종료됨(바이트) | 개수 | Sum(기본값) | 기본 제공 서버리스 SQL 풀에서 종료된 SQL 요청 수입니다. </br></br> 이 메트릭의 결과 차원을 사용하여 최종 상태로 필터링합니다. |

### <a name="dedicated-sql-pool-metrics"></a>전용 SQL 풀 메트릭

전용 SQL 풀에서 내보내는 메트릭 몇 가지는 다음과 같습니다.

| **메트릭**                           | **표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU 제한                       | 개수   | Max(기본값), Min, Avg | 구성된 SQL 풀 크기 |
| DWUUsed                             | DWU 사용됨                        | 개수   | Max(기본값), Min, Avg | SQL 풀의 대략적인 사용량을 나타냅니다. DWU 제한 * DWU 백분율로 측정합니다. |
| DWUUsedPercent                      | DWU 사용률             | 백분율 | Max(기본값), Min, Avg | SQL 풀의 대략적인 사용량을 나타냅니다. CPU 백분율과 데이터 IO 백분율 사이의 최댓값을 사용하여 측정됩니다. |
| ConnectionsBlockedByFirewall        | 방화벽에서 차단한 연결 | 개수   | Sum(기본값)   | 방화벽 규칙에서 차단한 연결 수입니다. 이 수가 높으면 SQL 풀의 액세스 제어 정책을 다시 방문하여 연결을 모니터링합니다. |
| AdaptiveCacheHitPercent             | 적응 캐시 적중률   | 백분율 | Max(기본값), Min, Avg | 워크로드가 적응 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 이 메트릭을 캐시 적중률 메트릭과 함께 사용하여 추가 용량을 스케일링할 것인지 아니면 워크로드를 다시 실행하여 캐시를 하이드레이션할 것인지 결정할 수 있습니다. |
| AdaptiveCacheUsedPercent            | 적응 캐시 사용률  | 백분율 | Max(기본값), Min, Avg | 워크로드가 적응 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 이 메트릭을 캐시 사용률 메트릭과 함께 사용하여 추가 용량을 스케일링할 것인지 아니면 워크로드를 다시 실행하여 캐시를 하이드레이션할 것인지 결정할 수 있습니다. |
| LocalTempDBUsedPercent              | 로컬 tempdb 사용률    | 백분율 | Max(기본값), Min, Avg | 모든 컴퓨팅 노드의 로컬 tempdb 사용률 - 5분마다 값을 내보냅니다. |
| MemoryUsedPercent                   | 메모리 사용률          | 백분율 | Max(기본값), Min, Avg | SQL 풀에 있는 모든 노드의 메모리 사용률 |
| CPUPercent                          | CPU 사용률             | 백분율 | Max(기본값), Min, Avg | SQL 풀에 있는 모든 노드의 CPU 사용률 |
| Connections                         | Connections                     | 개수   | Sum(기본값)  | SQL 풀에 대한 총 로그인 수 |
| ActiveQueries                      | 활성 쿼리                 | 개수   | Sum(기본값)   | 활성 쿼리입니다. 필터링되지 않고 분할되지 않은 이 메트릭을 사용하면 시스템에서 실행되는 모든 활성 쿼리가 표시됩니다. |
| QueuedQueries                      | 큐에 대기 중인 쿼리                  | 개수   | Sum(기본값)   | 최대 동시성 제한에 도달한 후에 큐에 대기 중인 누적 요청 수 |
| WLGActiveQueries                   | 작업 그룹 활성 쿼리   | 개수   | Sum(기본값)   | 워크로드 그룹 내의 활성 쿼리입니다. 필터링되지 않고 분할되지 않은 이 메트릭을 사용하면 시스템에서 실행되는 모든 활성 쿼리가 표시됩니다. |
| WLGActiveQueriesTimeouts           | 작업 그룹 쿼리 시간 제한   | 개수   | Sum(기본값)   | 시간이 초과된 작업 그룹에 대한 쿼리입니다. 이 메트릭에서 보고한 쿼리 시간 제한은 쿼리 실행이 시작된 후에만 해당됩니다(잠금 또는 리소스 대기로 인한 대기 시간은 포함되지 않음). |
| WLGQueuedQueries                   | 작업 그룹 큐에 대기 중인 쿼리   | 개수   | Sum(기본값)   | 최대 동시성 제한에 도달한 후에 큐에 대기 중인 누적 요청 수 |
| WLGAllocationBySystemPercent        | 시스템 비율별 작업 그룹 할당 | 백분율 | Max(기본값), Min, Avg, Sum | 전체 시스템에 상대적인 리소스 할당률 |
| WLGAllocationByEffectiveCapResourcePercent   | 최대 리소스 비율별 작업 그룹 할당 | 백분율 | Max(기본값), Min, Avg | 워크로드 그룹당 유효 상한 리소스 백분율에 상대적인 리소스 할당률을 표시합니다. 이 메트릭은 작업 그룹의 효과적인 사용률을 제공합니다. |
| WLGEffectiveCapResourcePercent      | 유효 상한 리소스 비율  | 백분율 | Max(기본값), Min, Avg | 워크로드 그룹에 대한 유효 상한 리소스 비율입니다. min_percentage_resource가 0보다 큰 다른 워크로드 그룹이 있으면 effective_cap_percentage_resource는 그에 비례해서 감소합니다. |
| WLGEffectiveMinResourcePercent      | 유효 최소 리소스 비율  | 백분율 | Max(기본값), Min, Avg, Sum | 서비스 수준 및 워크로드 그룹 설정을 고려하여 허용된 유효 최소 리소스 백분율입니다. 낮은 서비스 수준에서 유효 min_percentage_resource를 높일 수 있습니다. |

### <a name="apache-spark-pool-metrics"></a>Apache Spark 풀 메트릭

Apache Spark 풀에서 내보내는 메트릭 몇 가지는 다음과 같습니다.

| **메트릭**                           | **메트릭 범주, 표시 이름**                  | **단위** | **집계 형식** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | 종료된 Apache Spark 애플리케이션  | 개수 | Sum(기본값) | 종료된 Apache Spark 풀 애플리케이션의 수 |
| BigDataPoolAllocatedCores     | Apache Spark 풀에 할당된 vCore 수                 | 개수 | Max(기본값), Min, Avg | Apache Spark 풀에 할당된 vCore 수 |
| BigDataPoolAllocatedMemory    | Apache Spark 풀에 할당된 메모리의 양(GB)            | 개수 | Max(기본값), Min, Avg | Apach Spark 풀에 할당된 메모리(GB) |
| BigDataPoolApplicationsActive | 활성 Apache Spark 애플리케이션 | 개수 | Max(기본값), Min, Avg | 활성 Apache Spark 풀 애플리케이션의 수 |

## <a name="alerts"></a>경고

Azure Portal에 로그인하고 **모니터** > **경고** 를 선택하여 경고를 만듭니다.

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙** 을 선택하여 새 경고를 만듭니다.

1. 경고가 발생해야 하는 시점을 지정하는 **경고 조건** 을 정의합니다.

    > [!NOTE]
    > **리소스 종류별로 필터링** 드롭다운 목록에서 **모두** 를 선택해야 합니다.

1. 경고를 구성하는 방법을 자세히 지정하려면 **경고 세부 정보** 를 정의합니다.

1. **작업 그룹** 을 정의하여 경고를 보낼 사람(및 방법)을 결정합니다.

## <a name="logs"></a>로그

### <a name="workspace-level-logs"></a>작업 영역 수준 로그

Azure Synapse Analytics 작업 영역에서 내보내는 로그는 다음과 같습니다.

| Log Analytics 테이블 이름       | 로그 범주 이름              | Description          |
|--------------------------------|--------------------------------|----------------------|
| SynapseGatewayApiRequests      | GatewayApiRequests             | Azure Synapse 게이트웨이 API 요청입니다. |
| SynapseRbacOperations          | SynapseRbacOperations          | Azure Synapse SRBAC(역할 기반 액세스 제어) 작업입니다. |
| SynapseBuiltinSqlReqsEnded     | BuiltinSqlReqsEnded            | Azure Synapse 기본 제공 서버리스 SQL 풀에서 요청을 종료했습니다. |
| SynapseIntegrationPipelineRuns | IntegrationPipelineRuns        | Azure Synapse 통합 파이프라인 실행입니다. |
| SynapseIntegrationActivityRuns | IntegrationActivityRuns        | Azure Synapse 통합 활동 실행입니다. |
| SynapseIntegrationTriggerRuns  | IntegrationTriggerRuns         | Azure Synapse 통합 트리거 실행입니다. |

### <a name="dedicated-sql-pool-logs"></a>전용 SQL 풀 로그

전용 SQL 풀에서 내보내는 로그는 다음과 같습니다.

| Log Analytics 테이블 이름        | 로그 범주 이름             | Description |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Azure Synapse 전용 SQL 풀의 SQL 요청/쿼리에 대한 정보입니다.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Azure Synapse 전용 SQL 풀에서 DMS 단계를 완료하는 작업자에 대한 정보입니다.
| SynapseSqlPoolRequestSteps  | RequestSteps | Azure Synapse 전용 SQL 풀에서 특정 SQL 요청/쿼리를 구성하는 요청 단계에 대한 정보입니다.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Azure Synapse 전용 SQL 풀에서 SQL 요청/쿼리 단계의 쿼리 배포에 대한 정보입니다.
| SynapseSqlPoolWaits         | 대기        | Azure Synapse 전용 SQL 풀에서 전송 큐의 잠금 및 대기를 비롯한 SQL 요청/쿼리를 실행하는 동안 발생한 대기 상태에 대한 정보입니다.

이러한 로그에 대한 자세한 내용은 다음 정보를 참조하세요.
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Apache Spark 풀 로그

Apache Spark 풀이 내보내는 로그는 다음과 같습니다.

| Log Analytics 테이블 이름               | 로그 범주 이름              | Description                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | 종료된 Apache Spark 애플리케이션에 대한 정보 |

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용하여 비컴퓨팅 리소스에 대한 진단 로그를 구성합니다. 리소스 컨트롤에 대한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정합니다. 예를 들면 Azure Storage 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송되는 로그 범주를 지정합니다.
* 각 로그 범주를 스토리지 계정에 유지해야 하는 기간을 지정합니다.
* 보존 기간이 0일이면 로그가 영구적으로 유지됩니다. 그렇지 않은 경우 값은 1에서 2,147,483,647 사이의 일수일 수 있습니다.
* 보존 정책이 설정되었지만 스토리지 계정에 로그를 저장할 수 없는 경우 보존 정책은 적용되지 않습니다. 예를 들어 Event Hubs 또는 Monitor 로그 옵션만 선택한 경우 이 조건이 충족될 수 있습니다.
* 보존 정책은 일별로 적용됩니다. 하루는 UTC(협정 세계시) 자정을 기준으로 구분됩니다. 하루가 끝날 때 경과 일수가 보존 정책을 초과한 로그가 삭제됩니다. 예를 들어, 보존 정책이 하루인 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

Azure Monitor 진단 설정을 사용하여 분석용 진단 로그를 서로 다른 여러 대상으로 라우팅할 수 있습니다.

* **스토리지 계정**: 감사 또는 수동 검사를 위해 스토리지 계정에 진단 로그를 저장합니다. 진단 설정을 사용하여 보존 시간(일)을 지정할 수 있습니다.
* **이벤트 허브**: 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 Power BI와 같은 파트너 서비스/사용자 지정 분석 솔루션에 입력됩니다.
* **Log Analytics 작업 영역**: Log Analytics를 사용하여 로그를 분석합니다. Log Analytics와 Azure Synapse를 통합하면 다음 시나리오에서 유용합니다.
  * Azure Synapse에서 Log Analytics에 게시한 다양한 메트릭 집합에 대해 복잡한 쿼리를 작성하려고 합니다. Azure Monitor를 통해 이러한 쿼리에서 사용자 지정 경고를 만들 수 있습니다.
  * 작업 영역을 모니터링하려고 합니다. 여러 작업 영역에서 단일 Log Analytics 작업 영역으로 데이터를 라우팅할 수 있습니다.

또한 로그를 내보내는 리소스의 구독에 없는 스토리지 계정 또는 이벤트 허브 네임스페이스를 사용할 수도 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대한 적절한 Azure RBAC(Azure 역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

#### <a name="configure-diagnostic-settings"></a>진단 설정 구성

작업 영역, 전용 SQL 풀 또는 Apache Spark 풀에 대한 진단 설정을 만들거나 추가합니다.

1. 포털에서 모니터로 이동합니다. **설정** > **진단 설정** 을 선택합니다.

1. 진단 설정을 만들려는 Synapse 작업 영역, 전용 SQL 풀 또는 Apache Spark 풀을 선택합니다.

1. 선택한 작업 영역에 진단 설정이 없는 경우 설정을 만들라는 메시지가 표시됩니다. **진단 켜기** 를 선택합니다.

   작업 영역에 기존 진단 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. **진단 설정 추가** 를 선택합니다.

1. 설정에 이름을 지정하고 **Log Analytics에 보내기** 를 선택한 후 **Log Analytics 작업 영역** 에서 작업 영역을 선택합니다.

    > [!NOTE]
    > Azure 로그 테이블에는 500개가 넘는 열이 있을 수 없으므로 _리소스 관련 모드_ 를 선택할 것을 **적극 권장** 합니다. 자세한 내용은 [AzureDiagnostics 로그 참조](/azure/azure-monitor/reference/tables/azurediagnostics)를 확인하세요.

1. **저장** 을 선택합니다.

잠시 후 작업 영역, 전용 SQL 풀 또는 Apache Spark 풀의 설정 목록에 새 설정이 표시됩니다. 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역으로 스트림됩니다. 이벤트가 내보내지는 시점에서 Log Analytics에 표시되는 시점까지 최대 15분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

파이프라인 실행을 모니터링하는 방법에 대한 자세한 내용은 [Synapse Studio에서 파이프라인 실행 모니터링](how-to-monitor-pipeline-runs.md) 문서를 참조하세요. 

Apache Spark 애플리케이션 모니터링에 대한 자세한 내용은 [Synapse Studio에서 Apache Spark 애플리케이션 모니터링](apache-spark-applications.md) 문서를 참조하세요.

SQL 요청 모니터링에 대한 자세한 내용은 [Synapse Studio에서 SQL 요청 모니터링](how-to-monitor-sql-requests.md) 문서를 참조하세요.
