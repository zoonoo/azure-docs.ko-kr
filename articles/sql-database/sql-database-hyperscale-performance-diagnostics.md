---
title: Azure SQL Database Hyperscale 성능 문제 해결 진단 | Microsoft Docs
description: 이 문서에서는 SQL Database에서 하이퍼 규모의 성능 문제를 해결 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/09/2019
ms.openlocfilehash: 8c632866f942e27c4340dc83b7ef302dd4b21314
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392827"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL Hyperscale 성능 문제 해결 진단


하이퍼 확장 데이터베이스의 성능 문제를 해결 하기 위해 Azure SQL database 계산 노드의 [일반적인 성능 조정 방법론](sql-database-monitor-tune-overview.md) 은 성능 조사를 시작 하는 지점입니다. 그러나 하이퍼 규모의 [분산 아키텍처](sql-database-service-tier-hyperscale.md) 를 고려 하 여를 지원 하기 위해 추가 진단이 추가 되었습니다. 이 문서에서는 Hyperscale 특정 진단 데이터를 설명 합니다.


## <a name="log-rate-throttling-waits"></a>로그 비율 제한 대기


모든 Azure SQL Database 서비스 수준에는 [로그 전송률 거 버 넌 스](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)를 통해 적용 되는 로그 생성 률 제한이 있습니다. Hyperscale에서 로그 생성 제한은 현재 서비스 계층에 관계 없이 100 m b/초로 설정 됩니다. 그러나 복구 Sla를 유지 하기 위해 기본 계산 복제본의 로그 생성 속도를 제한 해야 하는 경우도 있습니다. 이 제한은 [페이지 서버 또는 다른 계산 복제본](sql-database-service-tier-hyperscale.md) 이 로그 서비스에서 새 로그 레코드를 적용 하는 것 보다 훨씬 더 많은 경우에 발생 합니다.

다음 대기 유형 ( [_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/))은 기본 계산 복제본에서 로그 전송률이 제한 될 수 있는 이유를 설명 합니다.

|대기 유형    |설명                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 페이지 서버에서 로그 사용량이 지연 되어 하이퍼 크기 조정 데이터베이스 기본 계산 노드 로그 생성 비율이 제한 될 때 발생 합니다.         |
|RBIO_RG_DESTAGE        | 장기 로그 저장소에의 한 로그 사용 지연으로 인해 Hyperscale 데이터베이스 계산 노드 로그 생성 비율이 제한 될 때 발생 합니다.         |
|RBIO_RG_REPLICA        | 읽을 수 있는 보조 복제본 노드에의 한 로그 사용 지연으로 인해 Hyperscale 데이터베이스 계산 노드 로그 생성 비율이 제한 될 때 발생 합니다.         |
|RBIO_RG_LOCALDESTAGE   | 로그 서비스에의 한 로그 사용 지연으로 인해 Hyperscale 데이터베이스 계산 노드 로그 생성 비율이 제한 될 때 발생 합니다.         |


## <a name="page-server-reads"></a>페이지 서버 읽기

계산 복제본은 데이터베이스의 전체 복사본을 로컬로 캐시 하지 않습니다. 계산 복제본의 로컬 데이터는 버퍼 풀 (메모리 내) 및 데이터 페이지의 일부 (비 다루기) 인 로컬 RBPEX (복원 버퍼 풀 확장) 캐시에 저장 됩니다. 이 로컬 RBPEX 캐시는 계산 크기에 비례하여 크기가 조정 되며 계산 계층의 메모리의 3 배입니다. RBPEX는 가장 자주 액세스 되는 데이터를 포함 한다는 점에서 버퍼 풀과 비슷합니다. 반면 각 페이지 서버에는 유지 관리 하는 데이터베이스의 일부에 대 한 RBPEX 캐시가 포함 됩니다.
 
