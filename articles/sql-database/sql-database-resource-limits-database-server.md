---
title: Azure SQL 데이터베이스 리소스 제한 | 마이크로 소프트 문서
description: 이 문서에서는 단일 데이터베이스 및 탄력적 풀에 대한 Azure SQL Database 리소스 제한에 대한 개요를 제공합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067258"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL 데이터베이스 리소스 제한 및 리소스 거버넌스

이 문서에서는 단일 데이터베이스 및 탄력적 풀을 관리하는 SQL Database 서버의 SQL Database 리소스 한도를 간략하게 설명합니다. 이러한 리소스 제한이 적중되거나 초과될 때 발생하는 일에 대한 정보를 제공하고 이러한 제한을 적용하는 데 사용되는 리소스 거버넌스 메커니즘에 대해 설명합니다.

> [!NOTE]
> Managed Instance 한도는 [관리되는 인스턴스에 대한 SQL Database 리소스 한도](sql-database-managed-instance-resource-limits.md)를 참조하세요.

## <a name="maximum-resource-limits"></a>최대 리소스 한도

| 리소스 | 제한 |
| :--- | :--- |
| 서버당 데이터베이스 | 5,000 |
| 모든 지역에서 구독당 서버의 기본 수 | 20 |
| 모든 지역에서 구독당 서버의 최대 수 | 200 |  
| 서버당 DTU/eDTU 할당량 | 54,000 |  
| 서버/인스턴스당 vCore 할당량 | 540 |
| 서버당 최대 풀 | DTU 또는 vCore의 수로 제한됩니다. 예를 들어 각 풀에 DTU가 1000개인 경우 서버는 54개 풀을 지원할 수 있습니다.|
|||

> [!IMPORTANT]
> 데이터베이스 수가 SQL Database 서버당 한도에 근접하면 다음이 발생할 수 있습니다.
>
> - 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> - 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

> [!NOTE]
> 기본 금액보다 더 많은 DTU/eDTU 할당량, vCore 할당량 또는 더 많은 서버를 얻으려면 Azure 포털에서 새 지원 요청을 제출합니다. 자세한 내용은 [Azure SQL 데이터베이스에 대한 할당량 증가를](quota-increase-request.md)요청합니다.

### <a name="storage-size"></a>스토리지 크기

단일 데이터베이스 리소스 저장소 크기의 경우 [DTU 기반 리소스 제한](sql-database-dtu-resource-limits-single-databases.md) 또는 가격 책정 계층별 저장소 크기 제한에 대한 [vCore 기반 리소스 제한을](sql-database-vcore-resource-limits-single-databases.md) 참조하십시오.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>데이터베이스 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute(DTU 및 eDTU / vCore)

데이터베이스 계산 사용률(DTUs 및 eDT 또는 vCore로 측정됨)이 높아지면 쿼리 대기 시간이 증가하고 쿼리 시간이 균등해질 수 있습니다. 이러한 조건에서 쿼리는 서비스에서 큐에 대기할 수 있으며 리소스가 자유로워지면 실행을 위한 리소스가 제공됩니다.
높은 컴퓨팅 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기를 늘려 데이터베이스에 더 많은 컴퓨팅 리소스를 제공합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>스토리지

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)가 표시됩니다. select 및 DELETE 문은 계속 성공합니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 저장소를 더 추가합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 속하는 경우 데이터베이스를 풀 외부로 이동하여 스토리지 공간이 다른 데이터베이스와 공유되지 않도록 할 수 있습니다.
- 데이터베이스를 축소하여 사용하지 않는 공간을 회수합니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청)

