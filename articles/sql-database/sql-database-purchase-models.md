---
title: Azure SQL Database 구매 모델 | Microsoft Docs
description: Azure SQL Database 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: ab291ab60a5e72b5c61552bc54c10e303c1df1a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492494"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>VCore와 DTU 구매 모델 중에서 선택 합니다.

Azure SQL Database를 사용 하면 성능 및 비용 요구에 맞는 완전히 관리 되는 PaaS (platform as a service) 데이터베이스 엔진을 쉽게 구매할 수 있습니다. Azure SQL Database 위해 선택한 배포 모델에 따라 사용자에 게 적합 한 구매 모델을 선택할 수 있습니다.

- [Vcore (가상 코어) 기반 구매 모델](sql-database-service-tiers-vcore.md) (권장) 이 구매 모델은 프로 비전 된 계산 계층과 서버를 사용 하지 않는 계산 계층 중에서 선택할 것을 제공 합니다. 프로 비전 된 계산 계층을 사용 하 여 워크 로드에 대해 항상 프로 비전 되는 정확한 계산 리소스 양을 선택 합니다. 서버를 사용 하지 않는 계산 계층에서는 구성 가능한 계산 범위에 대해 계산 리소스의 자동 크기 조정을 지정 합니다. 이 계산 계층을 사용 하 여 워크 로드 활동을 기반으로 데이터베이스를 자동으로 일시 중지 하 고 다시 시작할 수도 있습니다. 프로 비전 된 계산 계층의 시간 단위당 vCore 단가가 서버를 사용 하지 않는 계산 계층에 있는 것 보다 낮습니다.
- [DTU (데이터베이스 트랜잭션 단위) 기반 구매 모델](sql-database-service-tiers-dtu.md)입니다. 이 구매 모델은 공통 작업에 대해 분산 된 번들 계산 및 저장소 패키지를 제공 합니다.

다양 한 Azure SQL Database 배포 모델에 대해 여러 구매 모델을 사용할 수 있습니다.

