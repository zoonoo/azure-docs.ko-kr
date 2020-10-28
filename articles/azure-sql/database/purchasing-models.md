---
title: 구매 모델
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에서 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788828"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>VCore 및 DTU 구매 모델 중에서 선택-Azure SQL Database 및 SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance를 사용 하 여 성능 및 비용 요구에 맞는 완전히 관리 되는 PaaS (platform as a service) 데이터베이스 엔진을 쉽게 구매할 수 있습니다. Azure SQL Database 위해 선택한 배포 모델에 따라 사용자에 게 적합 한 구매 모델을 선택할 수 있습니다.

- [Vcore (가상 코어) 기반 구매 모델](service-tiers-vcore.md) (권장) 이 구매 모델은 프로 비전 된 계산 계층과 서버를 사용 하지 않는 계산 계층 중에서 선택할 것을 제공 합니다. 프로 비전 된 계산 계층을 사용 하 여 워크 로드에 대해 항상 프로 비전 되는 정확한 계산 리소스 양을 선택 합니다. 서버를 사용 하지 않는 계산 계층에서는 구성 가능한 계산 범위에 대해 계산 리소스의 자동 크기 조정을 지정 합니다. 이 계산 계층을 사용 하 여 워크 로드 활동을 기반으로 데이터베이스를 자동으로 일시 중지 하 고 다시 시작할 수도 있습니다. 프로 비전 된 계산 계층의 시간 단위당 vCore 단가가 서버를 사용 하지 않는 계산 계층에 있는 것 보다 낮습니다.
- [DTU (데이터베이스 트랜잭션 단위) 기반 구매 모델](service-tiers-dtu.md)입니다. 이 구매 모델은 공통 작업에 대해 분산 된 번들 계산 및 저장소 패키지를 제공 합니다.

구매 모델에는 두 가지가 있습니다.

- [Vcore 기반 구매 모델](service-tiers-vcore.md) 은 [AZURE SQL DATABASE](sql-database-paas-overview.md) 및 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)에 모두 사용할 수 있습니다. 하이퍼 [확장 서비스 계층](service-tier-hyperscale.md) 은 [vcore 기반 구매 모델](service-tiers-vcore.md)을 사용 하는 단일 데이터베이스에 사용할 수 있습니다.
- [DTU 기반 구매 모델](service-tiers-dtu.md) 은 [Azure SQL Database](single-database-manage.md)사용할 수 있습니다.

