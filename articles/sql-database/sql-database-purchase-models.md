---
title: Azure SQL Database 구매 모델 | Microsoft Docs
description: Azure SQL Database에 사용할 수 있는 구매 모델에 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431377"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>vCore와 DTU 구매 모델 중에서 선택

Azure SQL Database를 사용 하면 쉽게 성능 및 비용 필요에 맞는 서비스 (PaaS) 데이터베이스 엔진으로 완전히 관리 되는 플랫폼을 구매할 수 있습니다. Azure SQL Database에 대해 선택한 배포 모델에 따라 적합 한 구매 모델을 선택할 수 있습니다.

- [가상 코어 (vCore)-기반 구매 모델](sql-database-service-tiers-vcore.md) (권장). 이 구매 모델을 프로 비전 된 계산 계층과 (미리 보기) 서버 리스 계산 계층 중에서 선택할을 제공합니다. 프로 비전 된 계산 계층을 사용 하 여 워크 로드에 항상 프로 비전 되는 계산 리소스의 정확한 크기를 선택 합니다. 서버 리스 계산 계층을 사용 하 여 구성할 수 있는 계산 범위에 대해 계산 리소스 자동 크기 조정을 지정합니다. 이 계산 계층을 사용 하 여 일시 중지 하 고 워크 로드 동작을 기준으로 데이터베이스를 다시 시작도 자동으로 수 있습니다. 시간 단위당 vCore 단가 서버 리스 계산 계층에 비해 프로 비전 된 계산 계층에서 낮은 합니다.
- [데이터베이스 트랜잭션 단위 (DTU)-기반 구매 모델](sql-database-service-tiers-dtu.md)합니다. 이 구매 모델 일반적인 워크 로드에 대 한 분산 된 계산 및 저장소 번들된 패키지를 제공 합니다.

다른 구매 모델 다른 Azure SQL Database 배포 모델에 사용할 수 있습니다.

- [Azure SQL Database](sql-database-technical-overview.md)의 [단일 데이터베이스](sql-database-single-databases-manage.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 배포 옵션은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 모두 제공합니다.
- 합니다 [관리 되는 인스턴스](sql-database-managed-instance.md) Azure SQL Database의 배포 옵션은만 제공 합니다 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)합니다.
- [하이퍼 스케일 서비스 계층](sql-database-service-tier-hyperscale.md) 를 사용 하는 단일 데이터베이스에 대해 제공 되는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)합니다.

다음 테이블 및 차트 비교 하 고 vCore 기반 및 DTU 기반 구매 모델을 대조 합니다.

