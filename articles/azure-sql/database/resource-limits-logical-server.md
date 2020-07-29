---
title: Azure의 논리 서버에 대 한 리소스 제한
description: 이 문서에서는 Azure SQL Database 및 Azure Synapse Analytics에서 사용 되는 Azure의 논리 서버에 대 한 리소스 제한에 대 한 개요를 제공 합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 06/10/2020
ms.openlocfilehash: eac5814eb977a01135ad2fcd9551b3475673dbca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691753"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL Database 및 Azure Synapse Analytics 서버에 대 한 리소스 제한
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

이 문서에서는 Azure SQL Database 및 Azure Synapse Analytics에서 사용 되는 논리 서버에 대 한 리소스 제한에 대 한 개요를 제공 합니다. 리소스 제한에 도달 하거나 초과 하는 경우 발생 하는 상황에 대 한 정보를 제공 하 고 이러한 제한을 적용 하는 데 사용 되는 리소스 관리 메커니즘을 설명 합니다.

> [!NOTE]
> Azure SQL Managed Instance 제한에 대해서 [는 관리 되는 인스턴스의 리소스 제한 SQL Database](../managed-instance/resource-limits.md)을 참조 하세요.

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
> 데이터베이스 수가 서버당 제한에 근접하면 다음이 발생할 수 있습니다.
>
> - 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> - 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

> [!NOTE]
> 더 많은 DTU/eDTU 할당량, vCore 할당량 또는 기본 용량 보다 많은 서버를 얻으려면 Azure Portal에 새 지원 요청을 제출 합니다. 자세한 내용은 [Azure SQL Database에 대 한 요청 할당량 늘리기](quota-increase-request.md)를 참조 하세요.

### <a name="storage-size"></a>스토리지 크기

단일 데이터베이스 리소스 저장소 크기의 경우 가격 책정 계층 당 저장소 크기 제한에 대 한 [DTU 기반 리소스 제한](resource-limits-dtu-single-databases.md) 또는 [vcore 기반 리소스 제한](resource-limits-vcore-single-databases.md) 을 참조 하세요.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>데이터베이스 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-cpu"></a>CPU 계산

데이터베이스 계산 CPU 사용률이 높으면 쿼리 대기 시간이 늘어나고 쿼리 시간이 초과 될 수도 있습니다. 이러한 조건에서 쿼리는 서비스에 의해 큐에 대기 될 수 있으며 리소스가 사용 가능 해지면 실행할 리소스를 제공 합니다.
높은 컴퓨팅 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기를 늘려 데이터베이스에 더 많은 컴퓨팅 리소스를 제공합니다. [단일 데이터베이스 리소스 확장](single-database-scale.md) 및 [탄력적 풀 리소스 확장](elastic-pool-scale.md)을 참조하세요.
- 쿼리를 최적화 하 여 각 쿼리의 CPU 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>스토리지

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](troubleshoot-common-errors-issues.md)가 표시됩니다. SELECT 및 DELETE 문은 계속 성공 합니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 저장소를 더 추가 합니다. [단일 데이터베이스 리소스 확장](single-database-scale.md) 및 [탄력적 풀 리소스 확장](elastic-pool-scale.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 있는 경우에는 데이터베이스를 풀 외부로 이동 하 여 해당 저장소 공간을 다른 데이터베이스와 공유 하지 않을 수 있습니다.
- 데이터베이스를 축소하여 사용하지 않는 공간을 회수합니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청)

