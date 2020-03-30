---
title: 구매 모델
description: Azure SQL Database에서 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255991"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>vCore 및 DTU 구매 모델 중에서 선택

Azure SQL Database를 사용하면 성능 및 비용 요구에 맞는 PaaS(서비스) 데이터베이스 엔진으로 완벽하게 관리되는 플랫폼을 쉽게 구입할 수 있습니다. Azure SQL Database에 대해 선택한 배포 모델에 따라 적합한 구매 모델을 선택할 수 있습니다.

- [가상 코어(vCore) 기반 구매](sql-database-service-tiers-vcore.md) 모델(권장). 이 구매 모델은 프로비저닝된 계산 계층과 서버리스 계산 계층 중에서 선택할 수 있습니다. 프로비저닝된 계산 계층을 사용하면 워크로드에 대해 항상 프로비전되는 정확한 계산 리소스 양을 선택할 수 있습니다. 서버리스 계산 계층을 사용하면 구성 가능한 계산 범위에 대해 계산 리소스의 자동 크기 조정을 지정합니다. 이 계산 계층을 사용하면 워크로드 활동에 따라 데이터베이스를 자동으로 일시 중지하고 다시 시작할 수도 있습니다. 시간 단위당 vCore 단위 가격은 프로비저닝된 계산 계층에서 서버리스 계산 계층보다 낮습니다.
- [데이터베이스 트랜잭션 단위 (DTU) 기반 구매 모델.](sql-database-service-tiers-dtu.md) 이 구매 모델은 공통 워크로드에 적합한 번들 컴퓨팅 및 스토리지 패키지를 제공합니다.

다른 Azure SQL Database 배포 모델에 대해 다양한 구매 모델을 사용할 수 있습니다.