계산 복제본에서 읽기가 실행 되 면 버퍼 풀이나 로컬 RBPEX 캐시에 데이터가 없으면 getPage (pageId, LSN) 함수 호출이 실행 되 고 해당 페이지 서버에서 페이지가 인출 됩니다. 페이지 서버에서 읽기는 원격 읽기 이므로 로컬 RBPEX 읽기 보다 느립니다. IO 관련 성능 문제를 해결할 때 비교적 느린 원격 페이지 서버 읽기를 통해 수행 된 Io 수를 확인할 수 있어야 합니다.

페이지 서버 읽기를 Dmv 및 확장 이벤트 집합에 추가 하 여 페이지 서버 v/s의 논리적 읽기에서 원격 읽기 작업을 식별 하는 데 도움이 됩니다.

- 보고서 페이지 서버 읽기 열은 다음과 같은 실행 Dmv에서 사용할 수 있습니다.
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- 페이지 서버 읽기는 다음과 같은 확장 이벤트에 추가 됩니다.
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - 쿼리-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads는 실제 계획에 대 한 쿼리 계획 XML에 추가 됩니다.

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> SSMS의 쿼리 계획 속성 창에서 이러한 특성을 보려면 SSMS 18.3 이상이 필요 합니다.


## <a name="virtual-file-stats-and-io-accounting"></a>가상 파일 통계 및 IO 계정

