---
title: Azure SQL Database 구매 모델 | Microsoft Docs
description: Azure SQL Database 서비스의 데이터베이스에 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 9d566c0f95325635c5ce5030f4d3b22dba7ceb08
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726034"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database 구매 모델

Azure SQL Database를 사용하면 성능 및 비용 요구에 적합한 완벽히 관리되는 PaaS 데이터베이스 엔진을 손쉽게 구매할 수 있습니다. Azure SQL Database의 배포 모델에 따라 필요에 맞는 구매 모델을 선택할 수 있습니다.
- [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)(권장)을 사용하면 워크로드에 필요한 스토리지 용량 및 컴퓨팅의 정확한 양을 선택할 수 있습니다.
- [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하면 일반적인 워크로드에 맞게 부하가 분산되어 번들로 제공되는 컴퓨팅 및 스토리지 패키지를 선택할 수 있습니다.

Azure SQL Database 배포 모델에서 다양한 구매 모델을 사용할 수 있습니다.
- [Azure SQL Database](sql-database-technical-overview.md)의 [논리 서버](sql-database-logical-servers.md)에는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)과 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)이 모두 제공됩니다. 이 구매 모델에서는 [단일 데이터베이스](sql-database-single-databases-manage.md) 또는 [탄력적 풀](sql-database-elastic-pool.md)을 선택할 수 있습니다.
- Azure SQL Database의 [Managed Instance](sql-database-managed-instance.md)는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)만 제공합니다.

> [!IMPORTANT]
> [하이퍼스케일 데이터베이스(미리 보기)](sql-database-service-tier-hyperscale.md)는 vCore 구매 모델을 사용하는 단일 데이터베이스에 대해서만 공개 미리 보기로 제공됩니다.

다음 표와 차트에서는 이 두 가지 구매 모델을 비교하고 대조합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|계산, 저장소 및 IO 리소스를 번들로 묶은 측정값을 기반으로 합니다. 계산 크기는 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-service-tiers.md#dtu-based-purchasing-model)을 참조하세요.|간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.|
|vCore 기반 모델|이 모델을 사용하면 계산 및 저장소 리소스를 독립적으로 선택할 수 있습니다. SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 비용을 절약할 수 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