- [Azure SQL Database](sql-database-single-databases-manage.md)의 [단일 데이터베이스](sql-database-elastic-pool.md) 및 [탄력적 풀](sql-database-technical-overview.md) 배포 옵션은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 모두 제공합니다.
- Azure SQL Database의 [관리 되는 인스턴스](sql-database-managed-instance.md) 배포 옵션은 [vcore 기반 구매 모델만](sql-database-service-tiers-vcore.md)제공 합니다.
- 하이퍼 [확장 서비스 계층](sql-database-service-tier-hyperscale.md) 은 [vcore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용 하는 단일 데이터베이스에 사용할 수 있습니다.

다음 표와 차트는 vCore 기반 및 DTU 기반 구매 모델을 비교 하 고 대조 합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|이 모델은 계산, 저장소 및 i/o 리소스를 함께 제공 하는 측정값을 기반으로 합니다. 계산 크기는 단일 데이터베이스에 대 한 Dtu 및 탄력적 풀에 대 한 Edtu (탄력적 데이터베이스 트랜잭션 단위)에 표시 됩니다. Dtu 및 Edtu에 대 한 자세한 내용은 [dtu 및 Edtu 란?](sql-database-purchase-models.md#dtu-based-purchasing-model)을 참조 하세요.|간단 하 고 미리 구성 된 리소스 옵션을 원하는 고객에 게 가장 적합 합니다.|
|vCore 기반 모델|이 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델 비교](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>컴퓨팅 비용

### <a name="provisioned-compute-costs"></a>프로 비전 된 계산 비용

프로 비전 된 계산 계층에서 계산 비용은 응용 프로그램에 대해 프로 비전 된 총 계산 용량을 반영 합니다.

중요 비즈니스용 서비스 계층에서는 3개 이상의 복제본이 자동으로 할당됩니다. 이러한 추가 계산 리소스 할당을 반영 하기 위해 vCore 기반 구매 모델의 가격은 범용 서비스 계층에 있는 것 보다 중요 한 비즈니스 서비스 계층의 약 2.7 x 보다 높습니다. 마찬가지로 업무상 중요 한 서비스 계층에서 GB 당 더 높은 저장소 가격은 SSD 저장소의 높은 i/o 및 짧은 대기 시간을 반영 합니다.

두 계층 모두 standard storage를 사용 하기 때문에 백업 저장소 비용은 비즈니스에 중요 한 서비스 계층 및 범용 서비스 계층에 대해 동일 합니다.

### <a name="serverless-compute-costs"></a>서버를 사용 하지 않는 계산 비용

서버를 사용 하지 않는 계산 계층에 대해 계산 용량이 정의 되 고 비용이 계산 되는 방법에 대 한 설명은 서버를 사용 하지 않는 [SQL Database](sql-database-serverless.md)를 참조 하세요.

## <a name="storage-costs"></a>스토리지 비용

다양한 유형의 스토리지에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로 비전 된 저장소에 대 한 요금이 청구 됩니다. 최대값을 줄이거나 늘리지 않으면 비용이 변경 되지 않습니다. 백업 스토리지는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘려 인스턴스에서 사용 되는 백업 저장소를 늘립니다.

기본적으로 데이터베이스의 자동화 된 백업 7 일은 읽기 액세스 지역 중복 저장소 (RA-GRS) 표준 Blob 저장소 계정에 복사 됩니다. 이 저장소는 매주 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에 사용 됩니다. 트랜잭션 로그의 크기는 데이터베이스의 변경 률에 따라 달라 집니다. 데이터베이스 크기의 100%와 같은 최소 저장소 크기는 추가 비용 없이 제공 됩니다. 백업 스토리지의 추가 사용량은 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요.

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

VCore (가상 코어)는 논리적 CPU를 나타내며 하드웨어 세대와 하드웨어의 물리적 특성 (예: 코어 수, 메모리 및 저장소 크기) 중에서 선택할 수 있는 옵션을 제공 합니다. VCore 기반 구매 모델은 개별 리소스 소비의 유연성, 제어, 투명성 및 온-프레미스 워크 로드 요구 사항을 클라우드로 변환 하는 간단한 방법을 제공 합니다. 이 모델을 사용 하면 워크 로드 요구 사항에 따라 계산, 메모리 및 저장소 리소스를 선택할 수 있습니다.

VCore 기반 구매 모델에서 [단일 데이터베이스](sql-database-single-database-scale.md), [탄력적 풀](sql-database-elastic-pool.md)및 [관리 되는 인스턴스의](sql-database-managed-instance.md) [범용](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 및 [중요 비즈니스용](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 서비스 계층 중에서 선택할 수 있습니다. 단일 데이터베이스의 경우에는 [대규모 서비스 계층](sql-database-service-tier-hyperscale.md)을 선택할 수도 있습니다.

VCore 기반 구매 모델을 사용 하 여 독립적으로 계산 및 저장소 리소스를 선택 하 고, 온-프레미스 성능을 일치 시키고, 가격을 최적화할 수 있습니다. VCore 기반 구매 모델에서 다음에 대 한 비용을 지불 합니다.

- 계산 리소스 (서비스 계층 + vCores 수 및 메모리의 양과 하드웨어 세대)
- 데이터 및 로그 저장소의 유형 및 양입니다.
- 백업 저장소 (RA-GRS).

> [!IMPORTANT]
> 계산 리소스, i/o, 데이터 및 로그 저장소는 데이터베이스 또는 탄력적 풀 별로 요금이 청구 됩니다. 백업 저장소는 각 데이터베이스당 청구 됩니다. 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요.
> **지역 제한 사항:** 지원 되는 지역에 대 한 현재 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)을 참조 하세요. 현재 지원 되지 않는 지역에서 관리 되는 인스턴스를 만들려면 [Azure Portal를 통해 지원 요청을 보냅니다](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

단일 데이터베이스 또는 탄력적 풀이 300 개 보다 많은 Dtu를 사용 하는 경우 vCore 기반 구매 모델로 변환 하면 비용이 줄어들 수 있습니다. 원하는 API를 사용 하거나 가동 중지 시간 없이 Azure Portal를 사용 하 여 변환할 수 있습니다. 그러나 변환은 필요 하지 않으며 자동으로 수행 되지 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다.

DTU 기반 구매 모델에서 vCore 기반 구매 모델로 변환 하려면 다음 엄지 규칙을 사용 하 여 계산 크기를 선택 합니다.

- 표준 계층의 모든 100 Dtu에는 범용 서비스 계층의 vCore가 하나 이상 필요 합니다.
- 프리미엄 계층의 모든 125 Dtu에는 업무상 중요 한 서비스 계층에 하나 이상의 vCore가 필요 합니다.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU (데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 컴퓨팅 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성 된 번들 및 고정 상환의 단순성을 선호 하는 경우 DTU 기반 모델이 사용자의 요구에 더 적합할 수 있습니다.

DTU 기반 구매 모델에서 [단일 데이터베이스](sql-database-single-database-scale.md) 와 [탄력적 풀](sql-database-elastic-pool.md)에 대해 기본, 표준 및 프리미엄 서비스 계층 중에서 선택할 수 있습니다. DTU 기반 구매 모델은 [관리 되는 인스턴스에](sql-database-managed-instance.md)사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](sql-database-single-database-scale.md)내의 특정 계산 크기에 있는 단일 데이터베이스의 경우 Microsoft는 해당 데이터베이스에 대 한 특정 수준의 리소스 (Azure 클라우드의 다른 데이터베이스와 무관)를 보장 합니다. 이러한 보장은 예측 가능한 수준의 성능을 제공 합니다. 데이터베이스에 할당 되는 리소스의 양은 Dtu 수로 계산 되며 계산, 저장소 및 i/o 리소스의 번들로 측정 됩니다.

이러한 리소스 간의 비율은 원래 실제 OLTP 워크 로드의 일반적인 [oltp (온라인 트랜잭션 처리) 벤치 마크 워크 로드](sql-database-benchmark-overview.md) 에 의해 결정 됩니다. 작업이 이러한 리소스의 용량을 초과 하면 처리량이 제한 되어 성능이 저하 되 고 제한 시간이 초과 됩니다.

워크 로드에서 사용 하는 리소스는 Azure 클라우드의 다른 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다. 마찬가지로 다른 작업에서 사용 하는 리소스는 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu는 다양 한 계산 크기 및 서비스 계층에서 Azure SQL 데이터베이스에 할당 되는 상대 리소스를 이해 하는 데 가장 유용 합니다. 예:

- 데이터베이스의 계산 크기를 늘려서 Dtu를 두 배로 늘리면 해당 데이터베이스에서 사용할 수 있는 리소스 집합이 배가 됩니다.
- 1750 dtu를 포함 하는 프리미엄 서비스 계층 P11 데이터베이스는 5 개의 dtu를 포함 하는 기본 서비스 계층 데이터베이스 보다 350 배 더 이상의 dtu 계산 기능을 제공 합니다.  

워크 로드의 리소스 (DTU) 소비량에 대 한 자세한 정보를 얻으려면 [쿼리 성능 정보](sql-database-query-performance.md) 를 사용 하 여 다음을 수행 합니다.

- 성능 향상을 위해 잠재적으로 튜닝할 수 있는 CPU/기간/실행 수 별 상위 쿼리를 식별 합니다. 예를 들어 i/o를 많이 사용 하는 쿼리는 [메모리 내 최적화 기술을](sql-database-in-memory.md) 사용 하 여 특정 서비스 계층 및 계산 크기에서 사용 가능한 메모리를 보다 효율적으로 활용할 수 있습니다.
- 쿼리의 세부 정보로 드릴 다운 하 여 해당 텍스트와 리소스 사용 기록을 볼 수 있습니다.
- [SQL Database Advisor](sql-database-advisor.md)에서 수행 하는 작업을 표시 하는 성능 튜닝 권장 구성에 액세스 합니다.

### <a name="elastic-database-transaction-units-edtus"></a>Edtu (탄력적 데이터베이스 트랜잭션 단위)

항상 사용할 수 있는 SQL 데이터베이스의 경우 항상 필요 하지 않을 수 있는 전용 리소스 (Dtu) 집합을 제공 하는 대신, 이러한 데이터베이스를 [탄력적 풀](sql-database-elastic-pool.md)에 저장할 수 있습니다. 탄력적 풀의 데이터베이스는 단일 Azure SQL Database 서버에 있으며 리소스 풀을 공유 합니다.

탄력적 풀의 공유 리소스는 Edtu (탄력적 데이터베이스 트랜잭션 단위)로 측정 됩니다. 탄력적 풀은 매우 다양 하 고 예측할 수 없는 사용 패턴을 가진 여러 데이터베이스의 성능 목표를 관리 하기 위한 간단 하 고 비용 효율적인 솔루션을 제공 합니다. 탄력적 풀은 풀의 데이터베이스 하나에서 모든 리소스를 사용할 수 없도록 보장 하는 반면 풀의 각 데이터베이스에는 항상 필요한 최소한의 리소스를 사용할 수 있도록 보장 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에서 개별 데이터베이스는 구성 된 경계 내에서 자동으로 크기를 조정할 수 있습니다. 과도 한 로드에서 데이터베이스는 수요를 충족 하기 위해 더 많은 Edtu를 사용 합니다. 낮은 부하의 데이터베이스는 더 적은 Edtu를 사용 합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 리소스는 데이터베이스 단위가 아니라 전체 풀에 대해 프로 비전 되므로 탄력적 풀은 관리 작업을 단순화 하 고 풀에 대해 예측 가능한 예산을 제공 합니다.

데이터베이스 가동 중지 시간 없이 풀에 있는 데이터베이스에 영향을 주지 않고 기존 풀에 Edtu를 더 추가할 수 있습니다. 마찬가지로 더 이상 추가 Edtu 필요 하지 않은 경우 언제 든 지 기존 풀에서 제거 합니다. 또한 언제 든 지 풀에서 데이터베이스를 추가 하거나 뺄 수 있습니다. 다른 데이터베이스에 대 한 Edtu을 예약 하려면 데이터베이스가 높은 부하 상태에서 사용할 수 있는 Edtu 수를 제한 합니다. 데이터베이스에서 일관 되 게 리소스를 사용 하는 경우에는 풀에서 데이터를 이동 하 여 예상 되는 양의 필요한 리소스로 단일 데이터베이스로 구성 합니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 머신 워크 로드를 Azure SQL Database 마이그레이션하려면 [dtu 계산기](https://dtucalculator.azurewebsites.net/) 를 사용 하 여 필요한 dtu의 수를 대략적으로 계산 합니다. 기존 Azure SQL Database 워크 로드의 경우 [쿼리 성능 정보](sql-database-query-performance.md) 를 사용 하 여 데이터베이스 리소스 사용 (dtu)을 이해 하 고 워크 로드 최적화에 대 한 심층적인 통찰력을 얻습니다. [RESOURCE_STATS](https://msdn.microsoft.com/library/dn800981.aspx) DMV (동적 관리 뷰)를 사용 하 여 지난 1 시간의 리소스 소비량을 볼 수 있습니다. [Resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 카탈로그 뷰는 지난 14 일간의 리소스 사용을 표시 하지만 5 분 분량의 평균을 사용 합니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 리소스 사용률 평균이 낮고 비교적 드물게 사용률이 낮은 데이터베이스에 적합 합니다. SQL Database는 기존 SQL Database 서버에서 데이터베이스의 기록 리소스 사용량을 자동으로 평가 하 고 Azure Portal에서 적절 한 풀 구성을 권장 합니다. 자세한 내용은 [SQL Database 탄력적 풀을 고려해 야 하는 경우](sql-database-elastic-pool.md)를 참조 하세요.

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 변환 하려면 응용 프로그램을 오프 라인으로 전환 해야 하나요?

아니요. 응용 프로그램을 오프 라인으로 전환할 필요가 없습니다. 새 서비스 계층은 표준에서 프리미엄 서비스 계층으로 데이터베이스를 업그레이드 하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법을 제공 합니다. Azure Portal, PowerShell, Azure CLI, T-sql 또는 REST API를 사용 하 여이 변환을 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>VCore 기반 구매 모델의 서비스 계층에서 DTU 기반 구매 모델의 서비스 계층으로 데이터베이스를 변환할 수 있나요?

예, Azure Portal, PowerShell, Azure CLI, T-sql 또는 REST API를 사용 하 여 데이터베이스를 지원 되는 성능 목표로 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- VCore 기반 구매 모델에 대 한 자세한 내용은 [Vcore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조 하세요.
- DTU 기반 구매 모델에 대 한 자세한 내용은 [dtu 기반 구매 모델](sql-database-service-tiers-dtu.md)을 참조 하세요.