세션 및 작업자의 최대 수는 서비스 계층 및 계산 크기 (Dtu/Edtu 또는 vCores)에 따라 결정 됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 애플리케이션에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 이는 데이터베이스 리소스 제한에 도달 했을 때 발생 하는 최대 부하 기간 동안 쿼리, 큰 차단 체인 또는 과도 한 쿼리 병렬 처리로 인해 작업자를 누적 하는 경우에 특히 그렇습니다.

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](single-database-scale.md) 및 [탄력적 풀 리소스 확장](elastic-pool-scale.md)을 참조하세요.
- 컴퓨팅 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](performance-guidance.md#query-tuning-and-hinting)를 참조하세요.
- [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (최대 병렬 처리 수준) 설정 감소.
- 쿼리 작업을 최적화 하 여 쿼리 차단의 발생 횟수와 기간을 줄입니다.

### <a name="memory"></a>메모리

다른 리소스 (CPU, 작업자, 저장소)와 달리 메모리 한도에 도달 하면 쿼리 성능에 부정적인 영향을 주지 않으며 오류 및 오류를 발생 시 키 지 않습니다. [메모리 관리 아키텍처 가이드](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide)에 자세히 설명 된 대로 SQL Server 데이터베이스 엔진은 사용 가능한 모든 메모리를 의도적으로 사용 합니다. 메모리는 주로 데이터를 캐시 하는 데 사용 되어 비용이 많이 드는 저장소 액세스를 방지 합니다. 따라서 더 높은 메모리 사용률은 일반적으로 저장소에서 더 느리게 읽을 수 있는 것이 아니라 메모리에서 읽기가 더 빠르기 때문에 쿼리 성능을 향상 시킵니다.

데이터베이스 엔진을 시작한 후 작업이 저장소에서 데이터를 읽기 시작할 때 데이터베이스 엔진은 메모리에 데이터를 적극적으로 캐시 합니다. 이 초기 램프 기간 후에는 일반적 이며, `avg_memory_usage_percent` `avg_instance_memory_percent` [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 의 및 열이 100% (특히 유휴 상태가 아닌 데이터베이스의 경우)와 같이 표시 되 고 메모리에 완전히 들어가지 않습니다.

데이터 캐시 외에도, 메모리는 데이터베이스 엔진의 다른 구성 요소에서 사용 됩니다. 메모리에 대 한 요구가 있고 데이터 캐시에서 사용 가능한 모든 메모리를 사용 하는 경우 데이터베이스 엔진은 메모리를 다른 구성 요소에 사용할 수 있도록 데이터 캐시 크기를 동적으로 축소 하 고 다른 구성 요소에서 메모리를 해제할 때 데이터 캐시를 동적으로 확장 합니다.

드문 경우 지만 충분 한 작업을 수행 하면 메모리 부족 오류가 발생 하 여 메모리 부족 오류가 발생할 수 있습니다. 이는 0%에서 100% 사이의 모든 메모리 사용률 수준에서 발생할 수 있습니다. 이는 메모리 제한에 비례 하는 작은 계산 크기에서 발생 하거나, [조밀한 탄력적 풀](elastic-pool-resource-management.md)등의 쿼리 처리를 위해 더 많은 메모리를 사용 하는 워크 로드를 사용 하는 경우에 발생할 수 있습니다.

메모리 부족 오류가 발생할 경우 완화 옵션은 다음과 같습니다.
- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](single-database-scale.md) 및 [탄력적 풀 리소스 확장](elastic-pool-scale.md)을 참조하세요.
- 쿼리 및 구성을 최적화 하 여 메모리 사용률을 줄입니다. 일반적인 해결 방법은 다음 표에 설명 되어 있습니다.

|해결 방법|Description|
| :----- | :----- |
|메모리 부여 크기 줄이기|메모리 부여에 대 한 자세한 내용은 [메모리 부여 SQL Server 이해](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) 블로그 게시물을 참조 하세요. 과도 한 메모리 부여를 방지 하는 일반적인 솔루션은 [통계](https://docs.microsoft.com/sql/relational-databases/statistics/statistics) 를 최신 상태로 유지 하는 것입니다. 이로 인해 쿼리 엔진에서 메모리 사용을 보다 정확 하 게 예측 하 여 불필요 한 메모리 부여를 방지 합니다.</br></br>호환성 수준 140 이상을 사용 하는 데이터베이스에서 데이터베이스 엔진은 [일괄 처리 모드 메모리 부여 피드백](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#batch-mode-memory-grant-feedback)을 사용 하 여 메모리 부여 크기를 자동으로 조정할 수 있습니다. 호환성 수준 150 이상을 사용 하는 데이터베이스에서 데이터베이스 엔진은 보다 일반적인 행 모드 쿼리에 대해 [행 모드 메모리 부여 피드백](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#row-mode-memory-grant-feedback)을 사용 합니다. 이 기본 제공 기능을 사용 하면 불필요 하 게 많은 메모리 부여로 인 한 메모리 부족 오류를 방지할 수 있습니다.|
|쿼리 계획 캐시 크기 줄이기|데이터베이스 엔진은 모든 쿼리 실행에 대해 쿼리 계획을 컴파일하지 않도록 메모리에 쿼리 계획을 캐시 합니다. 한 번만 사용 되는 캐싱 계획으로 인해 발생 하는 쿼리 계획 캐시의 영향을 방지 하려면 OPTIMIZE_FOR_AD_HOC_WORKLOADS [데이터베이스 범위 구성을](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)사용 하도록 설정 합니다.|
|잠금 메모리 크기 줄이기|데이터베이스 엔진은 [잠금](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)에 메모리를 사용 합니다. 가능 하면 많은 수의 잠금을 획득 하 고 높은 잠금 메모리 사용을 유발할 수 있는 큰 트랜잭션을 사용 하지 마십시오.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>사용자 작업 및 내부 프로세스의 리소스 사용량

각 데이터베이스의 사용자 작업에의 한 CPU 및 메모리 사용은 및 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 열에서 dm_db_resource_stats 및 [resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) 뷰에 보고 됩니다. `avg_cpu_percent` `avg_memory_usage_percent` 탄력적 풀의 경우 풀 수준 리소스 소비는 [elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 보고 됩니다. 또한 사용자 작업 CPU 소비는 `cpu_percent` 풀 수준에서 [단일 데이터베이스](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) 및 [탄력적 풀](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) 에 대 한 Azure Monitor 메트릭을 통해 보고 됩니다.

Azure SQL Database은 고가용성 및 재해 복구, 데이터베이스 백업 및 복원, 모니터링, 쿼리 저장소, 자동 조정 등의 핵심 서비스 기능을 구현 하기 위해 계산 리소스가 필요 합니다. 시스템은 [리소스 거 버 넌 스](#resource-governance) 메커니즘을 사용 하 여 이러한 내부 프로세스에 대 한 전체 리소스의 특정 부분을 따로 설정 하 여 나머지 리소스를 사용자 작업에 사용할 수 있도록 합니다. 내부 프로세스에서 계산 리소스를 사용 하지 않는 경우 시스템에서 사용자 작업에 사용할 수 있도록 합니다.

사용자 작업 및 내부 프로세스의 총 CPU 및 메모리 사용은 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 및 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) `avg_instance_cpu_percent` `avg_instance_memory_percent` 열에서 dm_db_resource_stats 및 resource_stats 뷰에서 보고 됩니다. 이 데이터는 `sqlserver_process_core_percent` `sqlserver_process_memory_percent` [단일 데이터베이스](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) 에 대 한 및 Azure Monitor 메트릭과 풀 수준에서 [탄력적 풀](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) 을 통해서도 보고 됩니다.

사용자 작업 및 내부 프로세스에의 한 최근 리소스 사용에 대 한 자세한 분석은 [dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 및 [dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) 보기에서 보고 됩니다. 이러한 보기에서 참조 되는 리소스 풀 및 작업 그룹에 대 한 자세한 내용은 [리소스 관리](#resource-governance)를 참조 하세요. 이러한 뷰는 연결 된 리소스 풀 및 작업 그룹의 특정 내부 프로세스 및 사용자 작업에의 한 리소스 사용률을 보고 합니다.

성능 모니터링 및 문제 해결의 맥락에서 사용자 작업 및 내부 프로세스 (,)에의 한 **사용자 cpu 소비** ( `avg_cpu_percent` , `cpu_percent` ) 및 **총 cpu 소비** 를 고려 하는 것이 중요 합니다 `avg_instance_cpu_percent` `sqlserver_process_core_percent` .

**사용자 CPU 소비** 는 각 서비스 목표에서 사용자 작업 제한의 백분율로 계산 됩니다. 100%의 **사용자 CPU 사용률** 은 사용자 작업이 서비스 목표 한도에 도달 했음을 나타냅니다. 그러나 **총 cpu 사용량이** 70-100% 범위에 도달 하면 보고 된 **사용자 CPU 사용량이** 100% 미만으로 유지 되는 경우에도 사용자 작업 처리량이 증가 하 고 쿼리 대기 시간이 증가 하는 것을 볼 수 있습니다. 이는 계산 리소스의 중간 할당으로 더 작은 서비스 목표를 사용 하는 경우에 발생할 수 있지만, [조밀한 탄력적 풀](elastic-pool-resource-management.md)과 같은 비교적 강력한 사용자 작업을 사용 하는 경우가 많습니다. 이는 데이터베이스의 새 복제본을 만드는 경우와 같이 내부 프로세스에 일시적으로 추가 리소스가 필요할 때 더 작은 서비스 목표에서 발생할 수도 있습니다.

**총 CPU 사용량이** 높으면 완화 옵션은 앞에서 설명한 것과 같으며 서비스 목표 증가 및/또는 사용자 작업 최적화를 포함 합니다.

## <a name="resource-governance"></a>리소스 거버넌스

리소스 제한을 적용 하기 위해 Azure SQL Database는 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), 수정 및 확장을 기반으로 하는 리소스 거 버 넌 스 구현을 사용 하 여 Azure SQL Database에서 실행 합니다. SQL Database에서 리소스 제한이 풀과 그룹 수준 모두에서 설정 된 여러 [리소스 풀](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) 및 [작업 그룹](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)은 [분산 된 데이터베이스 서비스](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)를 제공 합니다. 사용자 작업 및 내부 작업은 별도의 리소스 풀 및 작업 그룹으로 분류 됩니다. 지역 복제본을 비롯 하 여 읽기 가능한 주 보조 복제본의 사용자 작업은 `SloSharedPool1` 리소스 풀 및 작업 그룹으로 분류 됩니다 `UserPrimaryGroup.DBId[N]` . 여기서는 `N` 데이터베이스 ID 값을 나타냅니다. 또한 다양 한 내부 워크 로드에 대 한 여러 리소스 풀 및 작업 그룹이 있습니다.

는 Resource Governor를 사용 하 여 SQL 프로세스 내에서 리소스를 제어 하는 것 외에도 Windows [작업 개체](https://docs.microsoft.com/windows/win32/procthread/job-objects) 를 사용 하 여 프로세스 수준 리소스 거 버 넌 스와 저장소 할당량 관리를 위한 Windows [파일 서버 리소스 관리자 (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 를 사용 Azure SQL Database.

Azure SQL Database 리소스 관리는 본질적으로 계층적입니다. 위에서 아래로, 운영 체제 리소스 관리 메커니즘을 사용 하 고 저장소 볼륨 수준에서 운영 체제 리소스 관리 메커니즘을 사용 하 고 Resource Governor 한 다음 Resource Governor를 사용 하 여 리소스 풀 수준에서 제한을 적용 하 고 Resource Governor를 사용 하 여 작업 그룹 수준에서 제한을 적용 합니다. 현재 데이터베이스 또는 탄력적 풀에 적용 되는 리소스 관리 제한은 [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 뷰에 표시 됩니다.

### <a name="data-io-governance"></a>데이터 IO 거 버 넌 스

데이터 IO 거 버 넌 스는 데이터베이스의 데이터 파일에 대 한 읽기 및 쓰기 물리적 IO를 제한 하는 데 사용 되는 Azure SQL Database의 프로세스입니다. 각 서비스 수준에 대해 IOPS 제한이 설정 되어 "잡음이 있는 환경" 효과를 최소화 하 고, 다중 테 넌 트 서비스에서 리소스 할당 범위를 제공 하 고, 기본 하드웨어 및 저장소의 기능을 유지할 수 있습니다.

단일 데이터베이스의 경우 데이터베이스에 대 한 모든 저장소 IO에 작업 그룹 제한이 적용 되는 반면, 리소스 풀 제한은 동일한 SQL 풀에 있는 모든 데이터베이스 (데이터베이스 포함)에 대해 모든 저장소 IO에 적용 됩니다 `tempdb` . 탄력적 풀의 경우 작업 그룹 제한은 풀의 각 데이터베이스에 적용 되는 반면, 리소스 풀 제한은 `tempdb` 풀의 모든 데이터베이스 간에 공유 되는 데이터베이스를 포함 하 여 전체 탄력적 풀에 적용 됩니다. 일반적으로 작업 그룹 제한은 리소스 풀 제한 보다 낮으므로 IOPS/처리량을 단축 하기 때문에 데이터베이스 (단일 또는 풀)에 대해 작업에서 리소스 풀 제한을 달성 하지 못할 수 있습니다. 그러나 풀 제한은 동일한 풀의 여러 데이터베이스에 대해 결합 된 워크 로드에서 도달할 수 있습니다.

예를 들어 쿼리가 IO 리소스 관리 없이 1000 IOPS를 생성 하지만 작업 그룹 최대 IOPS 제한이 900 IOPS로 설정 된 경우 쿼리에서 900 IOPS를 초과 하 여 생성할 수 없습니다. 그러나 리소스 풀의 최대 IOPS 한도가 1500 IOPS로 설정 되 고 리소스 풀과 연결 된 모든 작업 그룹의 총 IO가 1500 IOPS를 초과 하는 경우 동일한 쿼리의 IO가 작업 그룹 제한 (900 IOPS) 아래로 줄어들 수 있습니다.

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 뷰에서 반환 된 IOPS 및 처리량 최소/최대 값은 보장 되는 것이 아니라 극한/cap 역할을 합니다. 또한 리소스 관리는 특정 저장소 대기 시간을 보장 하지 않습니다. 지정 된 사용자 작업에 대해 가장 적합 한 대기 시간, IOPS 및 처리량은 IO 리소스 거 버 넌 스 제한 뿐만 아니라 사용 되는 IO 크기와 기본 저장소의 기능에 따라 달라 집니다. SQL Database는 512 KB와 4mb 사이의 크기를 변경 하는 IOs를 사용 합니다. IOPS 제한을 적용 하기 위해 모든 IO는 크기와 관계 없이 사용 되며 데이터 파일이 있는 데이터베이스는 Azure Storage에서 제외 됩니다. 이 경우 256 KB 보다 큰 IOs는 Azure Storage IO 계정에 맞게 여러 256 KB IOs로 계산 됩니다.

Azure Storage의 데이터 파일을 사용 하는 기본, 표준 및 범용 데이터베이스의 경우 `primary_group_max_io` 데이터베이스에 누적 된 IOPS 수를 제공 하기에 충분 한 데이터 파일이 없거나, 데이터가 여러 파일에 균등 하 게 분산 되지 않거나, 기본 blob의 성능 계층이 리소스 관리 제한 보다 낮은 IOPS/처리량을 제한 하는 경우 값을 달성할 수 없습니다. 마찬가지로 빈번한 트랜잭션 커밋에 의해 생성 된 작은 로그 Io를 사용 하는 경우 `primary_max_log_rate` 기본 Azure storage blob에 대 한 IOPS 제한으로 인해 작업에서 값을 달성 하지 못할 수 있습니다.

및와 같은 리소스 사용률 `avg_data_io_percent` 값 `avg_log_write_percent` 은 [dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)및 [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 뷰에서 보고 되는 리소스 사용률 값은 최대 리소스 거 버 넌 스 제한의 백분율로 계산 됩니다. 따라서 리소스 관리 외의 요소가 IOPS/처리량을 제한 하는 경우 보고 된 리소스 사용률이 100% 미만으로 유지 되는 경우에도 워크 로드 수가 늘어나면 IOPS/처리량을 평면화 하 고 대기 시간을 늘릴 수 있습니다.

데이터베이스 파일당 읽기 및 쓰기 IOPS, 처리량 및 대기 시간을 확인 하려면 [dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 함수를 사용 합니다. 이 함수는 고려 되지 않는 백그라운드 IO를 비롯 하 여 데이터베이스에 대 한 모든 IO를 표시 `avg_data_io_percent` 하지만 기본 저장소의 IOPS 및 처리량을 사용 하며 관찰 된 저장소 대기 시간에 영향을 줄 수 있습니다. 또한 함수는 및 열에서 각각 읽기 및 쓰기에 대 한 IO 리소스 관리에 의해 도입 될 수 있는 추가 대기 시간을 표시 합니다 `io_stall_queued_read_ms` `io_stall_queued_write_ms` .

### <a name="transaction-log-rate-governance"></a>트랜잭션 로그 요금 거 버 넌 스

트랜잭션 로그 속도 거 버 넌 스는 대량 삽입, SELECT INTO 및 인덱스 빌드와 같은 워크 로드에 대 한 높은 수집 속도를 제한 하는 데 사용 되는 Azure SQL Database의 프로세스입니다. 이러한 한도는 초이 수준에서 로그 레코드 생성 속도까지 추적 되 고 적용 되며, 데이터 파일에 대해 실행할 수 있는 IOs 수에 관계 없이 처리량이 제한 됩니다.  현재 트랜잭션 로그 생성 속도는 하드웨어에 종속 된 지점까지 선형적으로 확장 되며 vCore 구매 모델을 사용 하 여 최대 로그 속도는 96 m b/초까지 허용 됩니다.

> [!NOTE]
> 실제 실제 Io 트랜잭션 로그 파일은 제어 되거나 제한 되지 않습니다.

로그 속도는 다양 한 시나리오에서 달성 하 고 유지할 수 있도록 설정 되며, 전체 시스템은 사용자 부하에 대 한 영향이 최소화 된 기능을 유지할 수 있습니다. 로그 전송률 관리는 트랜잭션 로그 백업이 게시 된 복구 가능성 Sla 내에 유지 되도록 합니다.  이 거 버 넌 스는 또한 보조 복제본의 과도 한 백로그를 방지 합니다.

로그 레코드가 생성 되 면 각 작업이 평가 되 고, 최대 원하는 로그 전송률 (초당 MB/s)을 유지 하기 위해 지연 되어야 하는지 여부가 평가 됩니다. 로그 레코드가 저장소에 플러시될 때 지연 시간이 추가 되지 않고 로그 속도가 자동으로 생성 되는 동안 로그 속도가 적용 됩니다.

런타임에 적용 되는 실제 로그 생성 요금은 피드백 메커니즘의 영향을 받을 수 있으므로 시스템이 안정화 될 수 있도록 허용 가능한 로그 비율을 일시적으로 줄일 수 있습니다. 로그 파일 공간 관리를 사용 하 여 로그 공간 부족 상태를 방지 하 고 가용성 그룹 복제 메커니즘을 통해 전체 시스템 제한을 일시적으로 낮출 수 있습니다.

로그 전송률 관리자 트래픽 셰이핑은 다음 대기 유형 ( [dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 및 [dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 뷰에 노출 됨)을 통해 표시 됩니다.

| 대기 유형 | 참고 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 데이터베이스 제한 |
| POOL_LOG_RATE_GOVERNOR | 풀 제한 |
| INSTANCE_LOG_RATE_GOVERNOR | 인스턴스 수준 제한 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 피드백 컨트롤, 가용성 그룹 프리미엄/중요 비즈니스용의 물리적 복제를 유지 하지 않음 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 로그 공간 부족 상태를 방지 하기 위해 사용자 의견 제어, 속도 제한 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 지리적 복제 피드백 제어, 로그 빈도를 제한 하 여 데이터 대기 시간이 높고 지역 보조 복제본이 사용 되지 않도록 합니다.|
|||

원하는 확장성을 hampering 로그 전송률 제한이 발생할 경우 다음 옵션을 고려 하십시오.

- 최대 96 m b/초 로그 속도를 얻거나 다른 서비스 계층으로 전환 하기 위해 더 높은 서비스 수준으로 확장 합니다. [Hyperscale](service-tier-hyperscale.md) 서비스 계층은 선택한 서비스 수준에 관계 없이 100 m b/초 로그 비율을 제공 합니다.
- 데이터를 로드 하는 경우 ETL 프로세스에서 데이터를 준비 하는 것과 같이 임시 데이터는 tempdb로 로드 될 수 있습니다 (최소 기록 됨).
- 분석 시나리오의 경우 클러스터형 columnstore 대상 테이블로 로드 합니다. 이렇게 하면 압축으로 인해 필요한 로그 전송률이 줄어듭니다. 이 기법은 CPU 사용률을 높이고 클러스터형 columnstore 인덱스를 활용 하는 데이터 집합에만 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU](purchasing-models.md#dtu-based-purchasing-model)를 참조하세요.
- tempdb 크기 한도에 대한 자세한 내용은 [Azure SQL Database의 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.
 