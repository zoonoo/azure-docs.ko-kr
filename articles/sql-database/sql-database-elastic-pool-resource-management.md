---
title: 고밀도 탄력적 풀의 리소스 관리 | 마이크로 소프트 문서
description: 많은 데이터베이스가 있는 Azure SQL 탄력적 풀에서 계산 리소스를 관리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473730"
---
# <a name="resource-management-in-dense-elastic-pools"></a>조밀한 탄력적 풀의 리소스 관리

Azure SQL Database [탄력적 풀은](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) 다양한 리소스 사용량을 가진 많은 데이터베이스를 관리하기 위한 비용 효율적인 솔루션입니다. 탄력적 풀의 모든 데이터베이스는 풀에 있는 **데이터베이스의 하위 집합만 지정된 시간에 계산 리소스를 사용한다는**가정 하에 CPU, 메모리, 작업자 스레드, 저장소 공간, tempdb와 같은 동일한 리소스 할당을 공유합니다. 이러한 가정을 통해 탄력적 풀은 비용 효율적입니다. 고객은 각 개별 데이터베이스가 잠재적으로 필요로 할 수 있는 모든 리소스에 대해 지불하는 대신 풀의 모든 데이터베이스 간에 공유되는 훨씬 적은 리소스 집합에 대해 비용을 지불합니다.

## <a name="resource-governance"></a>리소스 거버넌스

리소스 공유는 리소스 사용량이 많은 데이터베이스가 동일한 탄력적 풀의 다른 데이터베이스에 영향을 미치는 "시끄러운 이웃" 효과를 최소화하기 위해 리소스 사용을 신중하게 제어해야 합니다. 동시에 시스템은 안정적으로 작동하려면 고가용성 및 재해 복구(HADR), 백업 및 복원, 모니터링, 쿼리 저장소, 자동 튜닝 등과 같은 기능에 충분한 리소스를 제공해야 합니다.

