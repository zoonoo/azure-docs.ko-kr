---
title: SQL insights를 사용 하 여 SQL 배포 모니터링 (미리 보기)
description: Azure Monitor의 SQL 정보 개요
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b9c5db14bec87b30e51d39b1430ecc1f3cbef855
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798292"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL insights를 사용 하 여 SQL 배포 모니터링 (미리 보기)
SQL 정보 활용은 SQL 배포의 성능 및 상태를 모니터링 합니다.  성능 병목 상태 및 문제를 식별 하 여 SQL 백 엔드를 기반으로 작성 한 중요 한 워크 로드의 예측 가능한 성능 및 가용성을 제공 하는 데 도움이 됩니다. SQL insights는 데이터를 [Azure Monitor 로그](../logs/data-platform-logs.md)에 저장 합니다 .이 로그를 사용 하면 강력한 집계 및 필터링을 제공 하 고 시간에 따른 데이터 추세를 분석할 수 있습니다. 이 제품의 일부로 제공 되는 보기의 Azure Monitor에서이 데이터를 볼 수 있으며, 로그 데이터를 직접 살펴보고 쿼리를 실행 하 고 추세를 분석할 수 있습니다.

Sql 정보 활용은 SQL IaaS 배포에 어떤 항목도 설치 하지 않습니다. 대신, 전용 모니터링 가상 컴퓨터를 사용 하 여 SQL PaaS와 SQL IaaS 배포 모두에 대 한 데이터를 원격으로 수집 합니다.  SQL insights 모니터링 프로필을 사용 하면 azure SQL DB, azure SQL Managed Instance 및 Azure 가상 머신에서 실행 되는 SQL server를 포함 하 여 SQL 유형에 따라 수집 되는 데이터 집합을 관리할 수 있습니다.

## <a name="pricing"></a>가격 책정

