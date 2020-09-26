---
title: DTU에서 vCore로 마이그레이션
description: Azure SQL Database의 데이터베이스를 DTU 모델에서 vCore 모델로 마이그레이션합니다. VCore로 마이그레이션하는 것은 표준 계층과 프리미엄 계층 간에 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: b8c7671e655594456621e4489cb06191d820b134
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333157"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU 기반 모델에서 vCore 기반 모델로 Azure SQL Database 마이그레이션
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 Azure SQL Database의 데이터베이스를 [DTU 기반 구매 모델](service-tiers-dtu.md) 에서 [vcore 기반 구매 모델로](service-tiers-vcore.md)마이그레이션하는 방법을 설명 합니다. 

## <a name="migrate-a-database"></a>데이터베이스 마이그레이션

DTU 기반 구매 모델에서 vCore 기반 구매 모델로 데이터베이스를 마이그레이션하는 것은 기본, 표준 및 프리미엄 서비스 계층의 서비스 목표 사이에서 크기를 조정 하는 것과 유사 합니다 .이는 마이그레이션 프로세스가 끝날 때와 비슷한 [기간](single-database-scale.md#latency) 및 [최소 가동 중지](scale-resources.md) 시간을 포함 합니다. VCore 기반 구매 모델로 마이그레이션되는 데이터베이스는 같은 방식으로 언제 든 지 DTU 기반 구매 모델로 다시 마이그레이션할 수 있습니다. 단, [Hyperscale](service-tier-hyperscale.md) service 계층으로 마이그레이션된 데이터베이스는 예외입니다. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>VCore 서비스 계층 및 서비스 목표 선택

대부분의 DTU-vCore 마이그레이션 시나리오에서 Basic 및 Standard 서비스 계층의 데이터베이스 및 탄력적 [풀은 범용 서비스 계층](service-tier-general-purpose.md) 에 매핑됩니다. Premium 서비스 계층의 데이터베이스 및 탄력적 풀은 [중요 비즈니스용](service-tier-business-critical.md) 서비스 계층에 매핑됩니다. 응용 프로그램 시나리오 및 요구 사항에 따라 모든 DTU 서비스 계층의 단일 데이터베이스에 대 한 마이그레이션 대상으로 [Hyperscale](service-tier-hyperscale.md) 서비스 계층을 사용할 수 있습니다.

VCore 모델에서 마이그레이션된 데이터베이스에 대해 서비스 목표 또는 계산 크기를 선택 하기 위해 간단 하지만 대략적인 규칙을 사용할 수 있습니다. 즉, 기본 또는 표준 계층의 모든 100 Dtu에는 하나 *이상의* vcore가 필요 하며 프리미엄 계층의 모든 125 dtu에는 하나 *이상의* vcore가 필요 합니다. 

> [!TIP]
> 이 규칙은 DTU 데이터베이스 또는 탄력적 풀에 사용 되는 하드웨어 생성을 고려 하지 않기 때문에 근사값입니다. 

DTU 모델에서는 사용 가능한 모든 [하드웨어 생성](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) 을 데이터베이스 또는 탄력적 풀에 사용할 수 있습니다. 또한 더 높거나 낮은 DTU 또는 eDTU 값을 선택 하 여 vCores (논리 Cpu) 수를 간접적으로 제어할 수 있습니다. 

VCore 모델을 사용 하는 경우 고객은 하드웨어 생성과 Vcore (논리 Cpu) 수를 모두 명시적으로 선택 해야 합니다. DTU 모델에서는 이러한 선택 항목을 제공 하지 않지만, 모든 데이터베이스 및 탄력적 풀에 사용 되는 논리 Cpu 수와 하드웨어 생성은 동적 관리 뷰를 통해 노출 됩니다. 이렇게 하면 일치 하는 vCore 서비스 목표를 보다 정확 하 게 확인할 수 있습니다. 

다음 방법에서는이 정보를 사용 하 여 vCore 모델로 마이그레이션한 후 비슷한 수준의 성능을 얻을 수 있도록 비슷한 리소스 할당을 통해 vCore 서비스 목표를 결정 합니다.

### <a name="dtu-to-vcore-mapping"></a>DTU-vCore 매핑

아래의 T-sql 쿼리는 마이그레이션할 DTU 데이터베이스의 컨텍스트에서 실행 될 때 Vcores 모델의 각 하드웨어 생성에 일치 하는 수의 vCores 수를 반환 합니다. 이 숫자를 Vcores 모델의 각 하드웨어 생성에서 [데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md) 에 사용할 수 있는 가장 가까운 vcores 수로 반올림 하 여 고객은 DTU 데이터베이스 또는 탄력적 풀에 가장 근접 하 게 일치 하는 vcores 서비스 목표를 선택할 수 있습니다. 

이 방법을 사용 하는 샘플 마이그레이션 시나리오는 [예제](#dtu-to-vcore-migration-examples) 섹션에 설명 되어 있습니다.

데이터베이스 대신 마이그레이션할 데이터베이스의 컨텍스트에서이 쿼리를 실행 `master` 합니다. 탄력적 풀을 마이그레이션할 때 풀에 있는 모든 데이터베이스의 컨텍스트에서 쿼리를 실행 합니다.

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>추가 요소

Vcores (논리 Cpu) 수와 하드웨어 생성 외에도 몇 가지 다른 요인이 Vcores 서비스 목표의 선택에 영향을 줄 수 있습니다.

- 매핑 T-sql 쿼리는 CPU 용량 측면에서 DTU 및 vCore 서비스 목적과 일치 하므로 CPU 바인딩된 작업에 대 한 결과는 더 정확 합니다.
- 동일한 하드웨어 생성과 동일한 수의 Vcores에 대해, Vcores 데이터베이스에 대 한 IOPS 및 트랜잭션 로그 처리량 리소스 제한은 DTU 데이터베이스 보다 높습니다. IO 바운드 워크 로드의 경우 동일한 수준의 성능을 얻기 위해 Vcores 모델에서 vCores 수를 낮출 수 있습니다. DTU 및 vCore 데이터베이스에 대 한 리소스 한도 (절대값)는 [dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 보기에서 제공 됩니다. 마이그레이션할 DTU 데이터베이스와 약 일치 하는 서비스 목표를 사용 하는 vCore 데이터베이스 간에 이러한 값을 비교 하면 vCore 서비스 목표를 보다 정확 하 게 선택할 수 있습니다.
- 또한 매핑 쿼리는 마이그레이션할 DTU 데이터베이스 또는 탄력적 풀에 대 한 코어 당 메모리 양과 vCore 모델의 각 하드웨어 생성을 반환 합니다. VCore로 마이그레이션한 후에도 전체 메모리를 더 많이 또는 더 높게 유지 하는 것은 충분 한 성능을 얻기 위해 큰 메모리 데이터 캐시가 필요한 작업 또는 쿼리 처리를 위한 큰 메모리 부여가 필요한 워크 로드에 중요 합니다. 이러한 워크 로드의 경우 실제 성능에 따라 충분 한 총 메모리를 얻기 위해 vCores 수를 늘려야 할 수 있습니다.
- VCore 서비스 목표를 선택할 때 DTU 데이터베이스의 [과거 리소스 사용률](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 을 고려해 야 합니다. 일관 되 게 사용 되는 CPU 리소스를 사용 하는 DTU 데이터베이스는 매핑 쿼리에서 반환 된 수보다 많은 vCores가 필요할 수 있습니다. 반대로, 지속적으로 높은 CPU 사용률이 있는 DTU 데이터베이스는 쿼리에 의해 반환 되는 것 보다 더 많은 vCores가 필요할 수 있습니다.
- 간헐적 이거나 예측할 수 없는 사용 패턴을 사용 하 여 데이터베이스를 마이그레이션하는 경우 [서버](serverless-tier-overview.md) 리스 계산 계층을 사용 하는 것이 좋습니다.  서버를 사용 하지 않는 동시 작업자 (요청)의 최대 수는 구성 된 동일한 수의 최대 vcore 수에 대해 프로 비전 된 계산의 한도를 75%입니다.  또한 서버 리스에서 사용할 수 있는 최대 메모리는 구성 된 최대 vcore 수의 3 GB입니다. 예를 들어 최대 메모리는 40 최대 vcore 구성 될 때 120 GB입니다.   
- VCore 모델에서 지원 되는 최대 데이터베이스 크기는 하드웨어 생성에 따라 다를 수 있습니다. 대량 데이터베이스의 경우 [단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)에 대 한 vcore 모델에서 지원 되는 최대 크기를 확인 합니다.
- 탄력적 풀의 경우 [DTU](resource-limits-dtu-elastic-pools.md) 및 [vcore](resource-limits-vcore-elastic-pools.md) 모델은 풀 당 지원 되는 최대 데이터베이스 수에 차이가 있습니다. 이는 여러 데이터베이스로 탄력적 풀을 마이그레이션할 때 고려해 야 합니다.
- 일부 하드웨어 생성은 모든 지역에서 사용 하지 못할 수 있습니다. [하드웨어 생성](service-tiers-vcore.md#hardware-generations)에서 가용성을 확인 합니다.

> [!IMPORTANT]
> 위의 DTU에서 vCore 크기 조정 지침을 참조 하 여 대상 데이터베이스 서비스 목표의 초기 추정에 도움을 제공할 수 있습니다.
>
> 대상 데이터베이스의 최적 구성은 워크 로드에 따라 달라 집니다. 따라서 마이그레이션 후 최적 가격/성능 비율을 달성 하려면 vCore 모델의 유연성을 활용 하 여 Vcore, [하드웨어 생성](service-tiers-vcore.md#hardware-generations), [서비스](service-tiers-vcore.md#service-tiers) 및 [계산](service-tiers-vcore.md#compute-tiers) 계층의 수를 조정 하 고, [최대 병렬 처리 수준과](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)같은 다른 데이터베이스 구성 매개 변수를 조정 해야 할 수 있습니다.
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU에서 vCore로의 마이그레이션 예

> [!NOTE]
> 아래 예의 값은 설명을 위한 목적 으로만 사용 됩니다. 설명 된 시나리오에서 반환 되는 실제 값은 다를 수 있습니다.
> 

**표준 S9 데이터베이스 마이그레이션**

매핑 쿼리는 다음 결과를 반환 합니다. 일부 열은 간단히 나타내기 위해 표시 되지 않습니다.

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|5세대|5.40|16.800|7|24.000|5.05|

DTU 데이터베이스에는 Vcores 당 5.4 GB의 메모리가 있는 24 개의 논리적 Cpu (vCores)가 있고 Gen5 하드웨어를 사용 하 고 있음을 알 수 있습니다. 이에 대 한 직접 일치는 Gen5 하드웨어의 범용 24 vCore 데이터베이스 ( **GP_Gen5_24** vcore 서비스 목표)입니다.

**표준 S0 데이터베이스 마이그레이션**

매핑 쿼리는 다음 결과를 반환 합니다. 일부 열은 간단히 나타내기 위해 표시 되지 않습니다.

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0.25|Gen4|0.42|0.250|7|0.425|5.05|

DTU 데이터베이스는 Vcores 당 0.42 GB의 메모리와 Gen4 하드웨어를 사용 하는 0.25 논리 Cpu (vCores)와 동등한 것을 확인할 수 있습니다. Gen4 및 Gen5 하드웨어 세대 **GP_Gen4_1** 및 **GP_Gen5_2**에서 가장 작은 Vcore 서비스 목표는 표준 S0 데이터베이스 보다 많은 계산 리소스를 제공 하므로 직접 일치 항목을 찾을 수 없습니다. Gen4 하드웨어가 [서비스 해제](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)되 고 있으므로 **GP_Gen5_2** 옵션이 선호 됩니다. 또한 워크 로드가 [서버](serverless-tier-overview.md) 를 사용 하지 않는 계산 계층에 적합 한 경우 **GP_S_Gen5_1** 는 보다 일치 합니다.

**Premium P15 데이터베이스 마이그레이션**

매핑 쿼리는 다음 결과를 반환 합니다. 일부 열은 간단히 나타내기 위해 표시 되지 않습니다.

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|5세대|4.86|29.400|7|42.000|5.05|

DTU 데이터베이스에는 Vcores 당 4.86 GB의 메모리와 Gen5 하드웨어를 사용 하는 42 개의 논리적 Cpu (vCores 있음)가 있습니다. 42 코어를 사용 하는 vCore 서비스 목표가 없지만 **BC_Gen5_40** 서비스 목표는 CPU와 메모리 용량 면에서 매우 가깝습니다 .이는 잘 일치 합니다.

**기본 200 eDTU 탄력적 풀 마이그레이션**

매핑 쿼리는 다음 결과를 반환 합니다. 일부 열은 간단히 나타내기 위해 표시 되지 않습니다.

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|5세대|5.40|2.800|7|4.000|5.05|

DTU 탄력적 풀에 4 개의 논리적 Cpu (Vcores)가 있고 Vcores 당 5.4 GB의 메모리가 있으며 Gen5 하드웨어를 사용 하는 것을 확인할 수 있습니다. VCore 모델의 직접 일치는 **GP_Gen5_4** 탄력적 풀입니다. 그러나이 서비스 목표는 풀 당 최대 200 데이터베이스를 지원 하지만 기본 200 eDTU 탄력적 풀은 최대 500 개의 데이터베이스를 지원 합니다. 마이그레이션할 탄력적 풀에 200 개 이상의 데이터베이스가 있는 경우 일치 하는 vCore 서비스 목표는 **GP_Gen5_6**해야 하며,이는 최대 500 개의 데이터베이스를 지원 합니다.

## <a name="migrate-geo-replicated-databases"></a>지리적으로 복제 된 데이터베이스 마이그레이션

DTU 기반 모델에서 vCore 기반 구매 모델로 마이그레이션하는 것은 standard 및 premium 서비스 계층에 있는 데이터베이스 간의 지역에서 복제 관계를 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다. 마이그레이션 중에 지역에서 복제를 중지할 필요는 없지만 다음 시퀀싱 규칙을 따라야 합니다.

- 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드해야 합니다.
- 다운그레이드하는 경우 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 보조 데이터베이스를 다운그레이드해야 합니다.

두 탄력적 풀 간의 지역에서 복제를 사용 하는 경우 하나의 풀을 기본 풀로 지정 하 고 다른 풀을 보조 데이터베이스로 지정 하는 것이 좋습니다. 이 경우 탄력적 풀을 마이그레이션하는 경우 동일한 시퀀싱 지침을 사용 해야 합니다. 그러나 주 데이터베이스와 보조 데이터베이스를 모두 포함 하는 탄력적 풀을 사용 하는 경우 사용률이 높은 풀을 주 데이터베이스로 처리 하 고 그에 따라 시퀀싱 규칙을 따릅니다.  

다음 표에서는 특정 마이그레이션 시나리오에 대 한 지침을 제공 합니다.

|현재 서비스 계층|대상 서비스 계층|마이그레이션 유형|사용자 작업|
|---|---|---|---|
|Standard|범용 가상 컴퓨터|수평|모든 순서로 마이그레이션할 수 있지만, 위에 설명 된 대로 적절 한 vCore 크기 조정을 보장 해야 합니다.|
|Premium|중요 비즈니스용|수평|모든 순서로 마이그레이션할 수 있지만, 위에 설명 된 대로 적절 한 vCore 크기 조정을 보장 해야 합니다.|
|Standard|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|Standard|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|Premium|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|Premium|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
||||

## <a name="migrate-failover-groups"></a>장애 조치 (failover) 그룹 마이그레이션

여러 데이터베이스가 있는 장애 조치 그룹을 마이그레이션하려면 주 데이터베이스와 보조 데이터베이스를 개별적으로 마이그레이션해야 합니다. 이 과정에서 동일한 고려 사항과 순서 지정 규칙이 적용됩니다. 데이터베이스를 vCore 기반 구매 모델로 변환한 후에는 장애 조치 (failover) 그룹이 동일한 정책 설정에 계속 적용 됩니다.

### <a name="create-a-geo-replication-secondary-database"></a>지역에서 복제 보조 데이터베이스 만들기

주 데이터베이스에 사용한 것과 동일한 서비스 계층을 사용 하 여 지역에서 복제 보조 데이터베이스 (지역 보조 데이터베이스)를 만들 수 있습니다. 로그 생성 비율이 높은 데이터베이스의 경우 주 데이터베이스와 동일한 계산 크기로 지역 보조 데이터베이스를 만드는 것이 좋습니다.

단일 주 데이터베이스에 대 한 탄력적 풀에서 지역 보조 데이터베이스를 만드는 경우 `maxVCore` 풀에 대 한 설정이 주 데이터베이스의 계산 크기와 일치 하는지 확인 합니다. 다른 탄력적 풀에서 주 데이터베이스에 대 한 지역 보조 데이터베이스를 만드는 경우 풀에 동일한 설정을 설정 하는 것이 좋습니다 `maxVCore` .

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>데이터베이스 복사본을 사용 하 여 DTU에서 vCore로 마이그레이션

대상 컴퓨팅 크기가 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 경우 제한 또는 특별한 순서 지정 없이 DTU 기반 컴퓨팅 크기의 데이터베이스를 vCore 기반 컴퓨팅 크기의 데이터베이스에 복사할 수 있습니다. 데이터베이스 복사본은 복사 작업을 시작할 때 데이터의 스냅숏을 만들고 원본과 대상 간의 데이터를 동기화 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](resource-limits-vcore-single-databases.md)을 참조 하세요.
- 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목에 대해서는 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](resource-limits-vcore-elastic-pools.md)을 참조 하세요.
