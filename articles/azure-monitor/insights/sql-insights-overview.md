---
title: SQL insights를 사용 하 여 SQL 배포 모니터링 (미리 보기)
description: Azure Monitor의 SQL 정보 개요
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567834"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL insights를 사용 하 여 SQL 배포 모니터링 (미리 보기)
SQL insights는 [AZURE sql 제품군](../../azure-sql/index.yml)의 모든 제품을 모니터링 하는 포괄적인 솔루션입니다. SQL 정보 활용은 [동적 관리 뷰](../../azure-sql/database/monitoring-with-dmvs.md) 를 사용 하 여 상태를 모니터링 하 고 문제를 진단 하 고 성능을 조정 하는 데 필요한 데이터를 노출 합니다.  

SQL 정보 활용은 원격으로 모든 모니터링을 수행 합니다. 전용 가상 컴퓨터의 모니터링 에이전트는 SQL 리소스에 연결 하 고 데이터를 원격으로 수집 합니다. 수집 된 데이터는 [Azure Monitor 로그](../logs/data-platform-logs.md)에 저장 되어 쉽게 집계, 필터링 및 추세 분석을 가능 하 게 합니다. SQL insights [통합 문서 템플릿에서](../visualize/workbooks-overview.md)수집 된 데이터를 보거나 [로그 쿼리](../logs/get-started-queries.md)를 사용 하 여 데이터를 직접 확인할 수 있습니다.

## <a name="pricing"></a>가격 책정
SQL insights에 대 한 직접적인 비용은 없습니다. 모든 비용은 데이터를 수집 하는 가상 컴퓨터, 데이터를 저장 하는 Log Analytics 작업 영역 및 데이터에 구성 된 모든 경고 규칙에 의해 발생 합니다. 

**가상 머신**

