---
title: 하이퍼스케일의 성능 진단
description: 이 문서에서는 Azure SQL Database에서 하이퍼스케일 성능 문제를 해결하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615058"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 하이퍼스케일 성능 문제 해결 진단

하이퍼스케일 데이터베이스의 성능 문제를 해결하기 위해 Azure SQL 데이터베이스 계산 노드의 [일반 성능 튜닝 방법은](sql-database-monitor-tune-overview.md) 성능 조사의 시작점입니다. 그러나 Hyperscale의 [분산 아키텍처를](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) 감안할 때 지원 하기 위해 추가 진단이 추가 되었습니다. 이 문서에서는 하이퍼스케일 별 진단 데이터에 대해 설명합니다.

## <a name="log-rate-throttling-waits"></a>로그 속도 제한 대기

모든 Azure SQL Database 서비스 수준에는 [로그 비율 거버넌스를](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)통해 적용된 로그 생성 속도 제한이 있습니다. 하이퍼스케일에서 로그 생성 제한은 현재 서비스 수준에 관계없이 초당 100MB로 설정되어 있습니다. 그러나 복구 가능성 SLA를 유지하기 위해 기본 계산 복제본의 로그 생성 속도를 제한해야 하는 경우가 있습니다. 이 제한은 페이지 [서버 또는 다른 계산 복제본이](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) Log 서비스에서 새 로그 레코드를 적용하는 데 상당히 뒤처진 경우에 발생합니다.

다음 대기 [유형(sys.dm_os_wait_stats)에서는](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)기본 계산 복제본에서 로그 속도를 제한할 수 있는 이유를 설명합니다.

|대기 유형    |설명                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 하이퍼스케일 데이터베이스의 기본 계산 노드 로그 생성 속도가 페이지 서버에서 지연된 로그 소비로 인해 제한되는 경우에 발생합니다.         |
|RBIO_RG_DESTAGE        | 하이퍼스케일 데이터베이스계산 노드 로그 생성 속도가 장기 로그 저장소에 의한 로그 소비 지연으로 인해 제한되는 경우에 발생합니다.         |
|RBIO_RG_REPLICA        | 하이퍼스케일 데이터베이스가 노드 로그 생성 속도를 계산할 때 읽을 수 있는 보조 복제본에 의한 로그 사용 지연으로 인해 발생합니다.         |
|RBIO_RG_LOCALDESTAGE   | 하이퍼스케일 데이터베이스계산 노드 로그 생성 속도가 로그 서비스에 의한 로그 소비 지연으로 인해 제한되는 경우에 발생합니다.         |

## <a name="page-server-reads"></a>페이지 서버읽기

계산 복제본은 데이터베이스의 전체 복사본을 로컬로 캐시하지 않습니다. 계산 복제본에 로컬인 데이터는 버퍼 풀(메모리) 및 데이터 페이지의 부분(비포함) 캐시인 로컬 복원력 버퍼 풀 확장(RBPEX) 캐시에 저장됩니다. 이 로컬 RBPEX 캐시는 계산 크기에 비례하여 크기가 조정되며 계산 계층의 메모리의 3배입니다. RBPEX는 가장 자주 액세스하는 데이터가 있다는 점에서 버퍼 풀과 유사합니다. 반면에 각 페이지 서버는 유지 관리하는 데이터베이스 부분에 대해 커버링되는 RBPEX 캐시를 가지고 있습니다.
 
계산 복제본에서 읽기가 실행되면 버퍼 풀 또는 로컬 RBPEX 캐시에 데이터가 없는 경우 getPage(pageId, LSN) 함수 호출이 실행되고 해당 페이지 서버에서 페이지가 인출됩니다. 페이지 서버에서 읽기는 원격 읽기이므로 로컬 RBPEX에서 읽는 것보다 느립니다. IO 관련 성능 문제를 해결할 때상대적으로 느린 원격 페이지 서버 읽기를 통해 수행된 IO 수를 알 수 있어야 합니다.

여러 DMV 및 확장 이벤트에는 페이지 서버의 원격 읽기 수를 지정하는 열과 필드가 있으며, 이 열은 총 읽기와 비교할 수 있습니다. 쿼리 저장소는 쿼리 런타임 통계의 일부로 원격 읽기도 캡처합니다.