Azure SQL Database에서 [_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF는 SQL Server io를 모니터링 하는 기본 방법입니다. 하이퍼 규모의 IO 특성은 [분산 아키텍처로](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)인해 다릅니다. 이 섹션에서는이 DMF에 표시 된 것 처럼 데이터 파일에 대 한 IO (읽기 및 쓰기)에 중점을 둡니다. Hyperscale에서이 DMF에 표시 되는 각 데이터 파일은 원격 페이지 서버에 해당 합니다. 여기에 언급 된 RBPEX cache는 계산 노드의 비 포괄 캐시 인 로컬 SSD 기반 캐시입니다.


### <a name="local-rbpex-cache-usage"></a>로컬 RBPEX cache 사용

로컬 RBPEX 캐시가 로컬 SSD 저장소의 계산 노드에 있습니다. 따라서이 RBPEX cache의 IO는 원격 페이지 서버의 IO 보다 빠릅니다. 현재는 Hyperscale 데이터베이스의 [_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) 에 계산 복제본의 로컬 RBPEX 캐시에서 수행 되는 io를 보고 하는 특별 한 행이 있습니다. @No__t-0 및 `file_id` 열 모두에 대해이 행의 값은 0입니다. 예를 들어 다음 쿼리는 데이터베이스 시작 이후 RBPEX 사용 통계를 반환 합니다.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

다른 모든 데이터 파일에서 집계 된 읽기에 대 한 RBPEX에서 수행 되는 읽기의 비율은 RBPEX cache hit ratio를 제공 합니다.


### <a name="data-reads"></a>데이터 읽기

- 계산 복제본의 SQL Server 엔진에서 읽기를 실행 하는 경우 로컬 RBPEX 캐시 또는 원격 페이지 서버에서 또는 여러 페이지를 읽는 경우 둘의 조합에 의해 서비스가 제공 될 수 있습니다.
- 계산 복제본이 특정 파일 (예: file_id 1)에서 일부 페이지를 읽을 때이 데이터가 로컬 RBPEX 캐시에만 있는 경우이 읽기의 모든 IO는 file_id 0 (RBPEX)에 대해 고려 됩니다. 해당 데이터의 일부가 로컬 RBPEX 캐시에 있고 일부 일부가 원격 페이지 서버에 있는 경우 IO는 RBPEX에서 제공 되는 파트에 대해 file_id 0으로 계산 되며 원격 페이지 서버에서 제공 되는 파트는 file_id 1로 계산 됩니다. 
- 계산 복제본이 페이지 서버에서 특정 [lsn](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) 의 페이지를 요청 하는 경우 페이지 서버가 요청 된 lsn을 catch 하지 않으면 계산 복제본의 읽기는 페이지 서버가 계산 복제본에 반환 되기 전에 페이지 서버가 처리할 때까지 대기 합니다. 계산 복제본의 페이지 서버에서 읽을 경우 해당 IO를 대기 하는 PAGEIOLATCH_XX 대기 유형이 표시 됩니다. 이 대기 시간에는 페이지 서버의 요청 된 페이지를 필요한 LSN으로 파악 하는 시간과 페이지를 페이지 서버에서 계산 복제본으로 전송 하는 데 필요한 시간이 모두 포함 됩니다.
- 미리 읽기와 같은 대량 읽기는 종종 ["산 수집" 읽기](/sql/relational-databases/reading-pages/)를 사용 하 여 수행 됩니다. 이를 통해 한 번에 최대 4mb의 페이지를 읽을 수 있으며 SQL Server 엔진에서 단일 읽기로 간주 됩니다. 그러나 읽을 데이터가 RBPEX에 있는 경우 버퍼 풀 및 RBPEX 항상 8kb 페이지를 사용 하기 때문에 이러한 읽기는 여러 개별 8kb 읽기로 사용 됩니다. 결과적으로 RBPEX에 대해 확인 된 Io 수가 엔진에서 수행 하는 실제 Io 수 보다 클 수 있습니다.


### <a name="data-writes"></a>데이터 쓰기

- 기본 계산 복제본은 페이지 서버에 직접 쓰지 않습니다. 대신 로그 서비스의 로그 레코드가 해당 페이지 서버에서 재생 됩니다. 
- 계산 복제본에서 발생 하는 쓰기는 주로 로컬 RBPEX (file_id 0)에 기록 됩니다. 8kb 보다 큰 논리적 파일 (예: [수집-쓰기](/sql/relational-databases/writing-pages/)를 사용 하 여 쓰기)의 경우, 버퍼 풀 및 RBPEX 항상 8kb 페이지를 사용 하므로 각 쓰기 작업은 RBPEX에 대 한 8kb의 개별 쓰기로 변환 됩니다. 결과적으로 RBPEX에 대해 표시 되는 쓰기 Io 수가 엔진에서 수행 하는 실제 Io 수 보다 클 수 있습니다.
- RBPEX 않은 파일 또는 페이지 서버에 해당 하는 file_id 0 이외의 데이터 파일은 쓰기도 표시 합니다. Hyperscale 서비스 계층에서는 계산 복제본이 페이지 서버에 직접 쓰지 않으므로 이러한 쓰기가 시뮬레이션 됩니다. 쓰기 IOPS 및 처리량은 계산 복제본에서 발생 하는 것 처럼 보이지만 file_id 0 이외의 데이터 파일에 대 한 대기 시간은 페이지 서버 쓰기의 실제 대기 시간을 반영 하지 않습니다.

### <a name="log-writes"></a>로그 쓰기

- 기본 계산에서는 _io_virtual_file_stats의 file_id 2에 대 한 로그 쓰기가 고려 됩니다. 기본 계산에 대 한 로그 쓰기는 원격 Azure premium storage 인 로그 방문 영역에 대 한 쓰기입니다.
- 보조 복제본에서 로그 레코드는 커밋 시 보조 복제본에서 확정 되지 않습니다. log는 Xlog 서비스에 의해 원격 복제본에 적용 됩니다. 지정 된 로그 쓰기는 실제로 보조 복제본에서 발생 하지 않으며 추적 목적 으로만 사용 됩니다.

## <a name="additional-resources"></a>추가 리소스

- 하이퍼 규모의 단일 데이터베이스에 대 한 vCore 리소스 제한의 경우 하이퍼 [크기 조정 서비스 계층 vCore 제한](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute) 을 참조 하세요.
- Azure SQL Database 성능 튜닝에 대 한 자세한 내용은 [의 쿼리 성능](sql-database-performance-guidance.md) 을 참조 하십시오 Azure SQL Database
- 쿼리 저장소 사용 하는 성능 조정에 대해서는 [쿼리 저장소를 사용 하 여 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) 을 참조 하세요.
- DMV 모니터링 스크립트는 [동적 관리 뷰를 사용 하 Azure SQL Database 성능 모니터링](sql-database-monitoring-with-dmvs.md) 을 참조 하세요.