다음 표와 차트는 vCore 기반 및 DTU 기반 구매 모델을 비교 하 고 대조 합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반|이 모델은 계산, 저장소 및 i/o 리소스를 함께 제공 하는 측정값을 기반으로 합니다. 컴퓨팅 크기는 단일 데이터베이스에 대해서는 DTU, 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표시됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](purchasing-models.md#dtu-based-purchasing-model)을 참조하세요.|간단 하 고 미리 구성 된 리소스 옵션을 원하는 고객|
|vCore 기반|이 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 SQL Server용 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 나타내는 고객|
||||  

![가격 책정 모델 비교](./media/purchasing-models/pricing-model.png)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>컴퓨팅 비용

### <a name="provisioned-compute-costs"></a>프로 비전 된 계산 비용

프로 비전 된 계산 계층에서 계산 비용은 응용 프로그램에 대해 프로 비전 된 총 계산 용량을 반영 합니다.

중요 비즈니스용 서비스 계층에서는 세 개 이상의 복제본을 자동으로 할당 합니다. 이러한 추가 계산 리소스 할당을 반영 하기 위해 vCore 기반 구매 모델의 가격은 범용 서비스 계층 보다 중요 비즈니스용 서비스 계층에서 약 2.7 배 더 높습니다. 마찬가지로 중요 비즈니스용 서비스 계층에서 GB 당 더 높은 저장소 가격은 SSD 저장소의 더 높은 IO 제한과 낮은 대기 시간을 반영 합니다.

두 계층 모두 백업에 표준 저장소를 사용 하므로 백업 저장소 비용은 중요 비즈니스용 서비스 계층 및 범용 서비스 계층에 대해 동일 합니다.

### <a name="serverless-compute-costs"></a>서버를 사용 하지 않는 계산 비용

서버를 사용 하지 않는 계산 계층에 대해 계산 용량이 정의 되 고 비용이 계산 되는 방법에 대 한 설명은 서버를 사용 하지 않는 [계층 SQL Database](serverless-tier-overview.md)을 참조 하세요.

## <a name="storage-costs"></a>스토리지 비용

다양한 유형의 스토리지에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로 비전 된 저장소에 대 한 요금이 청구 됩니다. 최대값을 줄이거나 늘리지 않으면 비용이 변경 되지 않습니다. 백업 스토리지는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘려 인스턴스에서 사용 되는 백업 저장소를 늘립니다.

기본적으로 데이터베이스의 자동화 된 백업 7 일은 읽기 액세스 지역 중복 저장소 (RA-GRS) 표준 Blob 저장소 계정에 복사 됩니다. 이 저장소는 매주 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에 사용 됩니다. 트랜잭션 로그의 크기는 데이터베이스의 변경 률에 따라 달라 집니다. 데이터베이스 크기의 100%와 같은 최소 저장소 크기는 추가 비용 없이 제공 됩니다. 백업 스토리지의 추가 사용량은 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요.

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

VCore (가상 코어)는 논리적 CPU를 나타내며 하드웨어 세대와 하드웨어의 물리적 특성 (예: 코어 수, 메모리 및 저장소 크기) 중에서 선택할 수 있는 옵션을 제공 합니다. VCore 기반 구매 모델은 개별 리소스 소비의 유연성, 제어, 투명성 및 온-프레미스 워크 로드 요구 사항을 클라우드로 변환 하는 간단한 방법을 제공 합니다. 이 모델을 사용 하면 워크 로드 요구 사항에 따라 계산, 메모리 및 저장소 리소스를 선택할 수 있습니다.

VCore 기반 구매 모델에서는 SQL Database 및 SQL Managed Instance의 범용 및 [중요 비즈니스용](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 서비스 계층 [중에서 선택할](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 수 있습니다.  단일 데이터베이스의 경우에는 [Hyperscale 서비스 계층](service-tier-hyperscale.md)을 선택할 수도 있습니다.

VCore 기반 구매 모델을 사용 하 여 독립적으로 계산 및 저장소 리소스를 선택 하 고, 온-프레미스 성능을 일치 시키고, 가격을 최적화할 수 있습니다. VCore 기반 구매 모델에서 다음에 대 한 비용을 지불 합니다.

- 컴퓨팅 리소스(서비스 계층 + vCore 수 및 메모리 크기 + 하드웨어 세대)
- 데이터 및 로그 스토리지의 형식 및 크기
- 백업 저장소 (RA-GRS).

> [!IMPORTANT]
> 계산 리소스, i/o, 데이터 및 로그 저장소는 데이터베이스 또는 탄력적 풀 별로 요금이 청구 됩니다. 백업 저장소는 각 데이터베이스당 청구 됩니다. SQL Managed Instance 요금에 대 한 자세한 내용은 [sql Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)를 참조 하세요.
> **지역 제한 사항:** 지원 되는 지역에 대 한 현재 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)을 참조 하세요. 현재 지원 되지 않는 지역에서 관리 되는 인스턴스를 만들려면 [Azure Portal를 통해 지원 요청을 보냅니다](quota-increase-request.md).

데이터베이스가 300 개 보다 많은 Dtu를 사용 하는 경우 vCore 기반 구매 모델로 변환 하면 비용이 줄어들 수 있습니다. 원하는 API를 사용 하거나 가동 중지 시간 없이 Azure Portal를 사용 하 여 변환할 수 있습니다. 그러나 변환은 필요 하지 않으며 자동으로 수행 되지 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다.

DTU 기반 구매 모델에서 vCore 기반 구매 모델로 변환 하려면 [dtu에서 vCore로 마이그레이션](migrate-dtu-to-vcore.md)을 참조 하세요.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU (데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 컴퓨팅 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성 된 번들 및 고정 상환의 단순성을 선호 하는 경우 DTU 기반 모델이 사용자의 요구에 더 적합할 수 있습니다.

DTU 기반 구매 모델에서 Azure SQL Database에 대 한 기본, 표준 및 프리미엄 서비스 계층 중에서 선택할 수 있습니다. DTU 기반 구매 모델은 Azure SQL Managed Instance에서 사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](single-database-scale.md)내의 특정 계산 크기에 있는 단일 데이터베이스의 경우 azure는 해당 데이터베이스에 대 한 특정 수준의 리소스 (azure 클라우드의 다른 데이터베이스와 무관)를 보장 합니다. 이러한 보장은 예측 가능한 수준의 성능을 제공 합니다. 데이터베이스에 할당 되는 리소스의 양은 Dtu 수로 계산 되며 계산, 저장소 및 i/o 리소스의 번들로 측정 됩니다.

이러한 리소스 간의 비율은 원래 실제 OLTP 워크 로드의 일반적인 [oltp (온라인 트랜잭션 처리) 벤치 마크 워크 로드](service-tiers-dtu.md) 에 의해 결정 됩니다. 작업이 이러한 리소스의 용량을 초과 하면 처리량이 제한 되어 성능이 저하 되 고 제한 시간이 초과 됩니다.

워크 로드에서 사용 하는 리소스는 Azure 클라우드의 다른 데이터베이스에서 사용할 수 있는 리소스에 영향을 주지 않습니다. 마찬가지로 다른 작업에서 사용 하는 리소스는 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/purchasing-models/bounding-box.png)

Dtu는 다양 한 계산 크기 및 서비스 계층에서 데이터베이스에 할당 되는 상대 리소스를 이해 하는 데 가장 유용 합니다. 예를 들면 다음과 같습니다.

- 데이터베이스의 계산 크기를 늘려서 Dtu를 두 배로 늘리면 해당 데이터베이스에서 사용할 수 있는 리소스 집합이 배가 됩니다.
- 1750 Dtu를 포함 하는 프리미엄 서비스 계층 P11 데이터베이스는 5 개의 Dtu를 포함 하는 기본 서비스 계층 데이터베이스 보다 350 배 더 많은 DTU 계산 기능을 제공 합니다.  

워크 로드의 리소스 (DTU) 소비량에 대 한 자세한 정보를 얻으려면 [쿼리 성능 정보](query-performance-insight-use.md) 를 사용 하 여 다음을 수행 합니다.

- 성능 향상을 위해 잠재적으로 튜닝할 수 있는 CPU/기간/실행 수 별 상위 쿼리를 식별 합니다. 예를 들어 i/o를 많이 사용 하는 쿼리는 [메모리 내 최적화 기술을](../in-memory-oltp-overview.md) 사용 하 여 특정 서비스 계층 및 계산 크기에서 사용 가능한 메모리를 보다 효율적으로 활용할 수 있습니다.
- 쿼리의 세부 정보로 드릴 다운 하 여 해당 텍스트와 리소스 사용 기록을 볼 수 있습니다.
- [SQL Database Advisor](database-advisor-implement-performance-recommendations.md)에서 수행 하는 작업을 표시 하는 성능 튜닝 권장 구성에 액세스 합니다.

### <a name="elastic-database-transaction-units-edtus"></a>Edtu (탄력적 데이터베이스 트랜잭션 단위)

항상 사용할 수 있는 데이터베이스의 경우 항상 필요 하지 않을 수 있는 전용 리소스 (Dtu) 집합을 제공 하는 대신 이러한 데이터베이스를 [탄력적 풀](elastic-pool-overview.md)에 넣을 수 있습니다. 탄력적 풀의 데이터베이스는 단일 서버에 있으며 리소스 풀을 공유 합니다.

탄력적 풀의 공유 리소스는 Edtu (탄력적 데이터베이스 트랜잭션 단위)로 측정 됩니다. 탄력적 풀은 매우 다양 하 고 예측할 수 없는 사용 패턴을 가진 여러 데이터베이스의 성능 목표를 관리 하기 위한 간단 하 고 비용 효율적인 솔루션을 제공 합니다. 탄력적 풀은 풀의 데이터베이스 하나에서 모든 리소스를 사용할 수 없도록 보장 하는 반면 풀의 각 데이터베이스에는 항상 필요한 최소한의 리소스를 사용할 수 있도록 보장 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에서 개별 데이터베이스는 구성 된 경계 내에서 자동으로 크기를 조정할 수 있습니다. 과도 한 로드에서 데이터베이스는 수요를 충족 하기 위해 더 많은 Edtu를 사용 합니다. 낮은 부하의 데이터베이스는 더 적은 Edtu를 사용 합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 리소스는 데이터베이스 단위가 아니라 전체 풀에 대해 프로 비전 되므로 탄력적 풀은 관리 작업을 단순화 하 고 풀에 대해 예측 가능한 예산을 제공 합니다.

데이터베이스 가동 중지 시간 없이 풀에 있는 데이터베이스에 영향을 주지 않고 기존 풀에 Edtu를 더 추가할 수 있습니다. 마찬가지로 더 이상 추가 Edtu 필요 하지 않은 경우 언제 든 지 기존 풀에서 제거 합니다. 또한 언제 든 지 풀에서 데이터베이스를 추가 하거나 뺄 수 있습니다. 다른 데이터베이스에 대 한 Edtu을 예약 하려면 데이터베이스가 높은 부하 상태에서 사용할 수 있는 Edtu 수를 제한 합니다. 데이터베이스에서 일관 되 게 리소스를 사용 하는 경우에는 풀에서 데이터를 이동 하 여 예상 되는 양의 필요한 리소스로 단일 데이터베이스로 구성 합니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 머신 워크 로드를 SQL Database 마이그레이션하려면 [dtu 계산기](https://dtucalculator.azurewebsites.net/) 를 사용 하 여 필요한 dtu의 수를 대략적으로 계산 합니다. 기존 SQL Database 워크 로드의 경우 [쿼리 성능 정보](query-performance-insight-use.md) 를 사용 하 여 데이터베이스 리소스 사용 (dtu)을 이해 하 고 워크 로드 최적화에 대 한 심층적인 통찰력을 얻습니다. [SYS.DM_DB_RESOURCE_STATS](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV (동적 관리 뷰)를 사용 하 여 지난 1 시간의 리소스 소비량을 볼 수 있습니다. [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 카탈로그 뷰는 지난 14 일간의 리소스 사용을 표시 하지만 5 분 분량의 평균을 사용 합니다.

### <a name="determine-dtu-utilization"></a>DTU 사용률 확인

데이터베이스 또는 탄력적 풀의 DTU/eDTU 한도를 기준으로 DTU/eDTU 사용률의 평균 백분율을 확인 하려면 다음 수식을 사용 합니다.

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

이 수식에 대 한 입력 값은 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)및 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) dmv에서 가져올 수 있습니다. 즉, 데이터베이스 또는 탄력적 풀의 DTU/edtu 제한에 대 한 DTU/eDTU 사용률의 비율을 확인 하려면 `avg_cpu_percent` , `avg_data_io_percent` 및 `avg_log_write_percent` 지정 된 시간에서 가장 큰 비율 값을 선택 합니다.

