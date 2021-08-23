---
title: SQL 인사이트를 사용하여 SQL 배포 모니터링(미리 보기)
description: Azure Monitor의 SQL 인사이트 개요
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e69520f53cea9c6cd6eb662f4cc1acce2cab00f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567432"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL 인사이트를 사용하여 SQL 배포 모니터링(미리 보기)
SQL 인사이트는 [AZURE SQL 제품군](../../azure-sql/index.yml)의 모든 제품을 모니터링하는 포괄적인 솔루션입니다. SQL 인사이트는 [동적 관리 뷰](../../azure-sql/database/monitoring-with-dmvs.md)를 사용하여 상태를 모니터링하고 문제를 진단하고 성능을 조정하는 데 필요한 데이터를 공개합니다.  

SQL 인사이트는 원격으로 모든 모니터링을 수행합니다. 전용 가상 머신의 모니터링 에이전트는 SQL 리소스에 연결하고 데이터를 원격으로 수집합니다. 수집된 데이터는 [Azure Monitor 로그](../logs/data-platform-logs.md)에 저장되어 집계, 필터링 및 추세 분석을 쉽게 수행할 수 있도록 합니다. SQL 인사이트 [통합 문서 템플릿](../visualize/workbooks-overview.md)에서 수집된 데이터를 보거나 [로그 쿼리](../logs/get-started-queries.md)를 사용하여 데이터를 직접 확인할 수 있습니다.

## <a name="pricing"></a>가격 책정
SQL 인사이트에 대한 직접적인 비용은 없습니다. 모든 비용은 데이터를 수집하는 가상 머신, 데이터를 저장하는 Log Analytics 작업 영역 및 데이터에 구성된 모든 경고 규칙에 의해 발생합니다. 

**가상 머신**