- 페이지 서버 읽기를 보고하는 열은 실행 DMV 및 카탈로그 보기에서 사용할 수 있습니다.
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 페이지 서버 읽기는 다음 확장 이벤트에 추가됩니다.
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - 쿼리 store_execution_runtime_info
- 실제페이지서버읽기/실제페이지읽기판독어가 실제 계획에 대한 쿼리 계획 XML에 추가됩니다. 예를 들어:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 쿼리 계획 속성 창에서 이러한 특성을 보려면 SSMS 18.3 이상이 필요합니다.

## <a name="virtual-file-stats-and-io-accounting"></a>가상 파일 통계 및 IO 회계

Azure SQL 데이터베이스에서 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF는 SQL Server IO를 모니터링하는 기본 방법입니다. 하이퍼스케일의 IO 특성은 [분산 아키텍처로](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)인해 다릅니다. 이 섹션에서는 이 DMF에서 볼 수 있는 데이터 파일에 IO(읽기 및 쓰기)에 중점을 둡니다. 하이퍼스케일에서 이 DMF에 표시되는 각 데이터 파일은 원격 페이지 서버에 해당합니다. 여기에 언급된 RBPEX 캐시는 로컬 SSD 기반 캐시이며, 이는 계산 복제본의 비커버퍼 링 캐시입니다.

### <a name="local-rbpex-cache-usage"></a>로컬 RBPEX 캐시 사용량

로컬 RBPEX 캐시는 로컬 SSD 저장소의 계산 복제본에 있습니다. 따라서 이 캐시에 대한 IO는 원격 페이지 서버에 대한 IO보다 빠릅니다. 현재 하이퍼스케일 데이터베이스의 [sys.dm_io_virtual_file_stats()에는](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) 계산 복제본의 로컬 RBPEX 캐시에 대해 IO를 보고하는 특수 행이 있습니다. 이 행의 값은 모두 `database_id` `file_id` 열과 열에 대해 0입니다. 예를 들어 아래 쿼리는 데이터베이스 시작 이후 RBPEX 사용 통계를 반환합니다.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

RBPEX에서 수행된 읽기와 다른 모든 데이터 파일에서 수행된 집계된 읽기의 비율은 RBPEX 캐시 적중률을 제공합니다.

### <a name="data-reads"></a>데이터 읽기

- 계산 복제본에서 SQL Server 엔진에서 읽기를 발급하는 경우 로컬 RBPEX 캐시 또는 원격 페이지 서버또는 여러 페이지를 읽는 경우 둘의 조합으로 제공될 수 있습니다.
- 계산 복제본이 특정 파일(예: file_id 1)에서 일부 페이지를 읽는 경우 이 데이터가 로컬 RBPEX 캐시에만 있는 경우 이 읽기의 모든 IO는 file_id 0(RBPEX)에 대해 계산됩니다. 해당 데이터의 일부가 로컬 RBPEX 캐시에 있고 일부가 원격 페이지 서버에 있는 경우 IO는 RBPEX에서 제공되는 부품에 대해 file_id 0으로 계산되고 원격 페이지 서버에서 제공되는 부분은 file_id 1로 계산됩니다. 
- 계산 복제본이 페이지 서버에서 특정 [LSN의](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) 페이지를 요청하는 경우 페이지 서버가 요청한 LSN을 따라잡지 못하면 페이지 서버가 계산 복제본으로 반환되기 전에 페이지 서버가 검색될 때까지 계산 복제본의 읽기가 대기합니다. 계산 복제본의 페이지 서버에서 읽은 경우 해당 IO에서 대기 중일 경우 PAGEIOLATCH_* 대기 유형이 표시됩니다. 하이퍼스케일에서 이 대기 시간에는 페이지 서버에서 요청된 페이지를 필요한 LSN으로 catch하는 시간과 페이지 서버에서 계산 복제본으로 페이지를 전송하는 데 필요한 시간이 모두 포함됩니다.
- 미리 읽기와 같은 큰 읽기는 종종 ["분산 수집" 읽기를](/sql/relational-databases/reading-pages/)사용하여 수행됩니다. 이렇게 하면 SQL Server 엔진에서 단일 읽기로 간주되는 한 번에 최대 4MB의 페이지를 읽을 수 있습니다. 그러나 데이터를 RBPEX에 있는 경우 버퍼 풀과 RBPEX는 항상 8KB 페이지를 사용하기 때문에 이러한 읽기는 여러 개의 개별 8KB 읽기로 간주됩니다. 결과적으로 RBPEX에 대해 볼 수 있는 읽기 IOs 수가 엔진에서 수행하는 실제 IOs 수보다 클 수 있습니다.

### <a name="data-writes"></a>데이터 쓰기