SQL insights에 대 한 직접적인 비용은 없지만 Log Analytics 작업 영역에서 작업에 대 한 요금이 청구 됩니다. [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시 된 가격 책정에 따라 다음과 같은 SQL 정보를 청구 합니다.

- 데이터는 에이전트에서 수집 작업 영역에 저장 됩니다.
- 로그 데이터를 기반으로 하는 경고 규칙입니다.
- 경고 규칙에서 전송 되는 알림입니다.

로그 크기는 수집 된 데이터의 문자열 길이에 따라 달라 지 며 데이터베이스 작업의 양에 따라 증가할 수 있습니다. 

## <a name="supported-versions"></a>지원되는 버전 
SQL insights는 다음과 같은 SQL Server 버전을 지원 합니다.

- SQL Server 2012 이상

SQL server는 다음 환경에서 실행 되는 SQL Server을 지원 합니다.

- Azure SQL Database
- Azure SQL Managed Instance
- Azure SQL Vm
- Azure VM

SQL insights는 다음에 대 한 지원 또는 제한 된 지원을 제공 하지 않습니다.

- Azure 외부의 가상 머신에서 실행 되는 SQL Server는 현재 지원 되지 않습니다.
- Azure SQL Database 탄력적 풀: 공개 미리 보기 중에 제한적으로 지원 됩니다. 는 일반 공급으로 완벽 하 게 지원 됩니다.  
- Azure SQL 서버를 사용 하지 않는 배포: 활성 지역 DR과 마찬가지로 현재 모니터링 에이전트는 서버를 사용 하지 않는 배포가 절전 모드로 전환 되지 않도록 합니다. 이로 인해 서버를 사용 하지 않는 배포에서 예상 되는 비용이 높아질 수 있습니다.  
- 읽을 수 있는 보조: 현재는 읽을 수 있는 보조 끝점 (중요 비즈니스용 또는 Hyperscale)이 하나인 배포 유형만 지원 됩니다. 하이퍼 규모 배포에서 명명 된 복제본을 지 원하는 경우 단일 논리적 데이터베이스에 대해 여러 개의 읽을 수 있는 보조 끝점을 지원할 수 있습니다.  
- Azure Active Directory: 현재 모니터링 에이전트에 대 한 SQL 로그인만 지원 합니다. 향후 릴리스에서는 Azure Active Directory를 지원 하 고 맞춤식 도메인 컨트롤러의 Active directory를 사용 하는 SQL VM 인증을 현재 지원 하지 않습니다.  


## <a name="open-sql-insights"></a>SQL 정보 열기
Azure Portal **Azure Monitor** 메뉴의 **Insights** 섹션에서 **sql (미리 보기)** 을 선택 하 여 sql insights를 엽니다. 타일을 클릭 하 여 모니터링 중인 SQL의 유형에 대 한 환경을 로드 합니다.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure Portal의 SQL 정보":::


## <a name="enable-sql-insights"></a>SQL insights 사용 
문제 해결 단계 외에도 sql insights를 사용 하도록 설정 하는 자세한 절차는 [sql 정보 사용](sql-insights-enable.md) 을 참조 하세요.


## <a name="data-collected-by-sql-insights"></a>SQL insights에서 수집 된 데이터
공개 미리 보기에서 SQL 정보 활용은 원격 모니터링 메서드만 지원 합니다. [Telegraf 에이전트가](https://www.influxdata.com/time-series-platform/telegraf/) SQL Server에 설치 되어 있지 않습니다. [Telegraf에 대 한 SQL Server 입력 플러그 인](https://www.influxdata.com/integration/microsoft-sql-server/) 을 사용 하며, AZURE sql DB, azure sql Managed Instance, azure VM에서 실행 되는 sql Server 등의 다양 한 유형의 sql server에서 모니터링 하는 세 가지 쿼리 그룹을 사용 합니다. 

다음 표에는 다음이 요약 되어 있습니다.

- Sqlserver telegraf 플러그 인의 쿼리 이름
- 쿼리가 호출 하는 동적 관리 뷰
- *InsighstMetrics* 테이블의 아래에 데이터가 표시 되는 네임 스페이스
- 기본적으로 데이터 수집 여부
- 기본적으로 데이터를 수집 하는 빈도
 
모니터링 프로필을 만들 때 실행 되는 쿼리와 데이터 수집 빈도를 수정할 수 있습니다. 

### <a name="azure-sql-db-data"></a>Azure SQL DB 데이터 

| 쿼리 이름 | DMV | 네임스페이스 | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | 아니요 | 해당 없음 |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | 예 | 60초 |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | 예 | 60초 |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | 예 | 60초 |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>시스템. 데이터베이스인<br>시스템. [database_service_objectives] | sqlserver_server_properties | 예 | 60초 |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | 예 | 60초 |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | 예 | 60초 |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | 예 | 60초 |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | 아니요 | 해당 없음 |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | 아니요 | 해당 없음  |

### <a name="azure-sql-managed-instance-data"></a>Azure SQL 관리 되는 인스턴스 데이터 

| 쿼리 이름 | DMV | 네임스페이스 | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | 예 | 60초 |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | 예 | 60초 |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | 예 | 60초 |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | 예 | 60초 |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | 예 | 60초 |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | 예 | 60초 |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | 예 | 60초 |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | 아니요 | 해당 없음 |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | 아니요 | 해당 없음 |

### <a name="sql-server-data"></a>SQL Server 데이터

| 쿼리 이름 | DMV | 네임스페이스 | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | 예 | 60초 |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | 예 | 60초 | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | 예 | 60초 |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | 예 | 60초 |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | 예 | 60초 |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | 아니요 | 해당 없음 |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | 아니요 | 해당 없음 |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | 예 | 60초 |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | 예 | 60초 |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60초 |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60초 |




## <a name="next-steps"></a>다음 단계

SQL insights를 사용 하도록 설정 하는 방법에 대 한 자세한 절차는 [sql 정보 사용](sql-insights-enable.md) 을 참조 하세요.
SQL 정보에 대 한 자주 묻는 [질문은 질문과 대답을](../faq.md#sql-insights-preview) 참조 하세요.