Virtual machines의 경우 [가상 머신 가격 책정 페이지](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/)에 게시 된 가격 책정에 따라 요금이 청구 됩니다. 필요한 가상 컴퓨터 수는 모니터링 하려는 연결 문자열의 수에 따라 달라 집니다. 100 연결 문자열 마다 크기가 Standard_B2s 1 개의 가상 컴퓨터를 할당 하는 것이 좋습니다. 자세한 내용은 [Azure 가상 컴퓨터 요구 사항](sql-insights-enable.md#azure-virtual-machine-requirements) 을 참조 하세요.

**Log Analytics 작업 영역**

Log Analytics 작업 영역에 대해서는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시 된 가격 책정에 따라 요금이 청구 됩니다. SQL insights에서 사용 하는 Log Analytics 작업 영역에는 데이터 수집, 데이터 보존 및 데이터 내보내기 (선택 사항)에 대 한 비용이 발생 합니다. 정확한 요금은 데이터 수집, 보관 및 내보내기의 양에 따라 달라 집니다. 이 데이터의 양은 이후에 [모니터링 프로필](sql-insights-enable.md#create-sql-monitoring-profile)에 정의 된 데이터베이스 작업 및 컬렉션 설정에 따라 달라 집니다.

**경고 규칙**

Azure Monitor의 경고 규칙의 경우 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시 된 가격 책정에 따라 요금이 청구 됩니다. [SQL 정보를 사용 하 여 경고를 만들도록](sql-insights-alerts.md)선택 하면 생성 된 모든 경고 규칙 및 전송 된 알림에 대해 요금이 청구 됩니다.

## <a name="supported-versions"></a>지원되는 버전 
SQL insights는 다음과 같은 SQL Server 버전을 지원 합니다.
- SQL Server 2012 이상

SQL server는 다음 환경에서 실행 되는 SQL Server을 지원 합니다.
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Virtual Machines SQL Server ( [SQL 가상 컴퓨터](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 공급자에 등록 된 가상 컴퓨터에서 실행 되는 SQL Server)
- Azure Vm ( [SQL 가상 컴퓨터](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 공급자에 등록 되지 않은 가상 컴퓨터에서 실행 중인 SQL Server)

SQL insights는 다음에 대 한 지원 또는 제한 된 지원을 제공 하지 않습니다.
- Azure가 **아닌 인스턴스**: azure 외부의 가상 머신에서 실행 되는 SQL Server는 지원 되지 않습니다.
- **탄력적 풀 Azure SQL Database**: 탄력적 풀에 대 한 메트릭을 수집할 수 없습니다. 탄력적 풀 내의 데이터베이스에 대해서는 메트릭을 수집할 수 없습니다.
- **Azure SQL Database 낮은 서비스 계층**: Basic, S0, S1 및 S2 [서비스 계층](../../azure-sql/database/resource-limits-dtu-single-databases.md) 의 데이터베이스에 대해서는 메트릭을 수집할 수 없습니다.
- **서버** 를 사용 하지 않는 계층: 서버 리스 계산 계층을 사용 하 여 데이터베이스에 대 한 메트릭을 수집할 수 있습니다. Azure SQL Database 그러나 메트릭을 수집 하는 프로세스는 자동 일시 중지 지연 타이머를 다시 설정 하 여 데이터베이스가 자동 일시 중지 된 상태를 입력 하지 않도록 합니다.
- **보조 복제본**: 데이터베이스 당 단일 보조 복제본에 대해서만 메트릭을 수집할 수 있습니다. 데이터베이스에 두 개 이상의 보조 복제본이 있는 경우 1 개만 모니터링할 수 있습니다.
- **Azure Active Directory 인증**: 모니터링에 대해 지원 되는 유일한 [인증](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) 방법은 SQL 인증입니다. Azure VM에 대 한 SQL Server 사용자 지정 도메인 컨트롤러에서 Active Directory를 사용 하는 인증은 지원 되지 않습니다.  

## <a name="open-sql-insights"></a>SQL 정보 열기
Azure Portal **Azure Monitor** 메뉴의 **Insights** 섹션에서 **sql (미리 보기)** 을 선택 하 여 sql insights를 엽니다. 타일을 클릭 하 여 모니터링 중인 SQL의 유형에 대 한 환경을 로드 합니다.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure Portal의 SQL 정보":::

## <a name="enable-sql-insights"></a>SQL insights 사용 
SQL insights를 사용 하도록 설정 하는 방법에 대 한 지침은 [sql 정보 사용](sql-insights-enable.md) 을 참조 하세요.

## <a name="troubleshoot-sql-insights"></a>SQL 정보 문제 해결 
SQL insights 문제 해결에 대 한 지침은 [sql 정보 문제 해결](sql-insights-troubleshoot.md) 을 참조 하세요.

## <a name="data-collected-by-sql-insights"></a>SQL insights에서 수집 된 데이터
SQL 정보 활용은 원격으로 모든 모니터링을 수행 합니다. SQL Server를 실행 하는 가상 컴퓨터에는 에이전트를 설치 하지 않습니다. 

SQL insights는 전용 모니터링 가상 컴퓨터를 사용 하 여 SQL 리소스에서 데이터를 원격으로 수집 합니다. 각 모니터링 가상 컴퓨터에는 [Azure Monitor 에이전트](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) 와 Wli (워크 로드 정보) 확장이 설치 됩니다. WLI 확장에는 오픈 소스 [Telegraf 에이전트가](https://www.influxdata.com/time-series-platform/telegraf/)포함 됩니다. SQL insights는 [데이터 수집 규칙](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) 을 사용 하 여 Telegraf의 [SQL Server 플러그 인](https://www.influxdata.com/integration/microsoft-sql-server/)에 대 한 데이터 수집 설정을 지정 합니다.

Azure SQL Database, Azure SQL Managed Instance 및 SQL Server에 대해 서로 다른 데이터 집합을 사용할 수 있습니다. 아래 표에서는 사용 가능한 데이터에 대해 설명 합니다. [모니터링 프로필을 만들](sql-insights-enable.md#create-sql-monitoring-profile)때 수집할 데이터 집합과 수집 빈도를 사용자 지정할 수 있습니다.

아래 표에는 다음과 같은 열이 있습니다.
- **이름**: 모니터링 프로필을 만들 때 Azure Portal에 표시 되는 쿼리 이름
- **구성 이름**: 모니터링 프로필을 편집할 때 Azure Portal에 표시 되는 쿼리 이름
- **네임 스페이스**: Log Analytics 작업 영역에 있는 쿼리 이름입니다. 이 식별자는  `Namespace` 열에 있는 속성의 InsighstMetrics 테이블에 표시 됩니다. `Tags`
- **Dmv**: 데이터 집합을 생성 하는 데 사용 되는 동적 관리 뷰
- **기본적으로 사용**: 기본적으로 데이터가 수집 되는지 여부
- **기본 컬렉션 빈도**: 기본적으로 데이터가 수집 되는 빈도

### <a name="data-for-azure-sql-database"></a>Azure SQL Database에 대 한 데이터 
| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|:---|
| DB 대기 통계 | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | 예 | 해당 없음 |
| DBO 대기 통계 | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | 예 | 60초 |
| 메모리 클럭 | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 예 | 60초 |
| 데이터베이스 IO | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | 예 | 60초 |
| 서버 속성 | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>시스템. 데이터베이스인<br>시스템. [database_service_objectives] | 예 | 60초 |
| 성능 카운터 | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | 예 | 60초 |
| 리소스 통계 | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | 예 | 60초 |
| 리소스 거버넌스 | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | 예 | 60초 |
| 요청 | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 예 | 해당 없음 |
| 스케줄러| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 예 | 해당 없음  |

### <a name="data-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 데이터 

| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|:---|
| 대기 통계 | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | 예 | 60초 |
| 메모리 클럭 | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 예 | 60초 |
| 데이터베이스 IO | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | 예 | 60초 |
| 서버 속성 | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | 예 | 60초 |
| 성능 카운터 | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| 예 | 60초 |
| 리소스 통계 | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | 예 | 60초 |
| 리소스 거버넌스 | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | 예 | 60초 |
| 요청 | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 예 | 해당 없음 |
| 스케줄러 | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 예 | 해당 없음 |

### <a name="data-for-sql-server"></a>SQL Server에 대 한 데이터

| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 컬렉션 빈도 |
|:---|:---|:---|:---|:---|:---|
| 대기 통계 | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | 예 | 60초 | 
| 메모리 클럭 | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | 예 | 60초 |
| 데이터베이스 IO | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | 예 | 60초 |
| 서버 속성 | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | 예 | 60초 |
| 성능 카운터 | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | 예 | 60초 |
| 볼륨 공간 | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | 예 | 60초 |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | 예 | 60초 |
| 스케줄러 | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | 예 | 해당 없음 |
| 요청 | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | 예 | 해당 없음 |
| 가용성 복제본 상태 | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | 예 | 60초 |
| 가용성 데이터베이스 복제본 | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | 예 | 60초 |

## <a name="next-steps"></a>다음 단계

- SQL 정보 사용에 대 한 지침은 [sql Insights 사용](sql-insights-enable.md) 을 참조 하세요.
- SQL [정보에 대](../faq.md#sql-insights-preview) 한 자주 묻는 질문과 대답을 참조 하세요.
