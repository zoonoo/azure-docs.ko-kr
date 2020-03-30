---
title: 범용 및 비즈니스 크리티컬
description: 이 문서에서는 vCore 기반 구매 모델의 범용 목적 및 비즈니스 크리티컬 서비스 계층에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937849"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층

Azure SQL Database는 인프라 장애가 있더라도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 맞게 조정된 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 세 가지 서비스 계층은 각각 다른 아키텍처 모델을 가진 Azure SQL Database에서 사용됩니다. 이러한 서비스 계층은 다음과 같습니다.

- 예산 지향 워크로드를 위해 설계된 [범용.](sql-database-service-tier-general-purpose.md)
- [하이퍼스케일은](sql-database-service-tier-hyperscale.md)대부분의 비즈니스 워크로드에 맞게 설계되어 확장성이 뛰어난 스토리지, 읽기 확장 및 빠른 데이터베이스 복원 기능을 제공합니다.
- [비즈니스 크리티컬(business critical)은](sql-database-service-tier-business-critical.md)고장 및 빠른 장애 에 대한 복원력으로 대기 시간이 짧은 워크로드를 위해 설계되었습니다.

이 문서에서는 vCore 기반 구매 모델의 범용 및 비즈니스 중요 서비스 계층에 대한 서비스 계층, 저장소 및 백업 고려 사항의 차이점에 대해 설명합니다.

## <a name="service-tier-comparison"></a>서비스 계층 비교

다음 표에서는 최신 세대(Gen5)에 대한 서비스 계층 간의 주요 차이점에 대해 설명합니다. 서비스 계층 특성은 단일 데이터베이스 및 관리되는 인스턴스에서 다를 수 있습니다.