|**구매 모델**|**설명**|**적합한 대상**|
|---|---|---|
|DTU 기반 모델|이 모델의 계산, 저장소 및 I/O 리소스를 번들로 묶은 측정값을 기반으로 합니다. 계산 크기는 단일 데이터베이스에 대 한 Dtu 및 탄력적 풀에 대 한 elastic database 트랜잭션 단위 (Edtu)로 표시 됩니다. Dtu 및 Edtu에 대 한 자세한 내용은 참조 하세요. [Dtu 및 Edtu 란?](sql-database-purchase-models.md#dtu-based-purchasing-model)합니다.|가장 간단 하 고 미리 구성 된 리소스 옵션을 원하는 고객 적합 합니다.|
|vCore 기반 모델|이 모델을 사용하면 계산 및 스토리지 리소스를 독립적으로 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다.|유연성, 제어 및 투명성을 중요시하는 고객에게 가장 적합합니다.|
||||  

![가격 책정 모델 비교](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>컴퓨팅 비용

### <a name="provisioned-compute-costs"></a>프로 비전 된 계산 비용

프로 비전 된 계산 계층의 계산 비용은 응용 프로그램에 대 한 프로 비전 되는 총 계산 용량을 반영 합니다.

중요 비즈니스용 서비스 계층에서는 3개 이상의 복제본이 자동으로 할당됩니다. 이 추가 계산 리소스 할당에 맞게, vCore 기반 구매 모델의 가격은 약 2.7 범용 서비스 계층에 비해 비즈니스 중요 한 서비스 계층에서 더 높은 x입니다. 마찬가지로, 비즈니스 중요 한 서비스 계층에서 GB 당 더 높은 저장소 가격이 높은 I/O 및 SSD 저장소의 짧은 대기 시간을 반영합니다.

백업 저장소 비용이 모두 계층 표준 저장소를 사용 하기 때문에 비즈니스 중요 한 서비스 계층 및 범용 서비스 계층에 대 한 같습니다.

### <a name="serverless-compute-costs"></a>서버 리스 계산 비용

서버 리스 계산 계층에 대 한 계산 용량을 정의 하는 방법에 대 한 설명 및 비용 계산에 대 한 참조 [SQL Database 서버 리스 (미리 보기)](sql-database-serverless.md)합니다.

## <a name="storage-costs"></a>저장소 비용

다양한 유형의 저장소에 대해 별도의 방식으로 요금이 청구됩니다. 데이터 저장소에 대 한 최대 데이터베이스 또는 풀 크기 선택에 따라 프로 비전된 된 저장소에 대 한 청구 됩니다. 비용을 줄이거나 최대를 늘리지 않는 한 변경 되지 않습니다. 백업 저장소는 인스턴스의 자동 백업과 연결되고 동적으로 할당됩니다. 백업 보존 기간을 늘리면 인스턴스에서 사용 되는 백업 저장소도 늘어납니다.

기본적으로 7 일간의 자동화 된 백업 데이터베이스의 읽기 액세스 지역 중복 저장소 (RA-GRS) 표준 Blob 저장소 계정에 복사 됩니다. 이 저장소는 주별 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에서 사용 됩니다. 트랜잭션 로그의 크기는 데이터베이스의 변경 비율에 따라 달라 집니다. 데이터베이스 크기의 100%에 최소 저장소 금액은 추가 비용 없이 제공 됩니다. 백업 저장소의 추가 사용량은 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요.

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

가상 코어 (vCore)는 논리 CPU 수를 나타내며 세대의 하드웨어 및 하드웨어 (예를 들어 코어, 메모리 및 저장소 크기의 수)의 물리적 특성 중에서 선택 하는 옵션을 제공 합니다. VCore 기반 구매 모델을 제공 하면 유연성, 제어, 개별 리소스 사용의 투명도 및 변환 하는 간단한 방법 온-프레미스 워크 로드 요구 사항 클라우드입니다. 이 모델을 사용 하면 워크 로드 요구에 따라 계산, 메모리 및 저장소 리소스를 선택할 수 있습니다.

VCore 기반 구매 모델에서 선택할 수 있습니다는 [범용](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 하 고 [업무상 중요](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 서비스에 대 한 계층 [단일 데이터베이스](sql-database-single-database-scale.md), [ 탄력적 풀](sql-database-elastic-pool.md), 및 [관리 되는 인스턴스](sql-database-managed-instance.md)합니다. 단일 데이터베이스에 대해 선택할 수도 있습니다는 [하이퍼 스케일 서비스 계층](sql-database-service-tier-hyperscale.md)합니다.

VCore 기반 구매 모델을 사용 하 여 독립적으로 계산 및 저장소 리소스를 선택, 온-프레미스 성능, 일치 및 가격을 최적화할 수 있습니다. VCore 기반 구매 모델에서에 대 한 지불 합니다.

- 계산 리소스 (서비스 계층 + vcore 수 및 메모리 + 하드웨어 세대 크기)입니다.
- 데이터 및 로그 저장소의 크기와 형식입니다.
- 백업 저장소 (RA-GRS)입니다.

> [!IMPORTANT]
> 계산 리소스, I/O 및 데이터 및 로그 저장소는 데이터베이스 또는 탄력적 풀 당 요금이 청구 됩니다. 백업 저장소는 각 데이터베이스당 요금이 청구 됩니다. 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요.
> **지역 제한 사항:** 현재 지원되는 하위 지역 목록은 [하위 지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)을 참조하세요. 현재 지원 되지 않는 지역에서 관리 되는 인스턴스를 만들려고 [Azure portal 통해 지원 요청을 보낼](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)합니다.

단일 데이터베이스 또는 탄력적 풀 300 개 이상의 Dtu를 사용 하는 경우를 vCore 기반 구매 모델로 변환 비용을 줄일 수 있습니다. 선택한 API를 사용 하 여 또는 가동 중지 시간 없이 Azure portal을 사용 하 여 변환할 수 있습니다. 그러나 변환이 필요 하지 않습니다 및 자동으로 수행 되지 않습니다. DTU 기반 구매 모델이 성능 및 비즈니스 요구 사항을 충족하는 경우 이 모델을 계속 사용해야 합니다.

DTU 기반 구매 모델에서 vCore 기반 구매 모델으로 변환 하려면 다음 규칙의 thumb을 사용 하 여 계산 크기를 선택 합니다.

- 표준 계층의 모든 100 Dtu 범용 서비스 계층에서 하나 이상의 vCore 필요합니다.
- 프리미엄 계층에서 125dtu 마다 하나 이상의 vCore를 중요 비즈니스용 서비스 계층에 필요합니다.

## <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

데이터베이스 트랜잭션 단위 (DTU) CPU, 메모리, 읽기 및 쓰기의 혼합된 측정값을 나타냅니다. DTU 기반 구매 모델은 다양한 수준의 애플리케이션 성능을 구동하기 위해 계산 리소스 및 포함된 스토리지를 미리 구성된 번들로 묶은 집합을 제공합니다. 매월 고정된 지급액 및 미리 구성 된 번들의 단순성을 원할 경우 DTU 기반 모델에 맞게 적합할 수 있습니다.

DTU 기반 구매 모델에서 선택할 수 있습니다는 basic, standard 및 premium 서비스 계층 둘 다에 대해 [단일 데이터베이스](sql-database-single-database-scale.md) 하 고 [탄력적 풀](sql-database-elastic-pool.md)합니다. DTU 기반 구매 모델에 사용할 수 없습니다 [관리 되는 인스턴스](sql-database-managed-instance.md)합니다.

### <a name="database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)

특정 단일 데이터베이스 내에서 크기를 계산 하는 [서비스 계층](sql-database-single-database-scale.md), Microsoft 특정 수준의 해당 데이터베이스에 대 한 리소스 (Azure 클라우드의 다른 데이터베이스와 무관) 보장 합니다. 이 보장에는 예측 가능한 수준의 성능 제공합니다. 데이터베이스에 할당 된 리소스의 양을 Dtu 수로 계산 되 고 계산, 저장소 및 I/O 리소스의 번들로 묶은 측정값입니다.

이러한 리소스 간의 비율은 원래 따라는 [온라인 트랜잭션 처리 (OLTP) 벤치 마크 워크 로드](sql-database-benchmark-overview.md) 일반적인 실제 OLTP 워크 로드 되도록 설계 되었습니다. 워크 로드가 이러한 리소스의 용량을 초과 하는 경우 처리량 제한 됩니다 성능이 느려지고 시간 초과가 발생 합니다.

워크 로드에서 사용 하는 리소스는 Azure 클라우드의 다른 SQL database에 사용할 수 있는 리소스에 영향을 미치지 합니다. 마찬가지로, SQL database에 사용할 수 있는 리소스에 영향을 미치지 다른 워크 로드에서 사용 하는 리소스입니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu는 다른 계산 크기 및 서비스 계층에서 Azure SQL database에 대 한 할당 되는 상대 리소스를 이해 하는 데 가장 유용 합니다. 예를 들면 다음과 같습니다.

- 데이터베이스의 계산 크기를 증가 시켜 Dtu를 두 배로 높일 경우 해당 데이터베이스에 사용할 수 있는 리소스 집합을 두 배로 높일 경우와 같습니다.
- Premium 서비스 계층 P11 데이터베이스 1750 Dtu 사용 하 여 350 배 더 많은 DTU 계산 능력 보다 5 개의 Dtu 사용 하 여 기본 서비스 계층 데이터베이스를 제공 합니다.  

워크 로드의 리소스 (DTU) 소비에 대 한 심층적인 통찰력을 확보 하려면 사용 하 여 [쿼리 성능을 insights](sql-database-query-performance.md) 하려면:

- 향상 된 성능을 위해 잠재적으로 조정할 수 있는 CPU/기간/실행 수 별 상위 쿼리를 식별 합니다. O 집약적인 쿼리를에서 이점을 얻을 수는 예를 들어 [메모리 내 최적화 기술](sql-database-in-memory.md) 서비스 계층 및 크기를 계산 합니다. 더 나은 특정에서 사용 가능한 메모리를 사용 합니다.
- 텍스트 및 리소스 사용량의 해당 기록을 보고를 쿼리의 세부 정보로 드릴 다운 합니다.
- 수행한 작업을 보여 주는 성능 튜닝 권장 사항에 액세스할 [SQL Database Advisor](sql-database-advisor.md)합니다.

### <a name="elastic-database-transaction-units-edtus"></a>Elastic database 트랜잭션 단위 (Edtu)

항상 사용할 수 있는 SQL 데이터베이스의 경우 대신 전용된 하지 항상 필요할 수 있는, 리소스 (Dtu) 집합을 제공 하는 보다 배치할 수 있습니다 이러한 데이터베이스는 [탄력적 풀](sql-database-elastic-pool.md)합니다. 탄력적 풀의 데이터베이스는 단일 Azure SQL Database 서버 및 리소스 풀을 공유 합니다.

탄력적 풀의 공유 리소스는 elastic database 트랜잭션 단위 (Edtu) 하 여 측정 됩니다. 탄력적 풀에 광범위 하 고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대 한 성능 목표를 관리 하기는 간단 하 고 비용 효율적인 솔루션을 제공 합니다. 탄력적 풀은 풀의 각 데이터베이스에서 사용할 수 있는 필요한 리소스의 최소 크기를 항상에 있는지 확인 하는 동안 모든 리소스 풀의 데이터베이스에서 사용할 수 없습니다를 보장 합니다.

풀에는 정해진 가격으로 정해진 개수의 eDTU가 제공됩니다. 탄력적 풀의 개별 데이터베이스에 구성된 된 경계 내에서 자동 크기 조정 수 있습니다. 더 많은 부하 상태에서 데이터베이스 요구에 맞게 더 많은 Edtu 사용 합니다. 데이터베이스 가벼운 부하에서 더 적은 수의 Edtu를 사용 합니다. 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 리소스에는 데이터베이스당 아닌 전체 풀에 대 한 프로 비전 되 면 있으므로 탄력적 풀 관리 작업을 간소화 하 고 풀에 대 한 예측 가능한 예산을 제공 합니다.

추가 Edtu 풀에서 데이터베이스에 영향을 주지와 데이터베이스 가동 중지 시간 없이 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 Edtu를 더 이상 필요한 경우 제거할 기존 풀에서 언제 든 지. 데이터베이스를 추가 하거나 언제 든 지 풀에서 데이터베이스를 뺄 수도 있습니다. 다른 데이터베이스에 대 한 Edtu를 예약 하기 Edtu는 데이터베이스의 수를 제한 부하가 사용할 수 있습니다. 데이터베이스 리소스를 일관 되 게 underuses를 풀 밖으로 이동 하 고 예측 가능한 양의 필요한 리소스를 사용 하 여 단일 데이터베이스로 구성 합니다.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>워크로드에 필요한 DTU 수 결정

기존 마이그레이션하려는 경우 온-프레미스 또는 Azure SQL database를 사용 하 여 SQL Server 가상 머신 워크 로드를 [DTU 계산기](https://dtucalculator.azurewebsites.net/) 필요한 Dtu의 수를 예상 하도록 합니다. 기존 Azure SQL Database 워크 로드를 사용 하 여 [쿼리 성능을 insights](sql-database-query-performance.md) 에 데이터베이스 리소스 사용 (Dtu)를 이해 하 고 워크 로드를 최적화 하기 위한 심층적으로 통찰 하 합니다. 합니다 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 동적 관리 뷰 (DMV) 지난 1 시간 동안 리소스 사용량을 볼 수 있습니다. 합니다 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 카탈로그 뷰는 지난 14 일간 있지만 5 분 동안 평균 낮은 신뢰할 수 있는 리소스 소비를 표시 합니다.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>탄력적 리소스 풀의 이점을 활용할 수 있는 워크로드

풀은 리소스 사용률이 낮은 평균 및 상대적으로 사용률 급증을 사용 하 여 데이터베이스에 적합 합니다. SQL Database는 자동으로 기존 SQL Database 서버에 데이터베이스의 과거 리소스 사용량을 평가 하 고 Azure portal에서 적절 한 풀 구성을 권장 합니다. 자세한 내용은 [고려해 야는 SQL Database 탄력적 풀?](sql-database-elastic-pool.md)합니다.

## <a name="frequently-asked-questions-faqs"></a>FAQ(질문과 대답)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>내 응용 프로그램 오프 라인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 변환 하는 데 하나요?

아니요. 응용 프로그램을 오프 라인으로 전환 하지 않아도 됩니다. 새 서비스 계층을 표준에서 프리미엄 서비스 계층을 해결 하는 다른 방법은 데이터베이스를 업그레이드 하는 기존 프로세스와 비슷한 간단한 온라인 변환 방법을 제공 합니다. Azure portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용 하 여이 변환을 시작할 수 있습니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>으로 변환할 수는 데이터베이스 vCore 기반 구매 모델의 서비스 계층에서 DTU 기반 구매 모델의 서비스 계층?

예, 쉽게 변환할 수 있습니다 데이터베이스를 모든 지원 되는 성능 목표로 Azure portal, PowerShell, Azure CLI, T-SQL 또는 REST API를 사용 하 여 합니다. [단일 데이터베이스 관리](sql-database-single-database-scale.md) 및 [탄력적 풀 관리](sql-database-elastic-pool.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- VCore 기반 구매 모델에 대 한 자세한 내용은 참조 하세요. [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)합니다.
- DTU 기반 구매 모델에 대 한 자세한 내용은 참조 하세요. [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)합니다.