최대 세션 및 작업자 수는 서비스 계층 및 계산 크기(DTUS/eDT 또는 vCore)에 따라 결정됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 애플리케이션에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 이는 데이터베이스 리소스 제한에 도달하고 더 긴 실행 쿼리, 대규모 차단 체인 또는 과도한 쿼리 병렬 처리로 인해 작업자가 쌓이는 피크 로드 기간 동안 특히 그렇습니다.

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 컴퓨팅 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.
- [MAXDOP(최대](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) 평행도) 설정 감소.
- 쿼리 워크로드를 최적화하여 발생 횟수와 쿼리 차단 기간을 줄입니다.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>사용자 워크로드 및 내부 프로세스별 리소스 소비

각 데이터베이스의 사용자 워크로드별 CPU 및 메모리 사용량은 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 및 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) 보기, 내 `avg_cpu_percent` 및 `avg_memory_usage_percent` 열에 보고됩니다. 탄력적 풀의 경우 풀 수준 리소스 소비는 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에 보고됩니다. 사용자 워크로드 CPU 사용량은 `cpu_percent` 풀 수준에서 [단일 데이터베이스](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) 및 탄력적 풀에 대한 Azure Monitor 메트릭을 통해서도 [보고됩니다.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)

Azure SQL Database에서는 고가용성 및 재해 복구, 데이터베이스 백업 및 복원, 모니터링, 쿼리 저장소, 자동 튜닝 등과 같은 핵심 서비스 기능을 구현하는 데 계산 리소스가 필요합니다. 시스템은 [리소스 거버넌스](#resource-governance) 메커니즘을 사용하여 이러한 내부 프로세스에 대한 전체 리소스의 특정 제한된 부분을 따로 설정하여 나머지 리소스를 사용자 워크로드에 사용할 수 있도록 합니다. 내부 프로세스가 계산 리소스를 사용하지 않는 경우 시스템은 사용자 워크로드에서 사용할 수 있도록 합니다.

단일 데이터베이스 또는 탄력적 풀을 호스팅하는 SQL Server 인스턴스의 사용자 워크로드 및 내부 프로세스별 총 CPU 및 메모리 사용량은 `avg_instance_cpu_percent` `avg_instance_memory_percent` [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 및 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) 보기, 내 및 열에 보고됩니다. 이 데이터는 풀 수준에서 `sqlserver_process_core_percent` `sqlserver_process_memory_percent` [단일 데이터베이스](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) 및 탄력적 풀에 대한 및 Azure Monitor 메트릭을 통해서도 [보고됩니다.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)

사용자 워크로드 및 내부 프로세스별 최근 리소스 사용량에 대한 자세한 분석은 [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 및 [sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) 보기에 보고됩니다. 이러한 보기에서 참조되는 리소스 풀 및 워크로드 그룹에 대한 자세한 내용은 [리소스 거버넌스](#resource-governance)를 참조하십시오. 이러한 보기는 사용자 워크로드의 리소스 사용률과 관련 리소스 풀 및 워크로드 그룹의 특정 내부 프로세스에 대해 보고합니다.

성능 모니터링 및 문제 해결의 맥락에서 **사용자 CPU 사용량(,** `avg_cpu_percent` `cpu_percent`사용자 워크로드 및 내부 프로세스)별로`avg_instance_cpu_percent``sqlserver_process_core_percent` **총 CPU 사용량을** 모두 고려하는 것이 중요합니다.

**사용자 CPU 사용량은** 각 서비스 목표에서 사용자 워크로드 제한의 백분율로 계산됩니다. **사용자 CPU 사용률이** 100%로 사용자 워크로드가 서비스 목표의 한계에 도달했음을 나타냅니다. 그러나 **총 CPU 사용량이** 70-100% 범위에 도달하면 보고된 사용자 CPU **사용량이** 100% 미만으로 유지되더라도 사용자 워크로드 처리량이 평평해지고 쿼리 대기 시간이 증가하는 것을 볼 수 있습니다. 이는 컴퓨팅 리소스의 적당한 할당을 사용하여 더 작은 서비스 목표를 사용할 때 발생할 가능성이 [높지만, 조밀한 탄력적 풀과](sql-database-elastic-pool-resource-management.md)같이 상대적으로 강렬한 사용자 워크로드입니다. 이 문제는 내부 프로세스에 일시적으로 추가 리소스가 필요한 경우(예: 데이터베이스의 새 복제본을 만들 때) 더 작은 서비스 목표에서도 발생할 수 있습니다.

**총 CPU 사용량이** 많으면 완화 옵션은 앞서 언급한 것과 동일하며 서비스 목표 증가 및/또는 사용자 워크로드 최적화를 포함합니다.

## <a name="resource-governance"></a>리소스 거버넌스

리소스 제한을 적용하기 위해 Azure SQL Database는 Azure에서 SQL Server 데이터베이스 서비스를 실행하도록 수정및 확장된 SQL Server [리소스 거점을](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)기반으로 하는 리소스 거버넌스 구현을 사용합니다. 서비스의 각 SQL Server 인스턴스에는 여러 [리소스 풀](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) 및 워크로드 [그룹이](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)있으며, 리소스 제한은 풀 및 그룹 수준에서 모두 설정되어 [분산된 서비스형 데이터베이스를](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)제공합니다. 사용자 워크로드 및 내부 워크로드는 별도의 리소스 풀 및 워크로드 그룹으로 분류됩니다. 지리적 복제본을 포함하여 기본 및 읽을 수 있는 보조 복제본의 사용자 워크로드는 `SloSharedPool1` `N` 데이터베이스 ID 값을 나타내는 리소스 풀 및 `UserPrimaryGroup.DBId[N]` 워크로드 그룹으로 분류됩니다. 또한 다양한 내부 워크로드에 대해 여러 리소스 풀 및 워크로드 그룹이 있습니다.

Azure SQL Database는 리소스 거지(Resource Governor)를 사용하여 SQL Server 프로세스 내의 리소스를 관리하는 것 외에도 프로세스 수준 리소스 거버넌스에 Windows [작업 개체를](https://docs.microsoft.com/windows/win32/procthread/job-objects) 사용하고 저장소 할당량 관리를 위해 [Windows 파일 서버 리소스 관리자(FSRM)를](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 사용합니다.

Azure SQL Database 리소스 거버넌스는 본질적으로 계층적입니다. 위에서 아래로, 제한은 운영 체제 리소스 거버넌스 메커니즘 및 리소스 거지, 리소스 거지 번을 사용 하 여 다음 리소스 풀 수준에서 사용 하 여 OS 수준 및 저장소 볼륨 수준에서 적용 됩니다. 리소스 관리자. 현재 데이터베이스 또는 탄력적 풀에 대한 리소스 거버넌스 제한은 [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 보기에 표시됩니다. 

### <a name="data-io-governance"></a>데이터 IO 거버넌스

데이터 IO 거버넌스는 Azure SQL Database에서 데이터베이스의 데이터 파일에 대해 실제 IO 읽기 및 쓰기를 모두 제한하는 데 사용되는 프로세스입니다. IOPS 제한은 "시끄러운 이웃" 효과를 최소화하고, 다중 테넌트 서비스에서 리소스 할당 공정성을 제공하고, 기본 하드웨어 및 저장소의 기능 내에서 유지하도록 각 서비스 수준에 대해 설정됩니다.

단일 데이터베이스의 경우 워크로드 그룹 제한은 데이터베이스에 대한 모든 저장소 IO에 적용되며 리소스 풀 제한은 `tempdb` 데이터베이스를 포함한 동일한 SQL Server 인스턴스의 모든 데이터베이스에 대해 모든 저장소 IO에 적용됩니다. 탄력적 풀의 경우 워크로드 그룹 제한은 풀의 각 데이터베이스에 적용되지만 리소스 풀 `tempdb` 제한은 풀의 모든 데이터베이스 간에 공유되는 데이터베이스를 포함하여 전체 탄력적 풀에 적용됩니다. 일반적으로 워크로드 그룹 제한이 리소스 풀 제한보다 낮고 IOPS/처리량을 더 빨리 제한하기 때문에 데이터베이스(단일 또는 풀화)에 대한 워크로드에서 리소스 풀 제한을 달성할 수 없습니다. 그러나 동일한 SQL Server 인스턴스의 여러 데이터베이스에 대해 결합된 워크로드에 의해 풀 제한에 도달할 수 있습니다.

예를 들어 쿼리가 IO 리소스 거버넌스 없이 1000 IOPS를 생성하지만 워크로드 그룹 최대 IOPS 제한이 900 IOPS로 설정된 경우 쿼리는 900개 이상의 IOPS를 생성할 수 없습니다. 그러나 리소스 풀 최대 IOPS 제한이 1500 IOPS로 설정되고 리소스 풀과 연결된 모든 워크로드 그룹의 총 IO가 1500 IOPS를 초과하는 경우 동일한 쿼리의 IO가 작업 그룹 제한 인 900 IOPS 미만으로 감소될 수 있습니다.

[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 보기에 의해 반환되는 IOPS 및 처리량 최소/최대 값은 보장이 아닌 제한/상한으로 작동합니다. 또한 리소스 거버넌스는 특정 저장소 대기 시간을 보장하지 않습니다. 지정된 사용자 워크로드에 대한 최상의 대기 시간, IOPS 및 처리량은 IO 리소스 거버넌스 제한뿐만 아니라 사용되는 IO 크기의 혼합 및 기본 저장소의 기능에 따라 달라집니다. SQL Server는 512KB에서 4MB 사이의 크기가 다른 OS를 사용합니다. IOPS 제한을 적용하기 위해 Azure Storage에 데이터 파일이 있는 데이터베이스를 제외한 모든 IO는 크기에 관계없이 설명됩니다. 이 경우 256KB보다 큰 IO는 Azure 저장소 IO 회계에 맞추기 위해 다중 256KB IO로 계산됩니다.

Azure Storage에서 데이터 파일을 사용하는 기본 데이터베이스, 표준 및 `primary_group_max_io` 범용 데이터베이스의 경우 데이터베이스에 이 IOPS 수를 누적적으로 제공할 수 있는 데이터 파일이 충분하지 않거나 데이터가 파일 간에 균등하게 분산되지 않거나 기본 Blob의 성능 계층이 리소스 거버넌스 제한 보다 낮은 IOPS/처리량을 제한하는 경우 값을 달성할 수 없습니다. 마찬가지로 빈번한 트랜잭션 커밋에 의해 생성된 `primary_max_log_rate` 작은 로그 IO의 경우 기본 Azure 저장소 Blob의 IOPS 제한으로 인해 워크로드에서 값을 달성하지 못할 수 있습니다.

[sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)및 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에 보고된 `avg_data_io_percent` 리소스 `avg_log_write_percent`사용률 값은 최대 리소스 거버넌스 제한의 백분율로 계산됩니다. 따라서 리소스 거버넌스 이외의 요인이 IOPS/처리량을 제한하는 경우 보고된 리소스 사용률이 100% 미만으로 유지되더라도 워크로드가 증가함에 따라 IOPS/처리량이 평평해지고 대기 가 늘어남을 수 있습니다. 

데이터베이스 파일당 IOPS, 처리량 및 대기 시간을 읽고 작성하려면 [sys.dm_io_virtual_file_stats() 함수를](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 사용합니다. 이 함수는 기본 `avg_data_io_percent`저장소의 IOPS 및 처리량을 사용하는 백그라운드 IO를 포함하여 데이터베이스에 대한 모든 IO를 표시하며 관찰된 저장소 대기 시간에 영향을 미칠 수 있습니다. 또한 이 함수는 읽기 및 쓰기에 대한 IO 리소스 거버넌스에 `io_stall_queued_read_ms` 의해 `io_stall_queued_write_ms` 도입될 수 있는 추가 대기 시간을 각각 및 열에 표시합니다.

### <a name="transaction-log-rate-governance"></a>트랜잭션 로그 비율 거버넌스

트랜잭션 로그 속도 거버넌스는 대량 삽입, SELECT INTO 및 인덱스 빌드와 같은 워크로드에 대한 높은 수집 속도를 제한하는 데 사용되는 Azure SQL Database의 프로세스입니다. 이러한 제한은 하위 수준에서 로그 레코드 생성 속도에 따라 추적 및 적용되며, 데이터 파일에 대해 발급될 수 있는 IOs 수에 관계없이 처리량을 제한합니다.  트랜잭션 로그 생성 속도는 현재 하드웨어에 종속된 지점까지 선형으로 확장되며 vCore 구매 모델에서는 최대 로그 속도가 96MB/s가 허용됩니다. 

> [!NOTE]
> 트랜잭션 로그 파일에 대한 실제 OS는 관리되거나 제한되지 않습니다.

로그 속도는 다양한 시나리오에서 달성되고 유지될 수 있도록 설정되어 있으며 전체 시스템은 사용자 로드에 미치는 영향을 최소화하면서 기능을 유지할 수 있습니다. 로그 비율 거버넌스를 통해 트랜잭션 로그 백업이 게시된 복구 가능성 SLA 내에 유지됩니다.  또한 이 거버넌스는 보조 복제본에 대한 과도한 백로그를 방지합니다.

로그 레코드가 생성될 때 원하는 최대 로그 속도(MB/s/s/s/s)를 유지하기 위해 각 작업을 지연해야 하는지 여부를 평가하고 평가합니다. 로그 레코드가 저장소로 플러시될 때 지연이 추가되지 않고 로그 비율 생성 자체 중에 로그 비율 거버넌스가 적용됩니다.

런타임에 부과되는 실제 로그 생성 속도는 피드백 메커니즘의 영향을 받을 수 있으므로 시스템이 안정화될 수 있도록 허용 가능한 로그 속도를 일시적으로 줄일 수 있습니다. 로그 파일 공간 관리, 로그 공간 부족 조건 및 가용성 그룹 복제 메커니즘으로 인해 전체 시스템 제한이 일시적으로 감소할 수 있습니다.

로그 속도 주지사 트래픽 형성은 다음과 같은 대기 유형을 통해 표시됩니다 [(sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV에 노출됨).

| 대기 유형 | 메모 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 데이터베이스 제한 |
| POOL_LOG_RATE_GOVERNOR | 풀 제한 |
| INSTANCE_LOG_RATE_GOVERNOR | 인스턴스 수준 제한 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 피드백 제어, 프리미엄/비즈니스 중요도에서 가용성 그룹 물리적 복제가 유지되지 않음 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 피드백 제어, 로그 공간 부족 조건을 피하기 위해 속도 제한 |
|||

원하는 확장성을 방해하는 로그 속도 제한이 발생하는 경우 다음 옵션을 고려하십시오.
- 최대 96MB/s 로그 속도를 얻거나 다른 서비스 계층으로 전환하려면 더 높은 서비스 수준으로 확장합니다. [하이퍼스케일](sql-database-service-tier-hyperscale.md) 서비스 계층은 선택한 서비스 수준에 관계없이 100MB/s 로그 속도를 제공합니다.
- 로드중인 데이터가 ETL 프로세스의 스테이징 데이터와 같이 일시적인 경우 tempdb에 로드할 수 있습니다(최소로 기록됨). 
- 분석 시나리오의 경우 클러스터된 columnstore 적용 테이블에 로드합니다. 이렇게 하면 압축으로 인해 필요한 로그 속도가 줄어듭니다. 이 기술은 CPU 사용률을 높이고 클러스터된 columnstore 인덱스의 이점을 활용하는 데이터 집합에만 적용됩니다. 

## <a name="next-steps"></a>다음 단계

- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)를 참조하세요.
- tempdb 크기 한도에 대한 자세한 내용은 [Azure SQL Database의 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.