- 기본 계산 복제본은 페이지 서버에 직접 쓰지 않습니다. 대신 로그 서비스의 로그 레코드가 해당 페이지 서버에서 재생됩니다. 
- 계산 복제본에서 발생하는 쓰기는 주로 로컬 RBPEX(file_id 0)에 기록됩니다. 8KB보다 큰 논리 파일에 대한 쓰기의 경우, 즉 [Gather-write를](/sql/relational-databases/writing-pages/)사용하여 수행한 논리 파일에 대한 쓰기의 경우 버퍼 풀과 RBPEX는 항상 8KB 페이지를 사용하기 때문에 각 쓰기 작업은 RBPEX에 여러 개의 KB 개별 쓰기로 변환됩니다. 결과적으로 RBPEX에 대해 볼 수 있는 쓰기 IOS 수가 엔진에서 수행하는 실제 IOs 수보다 클 수 있습니다.
- RBPEX가 아닌 파일 또는 페이지 서버에 해당하는 file_id 0 이외의 데이터 파일도 쓰기를 표시합니다. 하이퍼스케일 서비스 계층에서는 계산 복제본이 페이지 서버에 직접 쓰지 않으므로 이러한 쓰기가 시뮬레이션됩니다. 쓰기 IOPS 및 처리량은 계산 복제본에서 발생하지만 0이 file_id 이외의 데이터 파일에 대한 대기 시간은 페이지 서버 쓰기의 실제 대기 시간을 반영하지 않습니다.

### <a name="log-writes"></a>로그 쓰기

- 기본 계산에서 로그 쓰기는 sys.dm_io_virtual_file_stats file_id 2에 설명됩니다. 기본 계산에 대한 로그 쓰기는 로그 랜딩 존에 대한 쓰기입니다.
- 커밋의 보조 복제본에서 로그 레코드가 강화되지 않습니다. 하이퍼스케일에서는 로그 서비스가 로그 서비스에 의해 보조 복제본에 비동기적으로 적용됩니다. 로그 쓰기는 보조 복제본에서 실제로 발생하지 않으므로 보조 복제본의 로그 IOs 회계는 추적목적으로만 사용됩니다.

## <a name="data-io-in-resource-utilization-statistics"></a>리소스 활용 통계의 데이터 IO

하이퍼스케일이 아닌 데이터베이스에서는 [리소스 거버넌스](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) 데이터 IOPS 제한을 기준으로 데이터 파일에 대해 IOPS를 결합하고 쓰기가 `avg_data_io_percent` 열의 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 및 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 보기에 보고됩니다. 포털에서 _데이터 IO 백분율과_동일한 값이 보고됩니다. 

하이퍼스케일 데이터베이스에서 이 열은 컴퓨팅 복제본의 로컬 저장소 제한, 특히 RBPEX 및 `tempdb`에 대한 IO에 대한 데이터 IOPS 사용률을 기준으로 보고합니다. 이 열의 100% 값은 리소스 거버넌스가 로컬 저장소 IOPS를 제한하고 있음을 나타냅니다. 성능 문제와 상관 관계가 있는 경우 워크로드를 조정하여 IO를 적게 생성하거나 데이터베이스 서비스 목표를 증가하여 리소스 거버넌스 _Max Data IOPS_ [제한을](sql-database-vcore-resource-limits-single-databases.md)늘립니다. RBPEX 의 리소스 거버넌스를 위해 시스템은 SQL Server 엔진에서 발급할 수 있는 큰 IOs가 아닌 개별 8KB IOS를 계산합니다. 

원격 페이지 서버에 대한 데이터 IO는 리소스 사용률 보기 또는 포털에서 보고되지 않지만 앞에서 설명한 것처럼 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF에 보고됩니다.


## <a name="additional-resources"></a>추가 리소스

- 하이퍼스케일 단일 데이터베이스에 대한 vCore 리소스 제한의 경우 [하이퍼스케일 서비스 계층 vCore 제한을](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) 참조하십시오.
- Azure SQL Database 성능 튜닝의 경우 [Azure SQL 데이터베이스의 쿼리 성능을](sql-database-performance-guidance.md) 참조하세요.
- 쿼리 저장소를 사용한 성능 튜닝의 경우 [쿼리 저장소를 사용한 성능 모니터링을](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) 참조하십시오.
- DMV 모니터링 스크립트의 경우 [동적 관리 보기를 사용하여 성능 Azure SQL Database 모니터링을](sql-database-monitoring-with-dmvs.md) 참조하십시오.