> [!NOTE]
> 데이터베이스의 DTU 한도는 데이터베이스에 사용할 수 있는 CPU, 읽기, 쓰기 및 메모리에 따라 결정 됩니다. 그러나 SQL Database 엔진은 일반적으로 데이터 캐시에 사용할 수 있는 모든 메모리를 사용 하 여 성능을 향상 시킬 수 있으므로 `avg_memory_usage_percent` 현재 데이터베이스 로드에 관계 없이 값은 일반적으로 100%에 가깝습니다. 따라서 메모리가 DTU 제한에 간접적으로 영향을 주는 경우에도 DTU 사용률 수식에서 사용 되지 않습니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 리소스 사용률 평균이 낮고 비교적 드물게 사용률이 낮은 데이터베이스에 적합 합니다. 자세한 내용은 [SQL Database 탄력적 풀을 고려해 야 하는 경우](elastic-pool-overview.md)를 참조 하세요.

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델의 하드웨어 생성

DTU 기반 구매 모델에서 고객은 해당 데이터베이스에 사용 되는 하드웨어 생성을 선택할 수 없습니다. 지정 된 데이터베이스는 일반적으로 오랜 시간 동안 특정 하드웨어 세대에서 유지 되지만 (일반적으로 여러 달의 경우) 다른 하드웨어 생성으로 데이터베이스를 이동할 수 있는 특정 이벤트가 있습니다.