- [Azure SQL Database](sql-database-technical-overview.md)의 [단일 데이터베이스](sql-database-single-databases-manage.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 배포 옵션은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 모두 제공합니다.
- Azure SQL Database에서 [관리되는 인스턴스](sql-database-managed-instance.md) 배포 옵션은 [vCore 기반 구매 모델만](sql-database-service-tiers-vcore.md)제공합니다.
- [하이퍼스케일 서비스 계층은](sql-database-service-tier-hyperscale.md) [vCore 기반 구매 모델을](sql-database-service-tiers-vcore.md)사용하는 단일 데이터베이스에 사용할 수 있습니다.

다음 표와 차트는 vCore 기반 및 DTU 기반 구매 모델을 비교하고 대조합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|이 모델은 계산, 저장소 및 I/O 리소스의 번들측측을 기반으로 합니다. 계산 크기는 단일 데이터베이스에 대한 DTUS와 탄력적 풀의 탄력적 데이터베이스 트랜잭션 단위(eDT)로 표현됩니다. DTUS 및 eDTUS에 대한 자세한 내용은 [D투스 및 eDTUS를 참조하십시오.](sql-database-purchase-models.md#dtu-based-purchasing-model)|미리 구성된 간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.|
|vCore 기반 모델|이 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델 비교](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>컴퓨팅 비용

### <a name="provisioned-compute-costs"></a>프로비저닝된 계산 비용

프로비저닝된 계산 계층에서 계산 비용은 응용 프로그램에 대해 프로비전되는 총 계산 용량을 반영합니다.

중요 비즈니스용 서비스 계층에서는 3개 이상의 복제본이 자동으로 할당됩니다. 이러한 추가 컴퓨팅 리소스 할당을 반영하기 위해 vCore 기반 구매 모델의 가격은 비즈니스 중요 서비스 계층에서 범용 서비스 계층보다 약 2.7배 높습니다. 마찬가지로 비즈니스 크리티컬 서비스 계층의 GB당 높은 스토리지 가격은 SSD 스토리지의 IO 제한 과 낮은 대기 시간을 반영합니다.

두 계층 모두 백업에 표준 저장소를 사용하기 때문에 백업 저장소 비용은 비즈니스 중요 서비스 계층 및 범용 서비스 계층에 대해 동일합니다.

### <a name="serverless-compute-costs"></a>서버리스 컴퓨팅 비용

계산 용량을 정의하고 서버리스 계산 계층에 대한 비용을 계산하는 방법에 대한 설명은 [SQL Database 서버리스](sql-database-serverless.md)를 참조하십시오.

## <a name="storage-costs"></a>스토리지 비용

다양한 유형의 스토리지에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로비저닝된 저장소에 대한 요금이 부과됩니다. 최대값을 줄이거나 늘리지 않으면 비용이 변경되지 않습니다. 백업 스토리지는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘리면 인스턴스에서 사용하는 백업 저장소가 증가합니다.

기본적으로 데이터베이스의 7일 간의 자동화된 백업은 읽기 액세스 지리적 중복 저장소(RA-GRS) 표준 Blob 저장소 계정에 복사됩니다. 이 저장소는 매주 전체 백업, 일일 차동 백업 및 트랜잭션 로그 백업에서 사용되며 5분마다 복사됩니다. 트랜잭션 로그의 크기는 데이터베이스 의 변경 속도에 따라 달라집니다. 데이터베이스 크기의 100%에 해당하는 최소 저장소 용량은 추가 비용 없이 제공됩니다. 백업 스토리지의 추가 사용량은 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요.

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

가상 코어(vCore)는 논리적 CPU를 나타내며 하드웨어 세대와 하드웨어의 물리적 특성(예: 코어 수, 메모리 및 저장소 크기) 중에서 선택할 수 있는 옵션을 제공합니다. vCore 기반 구매 모델은 개별 리소스 소비의 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 변환하는 간단한 방법을 제공합니다. 이 모델을 사용하면 워크로드 요구 사항에 따라 계산, 메모리 및 저장소 리소스를 선택할 수 있습니다.

vCore 기반 구매 모델에서 [단일 데이터베이스,](sql-database-single-database-scale.md)탄력적 풀 및 [관리되는 인스턴스에](sql-database-managed-instance.md)대한 [범용](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 및 [비즈니스 중요](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 서비스 계층 중에서 선택할 수 [있습니다.](sql-database-elastic-pool.md) 단일 데이터베이스의 경우 [하이퍼스케일 서비스 계층을](sql-database-service-tier-hyperscale.md)선택할 수도 있습니다.

vCore 기반 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택하고 온-프레미스 성능과 일치하며 가격을 최적화할 수 있습니다. vCore 기반 구매 모델에서 다음 비용을 지불합니다.

- 계산 리소스(서비스 계층 + vCore 수 및 메모리 양 + 하드웨어 생성).
- 데이터 및 로그 저장소의 유형 및 양입니다.
- 백업 저장소(RA-GRS).

> [!IMPORTANT]
> 컴퓨팅 리소스, I/O 및 데이터 및 로그 저장소는 데이터베이스 또는 탄력적 풀당 요금이 부과됩니다. 백업 저장소는 각 데이터베이스당 요금이 부과됩니다. 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요.
> **지역 제한 사항:** 지원되는 지역의 현재 목록은 [지역별로 사용할 수 있는 제품을](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)참조하십시오. 현재 지원되지 않는 리전에서 관리되는 인스턴스를 만들려면 [Azure 포털을 통해 지원 요청을 보냅니다.](quota-increase-request.md)

단일 데이터베이스 또는 탄력적 풀에서 300개 이상의 DTO를 사용하는 경우 vCore 기반 구매 모델로 변환하면 비용이 절감될 수 있습니다. 선택한 API를 사용하거나 가동 중지 시간 없이 Azure 포털을 사용하여 변환할 수 있습니다. 그러나 전환은 필요하지 않으며 자동으로 수행되지는 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다.

DTU 기반 구매 모델에서 vCore 기반 구매 모델로 변환하려면 다음 의 엄지 손가락 규칙을 사용하여 계산 크기를 선택합니다.

- 표준 계층의 DT 100개마다 범용 서비스 계층에서 1vCore 이상이 필요합니다.
- 프리미엄 계층의 125DT마다 비즈니스 크리티컬 서비스 계층에서 1vCore 이상이 필요합니다.

> [!NOTE]
> VCore 크기 조정 지침에 대한 DTU는 대략적인 것이며 대상 데이터베이스 서비스 목표의 초기 추정에 도움이 됩니다. 대상 데이터베이스의 최적 구성은 워크로드에 따라 다릅니다. 
> 
> 최적의 가격/성능 비율을 달성하려면 vCore 모델의 유연성을 활용하여 vCore 수, [하드웨어 생성,](sql-database-service-tiers-vcore.md#hardware-generations) [서비스](sql-database-service-tiers-vcore.md#service-tiers) 및 [컴퓨팅](sql-database-service-tiers-vcore.md#compute-tiers) 계층을 조정하고 최대 병렬 처리 정도와 같은 다른 데이터베이스 구성 매개 [변수를](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)조정해야 할 수 있습니다.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

데이터베이스 트랜잭션 단위(DTU)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 컴퓨팅 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성된 번들 및 고정 결제의 단순성 이상을 선호하는 경우 DTU 기반 모델이 사용자의 요구에 더 적합할 수 있습니다.

DTU 기반 구매 모델에서 [단일 데이터베이스](sql-database-single-database-scale.md) 및 [탄력적 풀모두에](sql-database-elastic-pool.md)대한 기본, 표준 및 프리미엄 서비스 계층 중에서 선택할 수 있습니다. [관리되는 인스턴스에서](sql-database-managed-instance.md)DTU 기반 구매 모델을 사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](sql-database-single-database-scale.md)내의 특정 계산 크기의 단일 데이터베이스의 경우 Microsoft는 해당 데이터베이스에 대한 특정 수준의 리소스를 보장합니다(Azure 클라우드의 다른 데이터베이스와 는 별개). 이 보증은 예측 가능한 수준의 성능을 제공합니다. 데이터베이스에 할당된 리소스의 양은 여러 DTUS로 계산되며 계산, 저장소 및 I/O 리소스의 번들 측정값입니다.

이러한 리소스 간의 비율은 원래 실제 OLTP 워크로드의 일반적인 것으로 설계된 [OLTP(온라인 트랜잭션 처리) 벤치마크 워크로드에](sql-database-benchmark-overview.md) 의해 결정됩니다. 워크로드가 이러한 리소스의 양을 초과하면 처리량이 제한되어 성능과 시간 제한이 느려집니다.

워크로드에서 사용하는 리소스는 Azure 클라우드의 다른 SQL 데이터베이스에서 사용할 수 있는 리소스에 영향을 주지 않습니다. 마찬가지로 다른 워크로드에서 사용하는 리소스는 SQL 데이터베이스에서 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUS는 서로 다른 계산 크기 및 서비스 계층에서 Azure SQL 데이터베이스에 할당된 상대 리소스를 이해하는 데 가장 유용합니다. 예를 들어:

- 데이터베이스의 계산 크기를 늘려 DTUS를 두 배로 늘리면 해당 데이터베이스에서 사용할 수 있는 리소스 집합이 두 배로 늘어남에 따라
- 1750DTU가 있는 프리미엄 서비스 계층 P11 데이터베이스는 5DTU가 있는 기본 서비스 계층 데이터베이스보다 350배 더 많은 DTU 컴퓨팅 성능을 제공합니다.  

워크로드의 리소스(DTU) 소비에 대한 심층적 인 통찰력을 얻으려면 [쿼리 성능 통찰력을](sql-database-query-performance.md) 사용하여 다음을 수행하십시오.

- 성능 향상을 위해 잠재적으로 조정할 수 있는 CPU/기간/실행 수별로 상위 쿼리를 식별합니다. 예를 들어 I/O 집약적인 쿼리는 특정 서비스 계층에서 사용 가능한 메모리를 더 잘 사용하고 크기를 계산하기 위해 메모리 내 [최적화 기술의](sql-database-in-memory.md) 이점을 활용할 수 있습니다.
- 쿼리의 세부 정보를 드릴다운하여 해당 텍스트및 리소스 사용 기록을 봅니다.
- [SQL Database Advisor에서](sql-database-advisor.md)수행한 작업을 보여 주는 성능 조정 권장 사항에 액세스합니다.

### <a name="elastic-database-transaction-units-edtus"></a>탄력적 데이터베이스 트랜잭션 단위(eDT)

항상 사용할 수 있는 SQL 데이터베이스의 경우 항상 필요하지 않을 수도 있는 전용 리소스 집합(DT)을 제공하는 대신 이러한 데이터베이스를 [탄력적 풀에](sql-database-elastic-pool.md)배치할 수 있습니다. 탄력적 풀의 데이터베이스는 단일 Azure SQL Database 서버에 있으며 리소스 풀을 공유합니다.

탄력적 풀의 공유 리소스는 탄력적 데이터베이스 트랜잭션 단위(eDTUs)로 측정됩니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 가진 여러 데이터베이스의 성능 목표를 관리하는 간단하고 비용 효율적인 솔루션을 제공합니다. 탄력적 풀은 풀의 한 데이터베이스에서 모든 리소스를 사용할 수 없도록 보장하는 동시에 풀의 각 데이터베이스에 항상 사용 가능한 최소한의 리소스를 갖도록 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에서 개별 데이터베이스는 구성된 경계 내에서 자동 크기 조정을 할 수 있습니다. 부하가 많은 데이터베이스는 수요를 충족하기 위해 더 많은 eDT를 소비합니다. 부하가 더 적은 데이터베이스는 eDT를 더 적게 소비합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 리소스는 데이터베이스가 아닌 전체 풀에 프로비전되므로 탄력적 풀은 관리 작업을 단순화하고 풀에 대한 예측 가능한 예산을 제공합니다.

데이터베이스 가동 중지 시간이 없고 풀의 데이터베이스에 영향을 미치지 않고 기존 풀에 eDT를 추가할 수 있습니다. 마찬가지로 추가 eDT가 더 이상 필요하지 않은 경우 언제든지 기존 풀에서 제거합니다. 언제든지 풀에 데이터베이스를 추가하거나 풀에서 빼을 수 있습니다. 다른 데이터베이스에 대한 eDTUs를 예약하려면 데이터베이스가 과부하로 사용할 수 있는 eDT의 수를 제한합니다. 데이터베이스가 리소스를 지속적으로 과소 평가하는 경우 풀 밖으로 이동하여 예측 가능한 양의 필요한 리소스가 있는 단일 데이터베이스로 구성합니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 시스템 워크로드를 Azure SQL Database로 마이그레이션하려면 [DTU 계산기를](https://dtucalculator.azurewebsites.net/) 사용하여 필요한 DTU 수를 근사화합니다. 기존 Azure SQL Database 워크로드의 경우 [쿼리 성능 통찰력을](sql-database-query-performance.md) 사용하여 DT(데이터베이스 리소스 소비)를 이해하고 워크로드를 최적화하기 위한 심층적인 통찰력을 얻을 수 있습니다. [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 동적 관리 보기(DMV)를 사용하면 지난 1시간 동안의 리소스 사용량을 볼 수 있습니다. [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 카탈로그 보기는 지난 14일 동안의 리소스 소비를 표시하지만 평균 5분의 낮은 충실도를 표시합니다.

### <a name="determine-dtu-utilization"></a>DTU 사용률 결정

데이터베이스 또는 탄력적 풀의 DTU/eDTU 한계를 기준으로 DTU/eDTU 사용률의 평균 백분율을 확인하려면 다음 수식을 사용하십시오.

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

이 수식의 입력 값은 [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)및 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV에서 얻을 수 있습니다. 즉, 데이터베이스 또는 탄력적 풀의 DTU/eDTU 한계에 대한 DTU/eDTU 사용률의 백분율을 확인하려면 `avg_cpu_percent`다음에서 `avg_data_io_percent` `avg_log_write_percent` 가장 큰 백분율 값을 선택합니다.

> [!NOTE]
> 데이터베이스의 DTU 제한은 데이터베이스에서 사용할 수 있는 CPU, 읽기, 쓰기 및 메모리에 의해 결정됩니다. 그러나 SQL Server 데이터베이스 엔진은 일반적으로 성능을 향상시키기 위해 데이터 캐시에 사용 가능한 모든 메모리를 사용하므로 현재 데이터베이스 로드에 관계없이 `avg_memory_usage_percent` 일반적으로 값이 100%에 가깝습니다. 따라서 메모리가 DTU 제한에 간접적으로 영향을 미치더라도 DTU 사용률 수식에는 사용되지 않습니다.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 리소스 사용률이 낮고 사용률이 상대적으로 낮은 데이터베이스에 적합합니다. 자세한 내용은 [SQL Database 탄력적 풀을 고려해야 하는 시기를 참조하십시오.](sql-database-elastic-pool.md)

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델의 하드웨어 세대

DTU 기반 구매 모델에서 고객은 데이터베이스에 사용되는 하드웨어 생성을 선택할 수 없습니다. 지정된 데이터베이스는 일반적으로 특정 하드웨어 생성에 오랜 시간 동안 유지되지만(일반적으로 여러 달동안) 데이터베이스를 다른 하드웨어 생성으로 이동시킬 수 있는 특정 이벤트가 있습니다.

예를 들어 데이터베이스를 다른 서비스 목표로 확장 또는 축소하거나 데이터 센터의 현재 인프라가 용량 제한에 가까워지거나 현재 사용 중인 하드웨어가 있는 경우 데이터베이스를 다른 하드웨어 생성으로 이동할 수 있습니다. 수명이 다한 것으로 인해 폐기되었습니다.

데이터베이스를 다른 하드웨어로 이동하면 워크로드 성능이 변경될 수 있습니다. DTU 모델은 서비스 목표(DTU 수)가 동일하게 유지되는 한 데이터베이스가 다른 하드웨어 생성으로 이동할 때 [DTU 벤치마크](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) 워크로드의 처리량및 응답 시간이 거의 동일하게 유지되도록 보장합니다. 

그러나 Azure SQL Database에서 실행되는 광범위한 고객 워크로드에서 동일한 서비스 목표에 대해 서로 다른 하드웨어를 사용하는 것이 미치는 영향을 더 두드러질 수 있습니다. 워크로드마다 하드웨어 구성 및 기능이 다릅니다. 따라서 DTU 벤치마크 이외의 워크로드의 경우 데이터베이스가 한 하드웨어 세대에서 다른 하드웨어 세대로 이동하는 경우 성능 차이를 볼 수 있습니다.

예를 들어 네트워크 대기 시간에 민감한 응용 프로그램은 Gen5에서 가속 네트워킹을 사용하므로 Gen5 하드웨어와 Gen4에서 더 나은 성능을 볼 수 있지만 집중읽기 IO를 사용하는 응용 프로그램은 Gen4 하드웨어와 Gen5에서 더 나은 성능을 볼 수 있습니다. Gen4의 코어 비율당 메모리가 더 높습니다.

하드웨어 변경에 민감한 워크로드가 있거나 데이터베이스에 대한 하드웨어 생성 선택을 제어하려는 고객은 [vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) 모델을 사용하여 데이터베이스 생성 및 크기 조정 중에 선호하는 하드웨어 생성을 선택할 수 있습니다. vCore 모델에서는 [단일 데이터베이스와](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) 탄력적 풀 모두에 대해 각 하드웨어 생성에 대한 각 서비스 목표의 리소스 제한이 [문서화됩니다.](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) vCore 모델의 하드웨어 세대에 대한 자세한 내용은 [하드웨어 세대를](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)참조하십시오.

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 변환하려면 응용 프로그램을 오프라인으로 전환해야 합니까?

아니요. 응용 프로그램을 오프라인으로 사용할 필요가 없습니다. 새로운 서비스 계층은 데이터베이스를 표준에서 프리미엄 서비스 계층으로 업그레이드하는 기존 프로세스와 유사한 간단한 온라인 변환 방법을 제공합니다. Azure 포털, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 이 변환을 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리를](sql-database-elastic-pool.md)참조하십시오.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>vCore 기반 구매 모델의 서비스 계층에서 DTU 기반 구매 모델의 서비스 계층으로 데이터베이스를 변환할 수 있습니까?

예. Azure 포털, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 데이터베이스를 지원되는 성능 목표로 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리를](sql-database-elastic-pool.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- vCore 기반 구매 모델에 대한 자세한 내용은 [vCore 기반 구매 모델을](sql-database-service-tiers-vcore.md)참조하십시오.
- DTU 기반 구매 모델에 대한 자세한 내용은 [DTU 기반 구매 모델을](sql-database-service-tiers-dtu.md)참조하십시오.