가상 코어는 하드웨어 세대와 하드웨어의 물리적 특성(예: 코어의 수, 메모리, 저장소 크기) 간에 선택할 수 있는 옵션이 함께 제공되는 논리적 CPU를 나타냅니다. vCore 기반 구매 모델은 개별 리소스 사용에 대한 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 통해 워크로드 요구 사항에 따라 계산, 메모리 및 저장소를 선택할 수 있습니다. vCore 기반 구매 모델에서는 [단일 데이터베이스](sql-database-single-database-scale.md), [관리되는 인스턴스](sql-database-managed-instance.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 모두에 대해 [범용](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 및 [중요 비즈니스](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 서비스 계층 중에서 선택할 수 있습니다. 단일 데이터베이스의 경우에도 [하이퍼스케일(미리 보기)](sql-database-service-tier-hyperscale.md) 서비스 계층을 선택할 수 있습니다.

vCore 기반 구매 모델을 사용하면 계산 및 저장소 리소스를 독립적으로 선택하고, 온-프레미스 성능에 맞추고, 가격을 최적화할 수 있습니다. vCore 기반 구매 모델에서 고객이 지불하는 비용은 다음과 같습니다.

- 계산(서비스 계층 + vCore 수 및 메모리 양 + 하드웨어 세대)
- 데이터 저장소 및 로그 저장소의 유형 및 크기
- 백업 저장소(RA-GRS)

> [!IMPORTANT]
> 계산, IO, 데이터 저장소 및 로그 저장소는 데이터베이스 또는 탄력적 풀당 요금이 부과됩니다. 백업 저장소는 각 데이터베이스당 요금이 부과됩니다. Managed Instance 요금에 대한 자세한 내용은 [Azure SQL Database Managed Instance](sql-database-managed-instance.md)를 참조하세요.
> **지역 제한 사항:** vCore 기반 구매 모델을 다음 지역에서는 아직 사용할 수 없습니다. 유럽 서부, 프랑스 중부, 영국 남부, 영국 서부 및 오스트레일리아 남동부.

데이터베이스 또는 탄력적 풀에서 300을 초과하는 DTU를 vCore로 변환하면 비용을 절약할 수 있습니다. 선택한 API를 사용하거나 Azure Portal을 사용하여 가동 중지 시간 없이 변환할 수 있습니다. 그러나 변환이 필요하지 않습니다. DTU 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다. DTU 모델을 vCore 모델로 변환하려는 경우 경험 규칙을 사용하여 계산 크기를 선택해야 합니다. 이 경우 표준 계층의 각 100 DTU에는 범용 계층에서 1개 이상의 vCore가 필요합니다. 프리미엄 계층의 각 125 DTU에는 중요 비즈니스용 계층에서 1개 이상의 vCore가 필요합니다.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU(데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 계산 리소스 및 포함된 저장소를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성된 번들과 고정된 지불 금액의 단순성을 선호하는 고객의 경우 DTU 기반 모델이 요구 사항에 더 적합하다는 것을 알 수 있습니다. DTU 기반 구매 모델에서 고객은 [단일 데이터베이스](sql-database-single-database-scale.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 모두에 대해 **기본**, **표준** 및 **프리미엄** 서비스 계층 중에서 선택할 수 있습니다. 이 구매 모델은 [관리되는 인스턴스](sql-database-managed-instance.md)에서 사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](sql-database-single-database-scale.md) 내 특정 계산 크기의 단일 Azure SQL Database에 대해 Microsoft는 해당 데이터베이스(Azure 클라우드의 다른 데이터베이스와는 무관)에 대해 특정 수준의 리소스를 보장하며 예측 가능한 성능 수준을 제공합니다. 리소스 양은 DTU(데이터베이스 트랜잭션 단위) 수로 계산되며 계산, 저장소 및 IO 리소스를 번들로 묶은 측정값입니다. 원래 이러한 리소스 간의 비율은 일반적인 실제 OLTP 워크로드에 맞게 디자인된 [OLTP 벤치마크 워크로드](sql-database-benchmark-overview.md)에 따라 결정되었습니다. 워크로드가 이러한 리소스의 양을 초과하면 처리량이 제한되어 성능이 느려지고 시간이 초과됩니다. 워크로드에서 사용되는 리소스는 Azure 클라우드의 다른 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않고, 다른 워크로드에서 사용되는 리소스는 사용자의 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU는 여러 계산 크기 및 서비스 계층에서 Azure SQL Database 간의 리소스의 상대적 크기를 이해하는 데 가장 유용합니다. 예를 들어 데이터베이스의 계산 크기를 늘려 DTU를 두 배로 높이면 해당 데이터베이스에 사용할 수 있는 리소스 집합도 똑같이 두 배로 높아집니다. 예를 들어 1750 DTU를 사용하는 프리미엄 P11 데이터베이스는 5개의 DTU를 사용하는 기본 데이터베이스보다 350배 더 많은 DTU 계산 기능을 제공합니다.  

워크로드의 리소스(DTU) 소비에 대해 더 자세히 이해하려면 [Azure SQL Database Query Performance Insight](sql-database-query-performance.md)를 사용하여 다음을 수행합니다.

- 향상된 성능을 위해 잠재적으로 조정될 수 있는 CPU/기간/실행 횟수별 최상위 쿼리를 식별합니다. 예를 들어 IO를 많이 사용하는 쿼리는 [메모리 내 최적화 기술](sql-database-in-memory.md)을 사용하여 특정 서비스 계층 및 계산 크기에 사용 가능한 메모리를 보다 효율적으로 사용할 수 있는 이점이 있습니다.
- 쿼리에 대한 세부 정보로 드릴다운하고, 해당 텍스트 및 리소스 사용률에 대한 기록을 확인합니다.
- [SQL Database Advisor](sql-database-advisor.md)에서 수행한 작업을 표시하는 성능 조정 권장 사항에 액세스합니다.

### <a name="elastic-database-transaction-units-edtus"></a>eDTU(탄력적 데이터베이스 트랜잭션 단위)

SQL Database에 항상 필요하지 않을 수도 있는데 항상 사용할 수 있는 전용 리소스 집합(DTU)을 SQL Database에 제공하는 대신, 해당 데이터베이스 간에 리소스 풀을 공유하는 SQL Database 서버의 [탄력적 풀](sql-database-elastic-pool.md)에 데이터베이스를 배치할 수 있습니다. 탄력적 풀의 공유 리소스는 eDTU(Elastic Database 트랜잭션 단위)로 측정됩니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. 탄력적 풀은 풀의 한 데이터베이스에서 리소스를 모두 사용할 수 없도록 하는 동시에 풀의 각 데이터베이스에서 항상 필요한 최소량의 리소스를 사용할 수 있도록 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에 속한 개별 데이터베이스는 구성된 경계 내에서 자동 크기 조정할 수 있습니다. 부하가 높은 데이터베이스는 요구를 충족하기 위해 더 많은 eDTU를 사용합니다. 부하가 낮은 데이터베이스는 더 적은 eDTU를 사용합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 데이터베이스 단위가 아니라 전체 풀에 대한 리소스를 프로비전하면 관리 작업이 간소화되고 풀 예산을 예측할 수 있습니다.

추가 eDTU는 데이터베이스 가동 중지 시간 없이 풀의 데이터베이스에 영향을 주지 않은 채 기존 풀에 추가할 수 있습니다. 마찬가지로 더 이상 필요하지 않은 추가 eDTU는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에 데이터베이스를 추가 또는 제거하거나 한 데이터베이스에서 부하가 높을 때 사용할 수 있는 eDTU 양을 제한하여 다른 데이터베이스를 위한 eDTU를 확보할 수 있습니다. 데이터베이스에서 예측 가능한 방식으로 리소스를 과소 사용하는 경우 풀 외부로 데이터베이스를 이동하고 예측 가능한 필수 리소스 양을 할당하여 단일 데이터베이스로 구성할 수 있습니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 머신 워크로드를 Azure SQL Database에 마이그레이션하려는 경우 [DTU 계산기](http://dtucalculator.azurewebsites.net/) 를 사용하여 필요한 DTU의 수를 대략적으로 계산할 수 있습니다. 기존 Azure SQL Database 워크로드의 경우, 워크로드 최적화 방법에 대한 심도 깊은 인사이트를 얻기 위해 [SQL Database Query Performance Insight](sql-database-query-performance.md)를 사용하여 데이터베이스 리소스 사용량(DTU)을 해석할 수 있습니다. [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV를 사용하여 지난 1시간 동안의 리소스 사용량을 확인할 수도 있습니다. 또는 카탈로그 뷰 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)에서 지난 14일 동안의 리소스 사용량을 표시하지만 충실도가 평균 5분으로 더 낮습니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 특정 사용 패턴을 가진 많은 데이터베이스에 적합합니다. 지정된 데이터베이스에서 이 패턴은 평균 사용률이 낮고, 사용률 급증이 비교적 드문 데이터베이스를 나타냅니다. SQL Database는 기존 SQL Database 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure Portal의 적절한 풀 구성을 권장합니다. 자세한 내용은 [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- vCore 기반 구매 모델의 경우, [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요.
- DTU 기반 구매 모델은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 참조하세요.
