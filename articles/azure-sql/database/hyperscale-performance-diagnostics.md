---
title: 하이퍼스케일의 성능 진단
description: 이 문서에서는 Azure SQL Database에서 하이퍼스케일의 성능 문제를 해결하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 0f5edcb3a024336ce96adb015609c9faf3318b86
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107947108"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 하이퍼스케일 성능 문제 해결 진단
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

하이퍼스케일 데이터베이스의 성능 문제를 해결하려면 Azure SQL Database 컴퓨팅 노드의 [일반 성능 튜닝 방법론](monitor-tune-overview.md)이 성능 조사의 시작점입니다. 그러나 하이퍼스케일의 [분산 아키텍처](service-tier-hyperscale.md#distributed-functions-architecture)를 고려하여 지원을 위해 추가 진단이 추가되었습니다. 이 문서에서는 하이퍼스케일에 따른 진단 데이터를 설명합니다.

## <a name="log-rate-throttling-waits"></a>로그 비율 제한 대기

모든 Azure SQL Database 서비스 수준에는 [로그 속도 거버넌스](resource-limits-logical-server.md#transaction-log-rate-governance)를 통해 적용되는 로그 생성 속도 제한이 있습니다. 하이퍼스케일에서 로그 생성 제한은 현재 서비스 수준과 관계없이 100MB/초로 설정됩니다. 그러나 복구 SLA를 유지하기 위해 기본 컴퓨팅 복제본의 로그 생성 속도를 제한해야 하는 경우도 있습니다. 이 제한은 [페이지 서버 또는 다른 컴퓨팅 복제본](service-tier-hyperscale.md#distributed-functions-architecture)이 로그 서비스에서 새 로그 레코드를 적용하는 데 상당히 뒤처져 있을 때 발생합니다.

다음 대기 유형([sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)에서)은 기본 컴퓨팅 복제본에서 로그 속도가 제한될 수 있는 이유를 설명합니다.

|대기 유형    |Description                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 하이퍼스케일 데이터베이스 기본 컴퓨팅 노드 로그 생성 속도가 페이지 서버에서 지연된 로그 사용량으로 인해 제한될 때 발생합니다.         |
|RBIO_RG_DESTAGE        | 하이퍼스케일 데이터베이스 컴퓨팅 노드 로그 생성 속도가 장기 로그 스토리지의 지연된 로그 사용으로 인해 제한될 때 발생합니다.         |
|RBIO_RG_REPLICA        | 하이퍼스케일 데이터베이스 컴퓨팅 노드 로그 생성 속도가 읽기 가능한 보조 복제본의 지연된 로그 사용으로 인해 제한될 때 발생합니다.         |
|RBIO_RG_GEOREPLICA    | 하이퍼스케일 데이터베이스 컴퓨팅 노드 로그 생성 속도가 지역 보조 복제본의 지연된 로그 사용으로 인해 제한될 때 발생합니다.         |
|RBIO_RG_LOCALDESTAGE   | 하이퍼스케일 데이터베이스 컴퓨팅 노드 로그 생성 속도가 로그 서비스의 지연된 로그 사용으로 인해 제한될 때 발생합니다.         |


## <a name="page-server-reads"></a>페이지 서버 읽기

컴퓨팅 복제본은 데이터베이스의 전체 복사본을 로컬로 캐시하지 않습니다. 컴퓨팅 복제본의 로컬 데이터는 버퍼 풀(메모리 내) 및 데이터 페이지의 일부(비포함) 캐시인 로컬 RBPEX(복원 버퍼 풀 확장) 캐시에 저장됩니다. 이 로컬 RBPEX 캐시는 컴퓨팅 크기에 비례하여 크기가 조정되며 컴퓨팅 계층 메모리의 3배입니다. RBPEX는 가장 자주 액세스되는 데이터를 포함한다는 점에서 버퍼 풀과 비슷합니다. 반면 각 페이지 서버에는 유지 관리하는 데이터베이스의 일부에 대한 RBPEX 캐시가 포함됩니다.

컴퓨팅 복제본에서 읽기가 실행되는 경우 버퍼 풀 또는 로컬 RBPEX 캐시에 데이터가 없으면 getPage(pageId, LSN) 함수 호출이 실행되며 해당 페이지 서버에서 페이지가 페치됩니다. 페이지 서버에서 읽기는 원격 읽기이므로 로컬 RBPEX에서 읽기보다 느립니다. IO 관련 성능 문제를 해결할 때 상대적으로 느린 원격 페이지 서버 읽기를 통해 얼마나 많은 IO가 수행되었는지 알 수 있어야 합니다.

여러 DMV(동적 관리 뷰) 및 확장 이벤트에는 페이지 서버에서 원격 읽기 수를 지정하는 열과 필드가 있으며 이는 총 읽기와 비교할 수 있습니다. 쿼리 저장소는 또한 쿼리 실행 시간 통계의 일부로 원격 읽기를 캡처합니다.

- 보고서 페이지 서버 읽기의 열은 다음과 같은 실행 DMV 및 카탈로그 뷰에서 사용할 수 있습니다.

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 페이지 서버 읽기는 다음과 같은 확장 이벤트에 추가됩니다.
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads는 실제 계획에 대한 쿼리 계획 XML에 추가됩니다. 예를 들어:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 쿼리 계획 속성 창에서 이러한 특성을 보려면 SSMS 18.3 이상이 필요합니다.

## <a name="virtual-file-stats-and-io-accounting"></a>가상 파일 통계 및 IO 계산

Azure SQL Database에서 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF는 SQL Database IO를 모니터링하는 기본 방법입니다. 하이퍼스케일의 IO 특성은 [분산 아키텍처](service-tier-hyperscale.md#distributed-functions-architecture)로 인해 다릅니다. 이 섹션에서는 이 DMF에 표시된 것처럼 데이터 파일에 대한 IO(읽기 및 쓰기)에 중점을 둡니다. 하이퍼스케일에서 이 DMF에 표시되는 각 데이터 파일은 원격 페이지 서버에 해당합니다. 여기에 언급된 RBPEX 캐시는 로컬 SSD 기반 캐시이며 컴퓨팅 복제본의 비포함 캐시입니다.

### <a name="local-rbpex-cache-usage"></a>로컬 RBPEX 캐시 사용량

로컬 RBPEX 캐시는 로컬 SSD 스토리지의 컴퓨팅 복제본에 있습니다. 따라서 이 캐시에 대한 IO는 원격 페이지 서버에 대한 IO보다 빠릅니다. 현재 하이퍼스케일 데이터베이스의 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)에는 컴퓨팅 복제본의 로컬 RBPEX 캐시에 대한 IO를 보고하는 특수 행이 있습니다. 이 행의 값은 `database_id` 및 `file_id` 열 모두에 대해 0입니다. 예를 들어 다음 쿼리는 데이터베이스 시작 이후 RBPEX 사용 통계를 반환합니다.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

다른 모든 데이터 파일에서 집계된 읽기에 대한 RBPEX에서 수행되는 읽기의 비율은 RBPEX cache hit ratio를 제공합니다. 카운터 `RBPEX cache hit ratio`는 성능 카운터 DMV `sys.dm_os_performance_counters`에도 노출됩니다.                                                                        

### <a name="data-reads"></a>데이터 읽기

- 컴퓨팅 복제본의 SQL Server 데이터베이스 엔진에서 읽기를 실행하는 경우 로컬 RBPEX 캐시 또는 원격 페이지 서버에서 제공되거나 여러 페이지를 읽는 경우 둘의 조합에 의해 제공될 수 있습니다.
- 컴퓨팅 복제본이 특정 파일(예: file_id 1)에서 일부 페이지를 읽을 때 이 데이터가 로컬 RBPEX 캐시에만 있는 경우 이 읽기의 모든 IO는 file_id 0(RBPEX)에 대해 고려됩니다. 해당 데이터의 일부가 로컬 RBPEX 캐시에 있고 일부는 원격 페이지 서버에 있는 경우 RBPEX에서 제공하는 부분에 대한 IO는 file_id 0으로, 원격 페이지 서버에서 제공되는 부분은 file_id 1로 고려됩니다.
- 컴퓨팅 복제본이 페이지 서버에서 특정 [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/)의 페이지를 요청할 때 페이지 서버가 요청된 LSN을 따라잡지 못한 경우 컴퓨팅 복제본에 대한 읽기는 페이지가 컴퓨팅 복제본으로 반환되기 전에 페이지 서버가 따라잡을 때까지 대기합니다. 컴퓨팅 복제본의 페이지 서버에서 모든 읽기의 경우 해당 IO에서 대기하고 있다면 PAGEIOLATCH_* 대기 유형이 표시됩니다. 하이퍼스케일에서 이 대기 시간은 페이지 서버에서 요청된 페이지를 필요한 LSN에 맞추는 시간과 페이지를 페이지 서버에서 컴퓨팅 복제본으로 전송하는 데 필요한 시간을 모두 포함합니다.
- 미리 읽기와 같은 대규모 읽기는 종종 ["Scatter-Gather" 읽기](/sql/relational-databases/reading-pages/)를 사용하여 수행됩니다. 이를 통해 한 번에 최대 4MB의 페이지를 읽을 수 있으며 SQL Server 데이터베이스 엔진에서는 단일 읽기로 간주됩니다. 그러나 읽는 데이터가 RBPEX에 있는 경우 버퍼 풀과 RBPEX는 항상 8KB 페이지를 사용하므로 이러한 읽기는 여러 개별 8KB 읽기로 고려됩니다. 결과적으로 RBPEX에 대해 확인된 IO 수가 엔진에서 수행하는 실제 IO 수보다 클 수 있습니다.

### <a name="data-writes"></a>데이터 쓰기

- 기본 컴퓨팅 복제본은 페이지 서버에 직접 쓰지 않습니다. 대신 로그 서비스의 로그 레코드가 해당 페이지 서버에서 재생됩니다.
- 컴퓨팅 복제본에서 발생하는 쓰기는 주로 로컬 RBPEX(file_id 0)에 기록됩니다. 8KB보다 큰 논리 파일, 즉 [Gather-write](/sql/relational-databases/writing-pages/)를 사용하여 수행된 쓰기의 경우 버퍼 풀과 RBPEX가 항상 8KB 페이지를 사용하기 때문에 각 쓰기 작업은 RBPEX에 대한 여러 개별 8KB 쓰기로 변환됩니다. 결과적으로 RBPEX에 대해 표시되는 쓰기 IO 수가 엔진에서 수행하는 실제 IO 수보다 클 수 있습니다.
- RBPEX 파일이 아니거나 페이지 서버에 해당하는 file_id 0이 아닌 데이터 파일 또한 쓰기를 표시합니다. 하이퍼스케일 서비스 계층에서는 컴퓨팅 복제본이 페이지 서버에 직접 쓰지 않으므로 이러한 쓰기가 시뮬레이션됩니다. 쓰기 IOPS 및 처리량은 컴퓨팅 복제본에서 발생하는 것처럼 보이지만 file_id 0이 아닌 데이터 파일에 대한 대기 시간은 페이지 서버 쓰기의 실제 대기 시간을 반영하지 않습니다.

### <a name="log-writes"></a>로그 쓰기

- 기본 컴퓨팅에서는 sys.dm_io_virtual_file_stats의 file_id 2에서 로그 쓰기가 고려됩니다. 기본 컴퓨팅에 대한 로그 쓰기는 로그 랜딩 존에 대한 쓰기입니다.
- 커밋 시 보조 복제본에서 로그 레코드가 확정되지 않습니다. 하이퍼스케일에서 로그는 로그 서비스를 통해 보조 복제본에 비동기식으로 적용됩니다. 로그 쓰기는 실제로 보조 복제본에서 발생하지 않기 때문에 보조 복제본에서 로그 IO의 모든 계산은 추적 목적으로만 사용됩니다.

## <a name="data-io-in-resource-utilization-statistics"></a>리소스 사용률 통계의 데이터 IO

하이퍼스케일이 아닌 데이터베이스에서 [리소스 거버넌스](./resource-limits-logical-server.md#resource-governance) 데이터 IOPS 제한과 관련된 데이터 파일에 대한 읽기 및 쓰기 IOPS가 결합되어 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 뷰, `avg_data_io_percent` 열에 보고됩니다. 동일한 값이 Azure Portal에서 _데이터 IO 백분율_ 로 보고됩니다.

하이퍼스케일 데이터베이스에서 이 열은 컴퓨팅 복제본의 로컬 스토리지 제한, 특히 RBPEX 및 `tempdb`에 대한 IO와 관련된 데이터 IOPS 사용률을 보고합니다. 이 열에서 100% 값은 리소스 관리에서 로컬 스토리지 IOPS를 제한하고 있음을 나타냅니다. 이것이 성능 문제와 관련이 있는 경우 더 적은 IO를 생성하도록 워크로드를 튜닝하거나 리소스 거버넌스 _최대 데이터 IOPS_ [한도](resource-limits-vcore-single-databases.md)를 늘리기 위해 데이터베이스 서비스 목표를 늘립니다. RBPEX 읽기 및 쓰기에 대한 리소스 관리를 위해 시스템은 SQL Server 데이터베이스 엔진에서 실행할 수 있는 큰 IO가 아닌 개별 8KB IO를 계산합니다.

원격 페이지 서버에 대한 데이터 IO는 리소스 사용률 보기 또는 포털에서 보고되지 않지만 앞에서 언급한 것처럼 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF에서 보고됩니다.

## <a name="additional-resources"></a>추가 자료

- 하이퍼스케일 단일 데이터베이스에 대한 vCore 리소스 제한은 [하이퍼스케일 서비스 계층 vCore 제한](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)을 참조하세요.
- Azure SQL Database를 모니터링하려면 [Azure Monitor SQL 인사이트](../../azure-monitor/insights/sql-insights-overview.md)를 사용하도록 설정합니다.
- Azure SQL Database 성능 튜닝은 [Azure SQL Database의 쿼리 성능](performance-guidance.md)을 참조하세요.
- 쿼리 저장소를 사용한 성능 튜닝은 [쿼리 저장소를 사용한 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)을 참조하세요.
- DMV 모니터링 스크립트는 [동적 관리 보기를 사용하여 Azure SQL Database 성능 모니터링](monitoring-with-dmvs.md)을 참조하세요.