가상 머신의 경우 [가상 머신 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 게시된 가격 책정에 따라 요금이 청구됩니다. 필요한 가상 머신 수는 모니터링하려는 연결 문자열의 수에 따라 달라집니다. 100개의 연결 문자열마다 크기가 Standard_B2 크기의 1개 가상 머신을 할당하는 것이 좋습니다. 자세한 내용은 [Azure Virtual Machines 요구 사항](sql-insights-enable.md#azure-virtual-machine-requirements)을 참조하세요.

**Log Analytics 작업 영역**

Log Analytics 작업 영역에 대해서는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라 요금이 청구됩니다. SQL 인사이트에서 사용하는 Log Analytics 작업 영역에는 데이터 수집, 데이터 보존 및 데이터 내보내기(선택 사항)에 대한 비용이 발생합니다. 정확한 요금은 수집, 보관 및 내보낸 데이터 크기에 따라 달라집니다. 이 데이터의 크기는 이후에 [모니터링 프로필](sql-insights-enable.md#create-sql-monitoring-profile)에 정의된 데이터베이스 작업 및 컬렉션 설정에 따라 달라집니다.

**경고 규칙**

Azure Monitor의 경고 규칙에서는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라 요금이 청구됩니다. [SQL 인사이트로 경고를 만들도록](sql-insights-alerts.md) 선택하면 생성된 모든 경고 규칙 및 전송된 알림에 대해 요금이 청구됩니다.

## <a name="supported-versions"></a>지원되는 버전 
SQL 인사이트는 다음 버전의 SQL Server를 지원합니다.
- SQL Server 2012 이상

SQL 인사이트는 다음 환경에서 실행되는 SQL Server를 지원합니다.
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Virtual Machines의 SQL Server([SQL 가상 머신](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 공급자에 등록된 가상 머신에서 실행되는 SQL Server)
- Azure VM([SQL 가상 머신](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 공급자에 등록되지 않은 가상 머신에서 실행되는 SQL Server)

SQL 인사이트는 다음에 대해 지원 또는 제한된 지원을 제공하지 않습니다.
- **Azure가 아닌 인스턴스**: Azure 외부의 가상 머신에서 실행되는 SQL Server는 지원되지 않습니다.
- **Azure SQL Database 탄력적 풀**: 탄력적 풀에 대한 메트릭을 수집할 수 없습니다. 탄력적 풀 내의 데이터베이스에 대해서는 메트릭을 수집할 수 없습니다.
- **Azure SQL Database 낮은 서비스 계층**: 기본, S0, S1 및 S2 [서비스 계층](../../azure-sql/database/resource-limits-dtu-single-databases.md)의 데이터베이스에 대해서는 메트릭을 수집할 수 없습니다.
- **Azure SQL Database 서버리스 계층**: 서버리스 컴퓨팅 계층을 사용하여 데이터베이스에 대한 메트릭을 수집할 수 있습니다. 그러나 메트릭을 수집하는 프로세스는 자동 일시 중지 지연 타이머를 다시 설정하여 데이터베이스가 자동 일시 중지 상태를 시작하지 않도록 합니다.
- **보조 복제본**: 데이터베이스당 보조 복제본 1개에 대해서만 메트릭을 수집할 수 있습니다. 데이터베이스에 두 개 이상의 보조 복제본이 있는 경우 1개만 모니터링할 수 있습니다.
- **Azure Active Directory를 사용한 인증**: 모니터링을 위해 지원되는 유일한 [인증](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) 방법은 SQL 인증입니다. Azure VM의 SQL Server에서는 사용자 지정 도메인 컨트롤러에서 Active Directory를 사용하는 인증은 지원되지 않습니다.  

## <a name="open-sql-insights"></a>SQL 인사이트 열기
Azure Portal **Azure Monitor** 메뉴의 **인사이트** 섹션에서 **SQL(미리 보기)** 을 선택하여 SQL 인사이트를 엽니다. 타일을 클릭하여 모니터링 중인 SQL의 유형에 대한 환경을 로드합니다.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure Portal의 SQL 인사이트":::

## <a name="enable-sql-insights"></a>SQL 인사이트 사용 
SQL 인사이트를 사용하도록 설정하기 위한 지침은 [SQL 인사이트 사용](sql-insights-enable.md)을 참조하세요.

## <a name="troubleshoot-sql-insights"></a>SQL 인사이트 문제 해결 
SQL 인사이트 문제 해결에 대한 지침은 [SQL 인사이트 정보 문제 해결](sql-insights-troubleshoot.md)을 참조하세요.

## <a name="data-collected-by-sql-insights"></a>SQL 인사이트에서 수집된 데이터
SQL 인사이트는 원격으로 모든 모니터링을 수행합니다. SQL Server를 실행하는 가상 머신에서는 에이전트를 설치하지 않습니다. 

SQL 인사이트는 전용 모니터링 가상 머신을 사용하여 SQL 리소스에서 데이터를 원격으로 수집합니다. 각 모니터링 가상 머신에는 [Azure Monitor 에이전트](../agents/azure-monitor-agent-overview.md) 및 WLI(워크로드 인사이트) 확장이 설치됩니다. WLI 확장에는 오픈 소스 [Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/)가 포함됩니다. SQL 인사이트는 [데이터 수집 규칙](../agents/data-collection-rule-overview.md)을 사용하여 Telegraf의 [SQL Server 플러그 인](https://www.influxdata.com/integration/microsoft-sql-server/)에 대한 데이터 수집 설정을 지정합니다.

Azure SQL Database, Azure SQL Managed Instance 및 SQL Server에 대해 여러 다른 데이터 세트를 사용할 수 있습니다. 아래 표에서는 사용 가능한 데이터에 대해 설명합니다. [모니터링 프로필을 만들 때](sql-insights-enable.md#create-sql-monitoring-profile) 수집할 데이터 세트와 수집 빈도를 사용자 지정할 수 있습니다.

아래 표에는 다음과 같은 열이 있습니다.
- **이름**: 모니터링 프로필을 만들 때 Azure Portal에 표시되는 쿼리의 이름입니다.
- **구성 이름**: 모니터링 프로필을 편집할 때 Azure Portal에 표시되는 쿼리의 이름입니다.
- **네임스페이스**: Log Analytics 작업 영역에 있는 쿼리의 이름입니다. 이 식별자는 **InsighstMetrics** 테이블의 `Tags` 열에 있는 `Namespace` 속성에 표시됩니다.
- **DMV**: 데이터 세트를 생성하는 데 사용되는 동적 관리 뷰입니다.
- **기본적으로 사용**: 기본적으로 데이터가 수집되는지 여부입니다.
- **기본 수집 빈도**: 기본적으로 데이터가 수집되는 빈도입니다.

### <a name="data-for-azure-sql-database"></a>Azure SQL Database용 데이터 
| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 수집 빈도 |
|:---|:---|:---|:---|:---|:---|
| DB 대기 통계 | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | 해당 없음 |
| DBO 대기 통계 | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60초 |
| 메모리 클럭 | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60초 |
| 데이터베이스 IO | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | Yes | 60초 |
| 서버 속성 | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | Yes | 60초 |
| 성능 카운터 | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60초 |
| 리소스 통계 | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60초 |
| 리소스 거버넌스 | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60초 |
| 요청 | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | 해당 없음 |
| 스케줄러| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | 해당 없음  |

### <a name="data-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance용 데이터 

| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 수집 빈도 |
|:---|:---|:---|:---|:---|:---|
| 대기 통계 | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60초 |
| 메모리 클럭 | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60초 |
| 데이터베이스 IO | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60초 |
| 서버 속성 | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60초 |
| 성능 카운터 | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60초 |
| 리소스 통계 | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60초 |
| 리소스 거버넌스 | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60초 |
| 요청 | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | 해당 없음 |
| 스케줄러 | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | 해당 없음 |

### <a name="data-for-sql-server"></a>SQL Server용 데이터

| 친숙한 이름 | 구성 이름 | 네임스페이스 | DMV | 기본적으로 사용 | 기본 수집 빈도 |
|:---|:---|:---|:---|:---|:---|
| 대기 통계 | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60초 | 
| 메모리 클럭 | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60초 |
| 데이터베이스 IO | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60초 |
| 서버 속성 | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60초 |
| 성능 카운터 | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60초 |
| 볼륨 공간 | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60초 |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60초 |
| 스케줄러 | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | 해당 없음 |
| 요청 | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | 해당 없음 |
| 가용성 복제본 상태 | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60초 |
| 가용성 데이터베이스 복제본 | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60초 |

## <a name="next-steps"></a>다음 단계

- SQL 인사이트를 사용하도록 설정하기 위한 지침은 [SQL 인사이트 사용](sql-insights-enable.md)을 참조하세요.
- SQL 인사이트 관련 질문과 대답에 대해서는 [질문과 대답](/azure/azure-monitor/faq#sql-insights-preview)을 참조하세요.