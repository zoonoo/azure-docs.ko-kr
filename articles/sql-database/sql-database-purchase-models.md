---
title: Azure SQL Database 구매 모델 | Microsoft Docs
description: Azure SQL Database 서비스의 데이터베이스에 사용할 수 있는 구매 모델에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 0e9001111d6aa48f0dad69a2fb3b2186bfc37ab7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010518"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database 구매 모델

Azure SQL Database를 사용하면 성능 및 비용 요구에 적합한 완벽히 관리되는 PaaS 데이터베이스 엔진을 손쉽게 구매할 수 있습니다. Azure SQL Database의 배포 모델에 따라 필요에 맞는 구매 모델을 선택할 수 있습니다.

- [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)(권장)을 사용하면 워크로드에 필요한 스토리지 용량 및 컴퓨팅의 정확한 양을 선택할 수 있습니다.
- [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하면 일반적인 워크로드에 맞게 부하가 분산되어 번들로 제공되는 컴퓨팅 및 스토리지 패키지를 선택할 수 있습니다.

Azure SQL Database 배포 모델에서 다양한 구매 모델을 사용할 수 있습니다.

- [Azure SQL Database](sql-database-technical-overview.md)의 [단일 데이터베이스](sql-database-single-databases-manage.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 배포 옵션은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 모두 제공합니다.
- Azure SQL Database의 [관리되는 인스턴스](sql-database-managed-instance.md) 배포 옵션은 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)만 제공합니다.

> [!IMPORTANT]
> [하이퍼스케일 서비스 계층(미리 보기)](sql-database-service-tier-hyperscale.md)은 vCore 구매 모델을 사용하는 단일 데이터베이스에 대해서만 공개 미리 보기로 제공됩니다.

다음 표와 차트에서는 이 두 가지 구매 모델을 비교하고 대조합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|계산, 스토리지 및 IO 리소스를 번들로 묶은 측정값을 기반으로 합니다. 계산 크기는 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-purchase-models.md#dtu-based-purchasing-model)을 참조하세요.|간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.|
|vCore 기반 모델|이 모델을 사용하면 계산 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>컴퓨팅 비용

계산 비용은 애플리케이션에 프로비전되는 총 계산 용량을 반영합니다. 중요 비즈니스용 서비스 계층에서는 3개 이상의 복제본이 자동으로 할당됩니다. 이러한 컴퓨팅 리소스의 추가 할당을 반영하기 위해 vCore 기반 구매 모델의 가격은 중요 비즈니스용 서비스 계층이 범용 서비스 계층보다 약 2.7배 높습니다. 같은 이유로, 중요 비즈니스용 서비스 계층에서 GB당 스토리지 가격이 높을수록 SSD 스토리지의 IO가 높고 대기 시간이 짧습니다. 하지만 두 경우 모두 표준 스토리지 클래스를 사용하므로 백업 스토리지 비용은 두 서비스 계층 간에 다르지 않습니다.

## <a name="storage-costs"></a>저장소 비용

다양한 유형의 저장소에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소의 경우 선택한 최대 데이터베이스 또는 풀 크기에 따라 프로비전된 저장소 비용이 청구됩니다. 해당 최댓값을 줄이거나 늘리지 않는 한 비용은 변하지 않습니다. 백업 저장소는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘리면 인스턴스에서 사용되는 백업 저장소도 늘어납니다. 

기본적으로 7일 분량의 자동화된 데이터베이스 백업이 RA-GRS 표준 Blob Storage에 복사됩니다. 저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 트랜잭션 로그의 크기는 데이터베이스 변동률에 따라 다릅니다. 데이터베이스 크기의 100%와 같은 최소 스토리지 양은 추가 요금 없이 제공됩니다. 추가로 사용되는 백업 스토리지의 경우 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요. 

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

가상 코어는 하드웨어 세대와 하드웨어의 물리적 특성(예: 코어의 수, 메모리, 저장소 크기) 간에 선택할 수 있는 옵션이 함께 제공되는 논리적 CPU를 나타냅니다. vCore 기반 구매 모델은 개별 리소스 사용에 대한 유연성, 제어, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 통해 워크로드 요구 사항에 따라 계산, 메모리 및 스토리지를 선택할 수 있습니다. vCore 기반 구매 모델에서는 [단일 데이터베이스](sql-database-single-database-scale.md), [탄력적 풀](sql-database-elastic-pool.md) 및 [관리되는 인스턴스](sql-database-managed-instance.md)에 대해 [범용](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 및 [중요 비즈니스](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 서비스 계층 중에서 선택할 수 있습니다. 단일 데이터베이스의 경우 [하이퍼스케일 서비스 계층(미리 보기)](sql-database-service-tier-hyperscale.md)을 선택할 수도 있습니다.

vCore 기반 구매 모델을 사용하면 계산 및 스토리지 리소스를 독립적으로 선택하고, 온-프레미스 성능에 맞추고, 가격을 최적화할 수 있습니다. vCore 기반 구매 모델에서 고객이 지불하는 비용은 다음과 같습니다.

- 컴퓨팅(서비스 계층 + vCore 수 및 메모리 양 + 하드웨어 세대)
- 데이터 저장소 및 로그 저장소의 유형 및 크기
- 백업 저장소(RA-GRS)

> [!IMPORTANT]
> 계산, IO, 데이터 및 로그 스토리지는 데이터베이스 또는 탄력적 풀당 요금이 부과됩니다. 백업 저장소는 각 데이터베이스당 요금이 부과됩니다. 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요.
> **지역 제한 사항:** 현재 지원되는 하위 지역 목록은 [하위 지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)을 참조하세요. 현재 지원되지 않는 지역에서 Managed Instance를 만들려면 [Azure Portal을 통해 지원 요청을 보낼 수 있습니다](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

단일 데이터베이스 또는 탄력적 풀이 300DTU 이상 사용하는 경우 vCore 기반 구매 모델로 전환하면 비용을 줄일 수 있습니다. 전환하려는 경우 선택한 API를 사용하거나 Azure Portal을 사용하여 가동 중지 시간 없이 전환할 수 있습니다. 그러나 전환이 필수는 아니며 자동으로 전환되지는 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다. DTU 기반 구매 모델에서 vCore 기반 구매 모델로 전환하려는 경우 다음과 같은 방법으로 컴퓨팅 크기를 선택합니다. 

- 표준 계층의 100DTU마다 범용 계층에 최소 1개의 vCore가 필요합니다.
- 프리미엄 계층의 125DTU마다 중요 비즈니스용 계층에 최소 1개의 vCore가 필요합니다.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU(데이터베이스 트랜잭션 단위)는 CPU, 메모리, 읽기 및 쓰기의 혼합 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 계산 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 미리 구성된 번들과 고정된 지불 금액의 단순성을 선호하는 고객의 경우 DTU 기반 모델이 요구 사항에 더 적합하다는 것을 알 수 있습니다. DTU 기반 구매 모델에서 고객은 [단일 데이터베이스](sql-database-single-database-scale.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 모두에 대해 **기본**, **표준** 및 **프리미엄** 서비스 계층 중에서 선택할 수 있습니다. 이 구매 모델은 [관리되는 인스턴스](sql-database-managed-instance.md)에서 사용할 수 없습니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

[서비스 계층](sql-database-single-database-scale.md) 내 특정 컴퓨팅 크기의 단일 데이터베이스에 대해 Microsoft는 해당 데이터베이스(Azure 클라우드의 다른 데이터베이스와는 무관)에 대해 특정 수준의 리소스를 보장하며 예측 가능한 성능 수준을 제공합니다. 리소스 양은 DTU(데이터베이스 트랜잭션 단위) 수로 계산되며 계산, 스토리지 및 IO 리소스를 번들로 묶은 측정값입니다. 원래 이러한 리소스 간의 비율은 일반적인 실제 OLTP 워크로드에 맞게 디자인된 [OLTP 벤치마크 워크로드](sql-database-benchmark-overview.md)에 따라 결정되었습니다. 워크로드가 이러한 리소스의 양을 초과하면 처리량이 제한되어 성능이 느려지고 시간이 초과됩니다. 워크로드에서 사용되는 리소스는 Azure 클라우드의 다른 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않고, 다른 워크로드에서 사용되는 리소스는 사용자의 SQL 데이터베이스에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU는 여러 계산 크기 및 서비스 계층에서 Azure SQL Database 간의 리소스의 상대적 크기를 이해하는 데 가장 유용합니다. 예를 들어 데이터베이스의 계산 크기를 늘려 DTU를 두 배로 높이면 해당 데이터베이스에 사용할 수 있는 리소스 세트도 똑같이 두 배로 높아집니다. 예를 들어 1750 DTU를 사용하는 프리미엄 P11 데이터베이스는 5개의 DTU를 사용하는 기본 데이터베이스보다 350배 더 많은 DTU 계산 기능을 제공합니다.  

워크로드의 리소스 사용량(DTU)에 대해 더 자세히 이해하려면 [쿼리 성능 Insight](sql-database-query-performance.md)를 사용하여 다음을 수행합니다.

- 향상된 성능을 위해 잠재적으로 조정될 수 있는 CPU/기간/실행 횟수별 최상위 쿼리를 식별합니다. 예를 들어 IO를 많이 사용하는 쿼리는 [메모리 내 최적화 기술](sql-database-in-memory.md)을 사용하여 특정 서비스 계층 및 계산 크기에 사용 가능한 메모리를 보다 효율적으로 사용할 수 있는 이점이 있습니다.
- 쿼리에 대한 세부 정보로 드릴다운하고, 해당 텍스트 및 리소스 사용률에 대한 기록을 확인합니다.
- [SQL Database Advisor](sql-database-advisor.md)에서 수행한 작업을 표시하는 성능 조정 권장 사항에 액세스합니다.

### <a name="elastic-database-transaction-units-edtus"></a>eDTU(탄력적 데이터베이스 트랜잭션 단위)

SQL Database에 항상 필요하지 않을 수도 있는데 항상 사용할 수 있는 전용 리소스 집합(DTU)을 SQL Database에 제공하는 대신, 해당 데이터베이스 간에 리소스 풀을 공유하는 SQL Database 서버의 [탄력적 풀](sql-database-elastic-pool.md)에 데이터베이스를 배치할 수 있습니다. 탄력적 풀의 공유 리소스는 eDTU(Elastic Database 트랜잭션 단위)로 측정됩니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. 탄력적 풀은 풀의 한 데이터베이스에서 리소스를 모두 사용할 수 없도록 하는 동시에 풀의 각 데이터베이스에서 항상 필요한 최소량의 리소스를 사용할 수 있도록 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀에 속한 개별 데이터베이스는 구성된 경계 내에서 자동 크기 조정할 수 있습니다. 부하가 높은 데이터베이스는 요구를 충족하기 위해 더 많은 eDTU를 사용합니다. 부하가 낮은 데이터베이스는 더 적은 eDTU를 사용합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 데이터베이스 단위가 아니라 전체 풀에 대한 리소스를 프로비전하면 관리 작업이 간소화되고 풀 예산을 예측할 수 있습니다.

추가 eDTU는 데이터베이스 가동 중지 시간 없이 풀의 데이터베이스에 영향을 주지 않은 채 기존 풀에 추가할 수 있습니다. 마찬가지로 더 이상 필요하지 않은 추가 eDTU는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에 데이터베이스를 추가 또는 제거하거나 한 데이터베이스에서 부하가 높을 때 사용할 수 있는 eDTU 양을 제한하여 다른 데이터베이스를 위한 eDTU를 확보할 수 있습니다. 데이터베이스에서 예측 가능한 방식으로 리소스를 과소 사용하는 경우 풀 외부로 데이터베이스를 이동하고 예측 가능한 필수 리소스 양을 할당하여 단일 데이터베이스로 구성할 수 있습니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 온-프레미스 또는 SQL Server 가상 머신 워크로드를 Azure SQL Database로 마이그레이션하려는 경우 [DTU 계산기](https://dtucalculator.azurewebsites.net/)를 사용하여 필요한 DTU 수를 대략적으로 계산할 수 있습니다. 기존 Azure SQL Database 워크로드의 경우, 워크로드 최적화 방법에 대한 심도 깊은 인사이트를 얻기 위해 [쿼리 성능 Insight](sql-database-query-performance.md)를 사용하여 데이터베이스 리소스 사용량(DTU)을 해석할 수 있습니다. [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV를 사용하여 지난 1시간 동안의 리소스 사용량을 확인할 수도 있습니다. 또는 카탈로그 뷰 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)에서 지난 14일 동안의 리소스 사용량을 표시하지만 충실도가 평균 5분으로 더 낮습니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 특정 사용 패턴을 가진 많은 데이터베이스에 적합합니다. 지정된 데이터베이스에서 이 패턴은 평균 사용률이 낮고, 사용률 급증이 비교적 드문 데이터베이스를 나타냅니다. SQL Database는 기존 SQL Database 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure Portal의 적절한 풀 구성을 권장합니다. 자세한 내용은 [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool.md)를 참조하세요.

## <a name="purchase-model-frequently-asked-questions-faq"></a>구매 모델 관련 FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>DTU 기반 데이터베이스에서 vCore 기반 서비스 계층으로 변환하려면 애플리케이션을 오프라인으로 전환해야 하나요?

새 서비스 계층에서는 데이터베이스를 표준 서비스 계층에서 프리미엄 서비스 계층으로 업그레이드하거나 그 반대로 업그레이드하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법을 제공합니다. 이 변환은 Azure Portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>vCore 기반 구매 모델을 사용하는 서비스 계층에서 DTU 기반 구매 모델을 사용하는 서비스 계층으로 데이터베이스를 전환할 수 있나요?

예, Azure Portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용하여 데이터베이스를 지원하는 성능 목표로 쉽게 변환할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- vCore 기반 구매 모델의 경우, [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요.
- DTU 기반 구매 모델은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 참조하세요.