예를 들어 다른 서비스 목표로 규모를 확장 또는 축소 하는 경우 또는 데이터 센터의 현재 인프라가 용량 제한에 도달 하거나, 수명 종료로 인해 현재 사용 된 하드웨어가 서비스 해제 되는 경우 다른 하드웨어 생성으로 데이터베이스를 이동할 수 있습니다.

데이터베이스가 다른 하드웨어로 이동 되는 경우 작업 성능이 변경 될 수 있습니다. DTU 모델은 서비스 목표 (Dtu의 수)가 동일 하 게 유지 되는 한, 데이터베이스가 다른 하드웨어 생성으로 이동 하는 동안 [dtu 벤치 마크](./service-tiers-dtu.md#dtu-benchmark) 워크 로드의 처리량 및 응답 시간이 동일 하 게 유지 되도록 보장 합니다.

그러나 Azure SQL Database에서 실행 되는 광범위 한 고객 워크 로드에서 동일한 서비스 목표에 대해 다른 하드웨어를 사용 하는 경우에는 더 많은 영향을 줄 수 있습니다. 다양 한 워크 로드에서 다양 한 하드웨어 구성 및 기능을 활용 합니다. 따라서 DTU 벤치 마크 이외의 워크 로드의 경우에는 데이터베이스가 하드웨어 생성 간에 이동 하는 경우 성능 차이를 확인할 수 있습니다.

예를 들어 네트워크 대기 시간에 민감한 응용 프로그램은 Gen5에서 가속화 된 네트워킹을 사용 하기 때문에 Gen5 하드웨어와 Gen4의 성능을 향상 시킬 수 있지만, 집약적 읽기 IO를 사용 하는 응용 프로그램은 Gen5에서 코어 비율 당 더 높은 메모리를 사용 하 여 Gen4 하드웨어 및 Gen4에서 더 나은 성능을 볼 수 있습니다.

하드웨어 변경 또는 데이터베이스에 대 한 하드웨어 생성 선택을 제어 하려는 고객에 게 중요 한 작업을 수행 하는 고객은 [Vcore](service-tiers-vcore.md) 모델을 사용 하 여 데이터베이스를 만들고 크기를 조정 하는 동안 기본 설정 된 하드웨어 생성을 선택할 수 있습니다. VCore 모델에서는 [단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)모두에 대해 각 하드웨어 세대의 각 서비스 목표에 대 한 리소스 제한이 문서화 되어 있습니다. VCore 모델의 하드웨어 세대에 대 한 자세한 내용은 [하드웨어 생성](./service-tiers-vcore.md#hardware-generations)을 참조 하세요.

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 변환 하려면 응용 프로그램을 오프 라인으로 전환 해야 하나요?

아니요. 응용 프로그램을 오프 라인으로 전환할 필요가 없습니다. 새 서비스 계층은 표준에서 프리미엄 서비스 계층으로 데이터베이스를 업그레이드 하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법을 제공 합니다. Azure Portal, PowerShell, Azure CLI, T-sql 또는 REST API를 사용 하 여이 변환을 시작할 수 있습니다. [단일 데이터베이스 관리](single-database-scale.md) 및 [탄력적 풀 관리](elastic-pool-overview.md)를 참조 하세요.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>VCore 기반 구매 모델의 서비스 계층에서 DTU 기반 구매 모델의 서비스 계층으로 데이터베이스를 변환할 수 있나요?

예, Azure Portal, PowerShell, Azure CLI, T-sql 또는 REST API를 사용 하 여 데이터베이스를 지원 되는 성능 목표로 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](single-database-scale.md) 및 [탄력적 풀 관리](elastic-pool-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- VCore 기반 구매 모델에 대 한 자세한 내용은 [Vcore 기반 구매 모델](service-tiers-vcore.md)을 참조 하세요.
- DTU 기반 구매 모델에 대 한 자세한 내용은 [dtu 기반 구매 모델](service-tiers-dtu.md)을 참조 하세요.