| | 리소스 유형 | 범용 |  하이퍼스케일 | 중요 비즈니스용 |
|:---:|:---:|:---:|:---:|:---:|
| **적합한 대상** | |  예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다. | 대부분의 비즈니스 워크로드. 최대 100TB의 스토리지 크기를 자동 크기 조정, 유체 수직 및 수평 계산 크기 조정, 빠른 데이터베이스 복원. | 트랜잭션 속도가 높고 IO 대기 시간이 낮은 OLTP 응용 프로그램입니다. 여러 동기적으로 업데이트된 복제본을 사용하여 장애 및 빠른 장애 조치(failover)에 대한 최고의 복원력을 제공합니다.|
|  **리소스 유형에서 사용 가능:** ||단일 데이터베이스/탄력적 풀/관리되는 인스턴스 | 단일 데이터베이스 | 단일 데이터베이스/탄력적 풀/관리되는 인스턴스 |
| **컴퓨팅 크기**|단일 데이터베이스/탄력적 풀 | vCore 1~80개 | 1 ~ 80 vCores | vCore 1~80개 |
| | 관리되는 인스턴스 | 4, 8, 16, 24, 32, 40, 64, 80 vCores | 해당 없음 | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | 관리되는 인스턴스 풀 | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | 해당 없음 | 해당 없음 |
| **스토리지 유형** | 모두 | 프리미엄 원격 스토리지(인스턴스별) | 로컬 SSD 캐시를 사용한 분리형 스토리지(인스턴스별) | 초고속 로컬 SSD 스토리지(인스턴스별) |
| **데이터베이스 크기** | 단일 데이터베이스/탄력적 풀 | 5GB~4TB | 최대 100TB | 5GB~4TB |
| | 관리되는 인스턴스  | 32GB~8TB | 해당 없음 | 32GB~4TB |
| **스토리지 크기** | 단일 데이터베이스/탄력적 풀 | 5GB~4TB | 최대 100TB | 5GB~4TB |
| | 관리되는 인스턴스  | 32GB~8TB | 해당 없음 | 32GB~4TB |
| **온도DB 크기** | 단일 데이터베이스/탄력적 풀 | [vCore당 32GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [vCore당 32GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [vCore당 32GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 관리되는 인스턴스  | [vCore당 24GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 해당 없음 | 최대 4TB - [스토리지 크기에 따라 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **로그 쓰기 처리량** | 단일 데이터베이스 | [vCore당 1.875MB/s(최대 30MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100MB/초 | [vCore당 6MB/s(최대 96MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 관리되는 인스턴스 | [vCore당 3MB/s(최대 22MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 해당 없음 | [vcore당 4MB/s(최대 48MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**가용성**|모두| 99.99% |  [보조 복제본 1개99.95%, 복제본 99.99%](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [영역 중복 단일 데이터베이스99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backup**|모두|RA-GRS, 7-35일(기본값: 7일)| RA-GRS, 7일, 일정한 시점-시간 복구(PITR) | RA-GRS, 7-35일(기본값: 7일) |
|**인메모리 OLTP** | | 해당 없음 | 해당 없음 | 사용 가능 |
|**읽기 전용 복제본**| | 0 내장 <br> 0 - 4 [지역 복제](sql-database-active-geo-replication.md) 사용 | 0 - 4 내장 | 1 내장, 가격에 포함 <br> 0 - 4 [지역 복제](sql-database-active-geo-replication.md) 사용 |
|**가격/청구** | 단일 데이터베이스 | [vCore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/single/) 청구됩니다. <br/>IOPS는 청구되지 않습니다. | [각 복제본 및 사용된 저장소에 대한 vCore요금이](https://azure.microsoft.com/pricing/details/sql-database/single/) 청구됩니다. <br/>IOPS가 아직 청구되지 않았습니다. | [vCore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/single/) 청구됩니다. <br/>IOPS는 청구되지 않습니다. |
|| 관리되는 인스턴스 | [vCore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/managed/) 충전됩니다. <br/>IOPS는 청구되지 않습니다.| 해당 없음 | [vCore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/managed/) 충전됩니다. <br/>IOPS는 청구되지 않습니다.| 
|**할인 모델**| | [예약된 인스턴스](sql-database-reserved-capacity.md)<br/>[Azure 하이브리드](sql-database-azure-hybrid-benefit.md) 혜택(개발/테스트 구독에서는 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독| [Azure 하이브리드](sql-database-azure-hybrid-benefit.md) 혜택(개발/테스트 구독에서는 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독| [예약된 인스턴스](sql-database-reserved-capacity.md)<br/>[Azure 하이브리드](sql-database-azure-hybrid-benefit.md) 혜택(개발/테스트 구독에서는 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종량제](https://azure.microsoft.com/offers/ms-azr-0023p/) 개발/테스트 구독|

자세한 내용은 [단일 데이터베이스(vCore),](sql-database-vcore-resource-limits-single-databases.md) [단일 데이터베이스 풀(vCore),](sql-database-dtu-resource-limits-single-databases.md) [단일 데이터베이스(DTU)](sql-database-dtu-resource-limits-single-databases.md)및 [단일 데이터베이스 풀(DTU)](sql-database-dtu-resource-limits-single-databases.md)및 [관리되는 인스턴스](sql-database-managed-instance-resource-limits.md) 페이지의 서비스 계층 간의 자세한 차이점을 참조하십시오.

> [!NOTE]
> vCore 기반 구매 모델의 하이퍼스케일 서비스 계층에 대한 자세한 내용은 [하이퍼스케일 서비스 계층을](sql-database-service-tier-hyperscale.md)참조하십시오. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 스토리지

다음 요소는 데이터 및 로그 파일에 사용되는 저장소의 양에 영향을 미치며 범용 및 비즈니스 중요도에 적용됩니다. 하이퍼스케일의 데이터 및 로그 스토리지에 대한 자세한 내용은 [하이퍼스케일 서비스 계층을](sql-database-service-tier-hyperscale.md)참조하십시오.

- 할당된 저장소는 데이터 파일(MDF) 및 로그 파일(LDF)에서 사용됩니다.
- 각 단일 데이터베이스 계산 크기는 기본 최대 크기가 32GB인 최대 데이터베이스 크기를 지원합니다.
- 필요한 단일 데이터베이스 크기(MDF 파일 크기)를 구성하면 LDF 파일을 지원하기 위해 30% 더 많은 추가 저장소가 자동으로 추가됩니다.
- SQL Database 관리 인스턴스의 저장소 크기는 32GB의 배수로 지정해야 합니다.
- 10GB에서 지원되는 최대 값 사이의 단일 데이터베이스 크기를 선택할 수 있습니다.
  - 표준 또는 범용 서비스 계층의 저장소의 경우 10GB 단위로 크기를 늘리거나 줄입니다.
  - 프리미엄 또는 비즈니스 크리티컬 서비스 계층의 스토리지의 경우 250GB 단위로 크기를 늘리거나 줄입니다.
- 범용 서비스 계층에서 `tempdb` 연결된 SSD를 사용하며 이 저장소 비용은 vCore 가격에 포함됩니다.
- 비즈니스 크리티컬 서비스 `tempdb` 계층에서 연결된 SSD를 MDF 및 LDF 파일과 공유하며 `tempdb` 스토리지 비용은 vCore 가격에 포함됩니다.

> [!IMPORTANT]
> MDF 및 LDF 파일에 할당된 총 저장소에 대한 요금이 부과됩니다.

MDF 및 LDF 파일의 현재 총 크기를 모니터링하려면 [sp_spaceused.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 파일 공간 관리를](sql-database-file-space-management.md)참조하십시오.

## <a name="backups-and-storage"></a>백업 및 스토리지

데이터베이스 백업을 위한 저장소는 SQL Database의 적시 복원(PITR) 및 [장기 보존(LTR)](sql-database-long-term-retention.md) 기능을 지원하기 위해 할당됩니다. 이 스토리지는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다.

- **PITR**: 개별 데이터베이스 백업이 [복사되어 읽기 액세스 지리적 중복(RA-GRS) 저장소가](../storage/common/storage-designing-ha-apps-with-ragrs.md) 자동으로 복사됩니다. 새 백업이 생성되면 저장소 크기가 동적으로 증가합니다. 저장소는 매주 전체 백업, 일일 차동 백업 및 트랜잭션 로그 백업에서 사용되며 5분마다 복사됩니다. 저장소 사용량은 데이터베이스 변경 속도와 백업 보존 기간에 따라 달라집니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터베이스 크기의 100%(1x)에 해당하는 최소 저장소 용량은 추가 비용 없이 제공됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database는 최대 10년 동안 전체 백업을 장기적으로 보존하는 옵션을 제공합니다. LTR 정책을 설정하면 이러한 백업이 RA-GRS 저장소에 자동으로 저장되지만 백업이 복사되는 빈도를 제어할 수 있습니다. 다양한 규정 준수 요구 사항을 충족하려면 주간, 월별 및/또는 연간 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 선택한 구성에 따라 LTR 백업에 사용할 저장소의 양이 결정됩니다. LTR 스토리지 비용을 추정하려면 LTR 가격 계산기를 사용할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 장기 보존](sql-database-long-term-retention.md)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

- 범용 및 비즈니스 크리티컬 서비스 계층의 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database vCore 기반 리소스 제한을](sql-database-vcore-resource-limits-single-databases.md)참조하십시오.
- 범용 및 비즈니스 크리티컬 서비스 계층의 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대한 자세한 내용은 [탄력적 풀에 대한 SQL Database vCore 기반 리소스 제한을](sql-database-vcore-resource-limits-elastic-pools.md)참조하십시오.