Azure SQL Database는 프로세스 수준 리소스 거버넌스를 위한 Windows [작업 개체,](https://docs.microsoft.com/windows/win32/procthread/job-objects) 저장소 할당량 관리를 위한 [Windows 파일 서버 리소스 관리자(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 및 수정되고 확장된 버전의 SQL Server [Resource 관리자를](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 포함하여 여러 리소스 거버넌스 메커니즘을 사용하여 SQL Azure Database에서 실행되는 각 SQL Server 인스턴스 내에서 리소스 거버넌스를 구현하여 이러한 목표를 달성합니다.

탄력적 풀의 주요 설계 목표는 비용 효율적인 것입니다. 이러한 이유로 이 시스템은 고객이 의도적으로 _조밀한_ 풀을 만들 수 있도록 합니다. 같은 이유로 시스템은 내부 프로세스에 필요한 모든 리소스를 예약하지는 않지만 내부 프로세스와 사용자 워크로드 간에 리소스를 공유할 수 있습니다.

이 방법을 사용하면 고객이 조밀한 탄력적 풀을 사용하여 적절한 성능과 주요 비용 절감을 달성할 수 있습니다. 그러나 조밀한 풀의 여러 데이터베이스에 대한 워크로드가 충분히 치열하면 리소스 경합이 중요해집니다. 리소스 경합은 사용자 워크로드 성능을 저하시키고 내부 프로세스에 부정적인 영향을 줄 수 있습니다.

> [!IMPORTANT]
> 활성 데이터베이스가 많은 조밀한 풀에서는 풀의 데이터베이스 수를 [DTU](sql-database-dtu-resource-limits-elastic-pools.md) 및 [vCore](sql-database-vcore-resource-limits-elastic-pools.md) 탄성 풀에 대해 문서화된 최대값까지 늘리는 것이 불가능할 수 있습니다.
> 
> 리소스 경합 및 성능 문제를 일으키지 않고 조밀한 풀에 배치할 수 있는 데이터베이스 수는 동시에 활성 데이터베이스 수와 각 데이터베이스의 사용자 워크로드에 의한 리소스 사용량에 따라 달라집니다. 이 번호는 사용자 워크로드가 변경됨에 따라 시간이 지남에 따라 변경될 수 있습니다.

밀집된 풀에서 리소스 경합이 발생하면 고객은 다음 작업 중 하나 이상을 선택하여 리소스 경합을 완화할 수 있습니다.
- 쿼리 워크로드를 조정하여 리소스 소비를 줄이거나 시간이 지남에 따라 여러 데이터베이스에 리소스 소비를 분산시입니다.
- 일부 데이터베이스를 다른 풀로 이동하거나 독립 실행형 데이터베이스로 만들어 풀 밀도를 줄입니다.
- 풀을 확장하여 더 많은 리소스를 가져옵니다.

마지막 두 작업을 구현하는 방법에 대한 제안 사항은 이 문서의 후반부에서 [운영 권장 사항을](#operational-recommendations) 참조하세요. 리소스 경합을 줄이면 사용자 워크로드와 내부 프로세스 모두에 이점이 있으며 시스템이 예상 서비스 수준을 안정적으로 유지할 수 있습니다.

## <a name="monitoring-resource-consumption"></a>리소스 소비 모니터링

리소스 경합으로 인한 성능 저하를 방지하려면 조밀한 탄력적 풀을 사용하는 고객은 리소스 소비를 사전에 모니터링하고 리소스 경합증가가 워크로드에 영향을 미치기 시작하면 적시에 조치를 취해야 합니다. 지속적인 모니터링은 사용자 워크로드의 변경, 데이터 볼륨 및 배포의 변경, 풀 밀도 변경 및 Azure SQL Database 서비스의 변경으로 인해 풀의 리소스 사용량이 시간이 지남에 따라 변경되기 때문에 중요합니다. 

Azure SQL Database는 이러한 유형의 모니터링과 관련된 몇 가지 메트릭을 제공합니다. 각 메트릭에 대해 권장되는 평균 값을 초과하면 풀의 리소스 경합을 나타내며 앞에서 설명한 작업 중 하나를 사용하여 해결해야 합니다.

|메트릭 이름|설명|권장 평균 값| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|기본 운영 체제에서 측정한 탄력적 풀과 관련된 SQL Server 프로세스의 CPU 사용률입니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 보기와 데이터베이스의 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. `master` 이 메트릭은 이름이 [지정된](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`Azure Monitor로도 내보내지며 Azure Portal에서 볼 수 있습니다. 이 값은 동일한 탄력적 풀의 모든 데이터베이스에 대해 동일합니다.|70% 미만. 90%까지 짧은 스파이크가 허용될 수 있습니다.|
|`max_worker_percent`|[작업자 스레드]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) 사용률입니다. 풀의 각 데이터베이스와 풀 자체에 대해서도 제공됩니다. 데이터베이스 수준및 풀 수준에서 작업자 스레드 수에 대한 제한이 다르므로 두 수준에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 보기와 데이터베이스의 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. `master` 이 메트릭은 이름이 [지정된](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`Azure Monitor로도 내보내지며 Azure Portal에서 볼 수 있습니다.|80% 미만. 최대 100%의 스파이크로 인해 연결 시도 및 쿼리가 실패합니다.|
|`avg_data_io_percent`|실제 IO읽기 및 쓰기에 대한 IOPS 사용률. 풀의 각 데이터베이스와 풀 자체에 대해서도 제공됩니다. 데이터베이스 수준및 풀 수준에서 IOPS 수에 대한 다른 제한이 있으므로 두 수준에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 보기와 데이터베이스의 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. `master` 이 메트릭은 이름이 [지정된](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`Azure Monitor로도 내보내지며 Azure Portal에서 볼 수 있습니다.|80% 미만. 100%까지 짧은 스파이크가 허용될 수 있습니다.|
|`avg_log_write_percent`|트랜잭션 로그에 대한 처리량 사용률은 IO를 작성합니다. 풀의 각 데이터베이스와 풀 자체에 대해서도 제공됩니다. 데이터베이스 수준과 풀 수준에서 로그 처리량에는 서로 다른 제한이 있으므로 두 수준에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기와 데이터베이스의 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. `master` 이 메트릭은 이름이 [지정된](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`Azure Monitor로도 내보내지며 Azure Portal에서 볼 수 있습니다. 이 메트릭이 100%에 가까우면 모든 데이터베이스 수정(INSERT, UPDATE, DELETE, MERGE 문, SELECT ... 안으로 들어가고, 벌크 인서삽입 등) 속도가 느려집니다.|90% 미만. 100%까지 짧은 스파이크가 허용될 수 있습니다.|
|`oom_per_second`|메모리 압력의 지표인 탄성 풀에서 OOM(메모리 부족) 오류의 비율입니다. [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) 보기에서 사용할 수 있습니다. 이 메트릭을 계산하는 샘플 쿼리의 [예제를](#examples) 참조하십시오.|0|
|`avg_storage_percent`|탄력적 풀 수준에서 스토리지 공간 활용도. 데이터베이스의 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 `master` 사용할 수 있습니다. 이 메트릭은 이름이 [지정된](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`Azure Monitor로도 내보내지며 Azure Portal에서 볼 수 있습니다.|80% 미만. 데이터 증가 없이 풀에 대해 100%에 접근할 수 있습니다.|
|`tempdb_log_used_percent`|데이터베이스의 트랜잭션 로그 공간 사용률입니다. `tempdb` 한 데이터베이스에서 만든 임시 개체가 동일한 탄력적 풀의 다른 `tempdb` 데이터베이스에 표시되지 않더라도 동일한 풀에 있는 모든 데이터베이스에 대한 공유 리소스입니다. 풀의 한 데이터베이스에서 `tempdb` 시작된 장기 실행 또는 유휴 트랜잭션은 트랜잭션 로그의 상당 부분을 소비할 수 있으며 동일한 풀의 다른 데이터베이스에서 쿼리에 실패할 수 있습니다. [sys.dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) 보기에서 사용할 수 있습니다. 이 메트릭은 Azure 모니터로도 내보내지며 Azure Portal에서 볼 수 있습니다. 이 메트릭의 현재 값을 반환하는 샘플 쿼리의 [예제를](#examples) 참조하십시오.|50% 미만. 최대 80%의 스파이크가 허용됩니다.|
|||

이러한 메트릭 외에도 Azure SQL Database는 실제 리소스 거버넌스 제한을 반환하는 보기와 리소스 풀 수준 및 워크로드 그룹 수준에서 리소스 사용률 통계를 반환하는 추가 보기를 제공합니다.

|뷰 이름|설명|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|현재 데이터베이스 또는 탄력적 풀에서 리소스 거버넌스 메커니즘에서 사용하는 실제 구성 및 용량 설정을 반환합니다.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|현재 리소스 풀 상태, 리소스 풀의 현재 구성 및 누적 리소스 풀 통계에 대한 정보를 반환합니다.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|누적 워크로드 그룹 통계 및 워크로드 그룹의 현재 구성을 반환합니다. 이 보기는 `pool_id` 열에 sys.dm_resource_governor_resource_pools 함께 조인하여 리소스 풀 정보를 얻을 수 있습니다.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|지난 32분 동안 리소스 풀 사용률 통계를 반환합니다. 각 행은 20초 간격을 나타냅니다. 열은 `delta_` 간격 동안 각 통계의 변경 을 반환합니다.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|지난 32분 동안 워크로드 그룹 사용률 통계를 반환합니다. 각 행은 20초 간격을 나타냅니다. 열은 `delta_` 간격 동안 각 통계의 변경 을 반환합니다.|
|||

이러한 보기는 리소스 사용률을 모니터링하고 거의 실시간으로 리소스 경합 문제를 해결하는 데 사용할 수 있습니다. 지리적 복제본을 포함하여 기본 및 읽을 수 있는 보조 복제본의 사용자 워크로드는 `SloSharedPool1` `N` 데이터베이스 ID 값을 나타내는 리소스 풀 및 `UserPrimaryGroup.DBId[N]` 워크로드 그룹으로 분류됩니다.

현재 리소스 사용률을 모니터링하는 것 외에도 조밀한 풀을 사용하는 고객은 별도의 데이터 저장소에서 기록 리소스 사용률 데이터를 유지할 수 있습니다. 이 데이터는 예측 분석에 사용하여 과거 및 계절적 추세를 기반으로 리소스 활용도를 사전에 관리할 수 있습니다.

## <a name="operational-recommendations"></a>운영 권장 사항

**충분한 리소스 헤드 룸을 남겨주세요.** 리소스 경합 및 성능 저하가 발생하는 경우 완화에는 영향을 받는 탄력적 풀에서 일부 데이터베이스를 이동하거나 앞에서 설명한 대로 풀을 확장하는 것이 포함될 수 있습니다. 그러나 이러한 작업을 완료하려면 추가 계산 리소스가 필요합니다. 특히 프리미엄 및 비즈니스 중요 풀의 경우 이러한 작업을 수행하려면 이동 중인 데이터베이스또는 풀의 확장이 있는 경우 탄력적 풀의 모든 데이터베이스에 대한 모든 데이터를 전송해야 합니다. 데이터 전송은 오래 실행되고 리소스를 많이 사용하는 작업입니다. 풀이 이미 높은 리소스 압력을 받고 있는 경우 완화 작업 자체는 성능을 더욱 저하시게 됩니다. 극단적인 경우에는 필요한 리소스를 사용할 수 없기 때문에 데이터베이스 이동 또는 풀 확장을 통해 리소스 경합을 해결하지 못할 수 있습니다. 이 경우 영향을 받는 탄력적 풀에서 쿼리 워크로드를 일시적으로 줄이는 것이 유일한 솔루션일 수 있습니다.

조밀한 풀을 사용하는 고객은 앞에서 설명한 대로 리소스 사용률 추세를 면밀히 모니터링하고 메트릭이 권장 범위 내에 남아 있고 탄력적 풀에 충분한 리소스가 있는 동안 완화 조치를 취해야 합니다.

리소스 사용률은 각 데이터베이스및 각 탄력적 풀에 대해 시간이 지남에 따라 변경되는 여러 요인에 따라 달라집니다. 밀집된 풀에서 최적의 가격/성능 비율을 달성하려면 지속적인 모니터링과 재조정이 필요하며, 이는 데이터베이스를 활용도가 높은 풀에서 덜 활용된 풀로 이동하고, 증가하는 워크로드를 수용하기 위해 필요에 따라 새 풀을 만들어야 합니다.

**"핫" 데이터베이스를 이동하지 마십시오.** 풀 수준에서 리소스 경합이 주로 사용률이 높은 소수의 데이터베이스로 인해 발생하는 경우 이러한 데이터베이스를 덜 활용된 풀로 이동하거나 독립 실행형 데이터베이스로 만들 려는 것이 좋습니다. 그러나 이동 작업이 이동 작업으로 인해 이동 중인 데이터베이스와 전체 풀의 성능이 더욱 저하되므로 데이터베이스의 활용도가 높은 상태에서는 이 작업을 수행하지 않는 것이 좋습니다. 대신 높은 사용률이 가라앉을 때까지 기다리거나 풀 수준에서 리소스 부담을 덜어주기 위해 덜 활용되는 데이터베이스를 이동합니다. 그러나 사용률이 매우 낮은 데이터베이스를 이동해도 풀 수준에서 리소스 사용률이 크게 감소하지 않으므로 이 경우에는 이점이 없습니다.

**"격리" 풀에서 새 데이터베이스를 만듭니다.** 데이터베이스당 테넌트 모델을 사용하는 응용 프로그램과 같이 새 데이터베이스가 자주 만들어지는 시나리오에서는 기존 탄력적 풀에 배치된 새 데이터베이스가 예기치 않게 상당한 리소스를 사용하고 다른 데이터베이스에 영향을 줄 위험이 있습니다. 및 풀의 내부 프로세스. 이러한 위험을 완화하려면 충분한 리소스 할당을 갖춘 별도의 "격리" 풀을 만듭니다. 아직 알려지지 않은 리소스 소비 패턴이 있는 새 데이터베이스에 이 풀을 사용합니다. 데이터베이스가 일주일 또는 한 달과 같은 비즈니스 주기 동안 이 풀에 머무르고 리소스 사용량이 알려진 경우 이 추가 리소스 사용량을 수용할 수 있는 충분한 용량의 풀로 이동할 수 있습니다.

**지나치게 조밀한 논리 서버를 피하십시오.** Azure SQL Database는 논리 서버당 최대 5000개의 데이터베이스를 [지원합니다.](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) 수천 개의 데이터베이스가 있는 탄력적 풀을 사용하는 고객은 지원되는 한도까지의 총 데이터베이스 수와 함께 단일 서버에 여러 개의 탄력적 풀을 배치하는 것을 고려할 수 있습니다. 그러나 수천 개의 데이터베이스가 있는 논리 서버는 운영상의 문제를 일으킵니다. 서버의 모든 데이터베이스(예: 포털의 데이터베이스 보기)를 열거해야 하는 작업은 느려집니다. 서버 수준 로그인 또는 방화벽 규칙을 잘못 수정하는 등의 운영 오류는 더 많은 수의 데이터베이스에 영향을 미칩니다. 논리적 서버를 실수로 삭제하려면 삭제된 서버의 데이터베이스를 복구하기 위해 Microsoft 지원팀의 도움이 필요하며 영향을 받는 모든 데이터베이스에 대한 중단이 장기화됩니다.

논리 서버당 데이터베이스 수를 지원되는 최대 데이터베이스보다 낮은 숫자로 제한하는 것이 좋습니다. 대부분의 시나리오에서 서버당 최대 1,000-2000개의 데이터베이스를 사용하는 것이 최적입니다. 실수로 서버가 삭제될 가능성을 줄이려면 논리 서버 또는 해당 리소스 그룹에 [삭제 잠금을](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) 배치하는 것이 좋습니다.

과거에는 동일한 논리 서버에서 탄력적 풀에서 데이터베이스를 이동하거나 외부로 이동하거나 이동하는 특정 시나리오가 논리 서버 간에 데이터베이스를 이동할 때보다 더 빨랐습니다. 현재 모든 데이터베이스 이동은 원본 및 대상 논리 서버에 관계없이 동일한 속도로 실행됩니다.

## <a name="examples"></a>예

### <a name="monitoring-memory-utilization"></a>메모리 사용률 모니터링

이 쿼리는 `oom_per_second` 지난 32분 동안 각 리소스 풀에 대한 메트릭을 계산합니다. 이 쿼리는 탄력적 풀의 모든 데이터베이스에서 실행할 수 있습니다.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>로그 공간 활용도 모니터링 `tempdb`

이 쿼리는 메트릭의 `tempdb_log_used_percent` 현재 값을 반환합니다. 이 쿼리는 탄력적 풀의 모든 데이터베이스에서 실행할 수 있습니다.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>다음 단계

* 탄력적 풀에 대한 소개는 [여러 Azure SQL 데이터베이스를 관리하고 확장하는 데 도움이 되는 탄력적 풀을](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)참조하십시오.
* 리소스 사용률을 줄이기 위해 쿼리 워크로드를 조정하는 방법에 대한 자세한 내용은 [모니터링 및 튜닝]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)및 [모니터링 및 성능 튜닝을](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)참조하십시오.
