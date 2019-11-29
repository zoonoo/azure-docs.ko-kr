---
title: 범용 및 업무상 중요
description: 이 문서에서는 vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대해 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: 5e3cc12351313b8fb1dedf795031202070ac7cf7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558980"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층

Azure SQL Database는 인프라 오류가 발생 한 경우에도 99.99%의 가용성을 보장 하기 위해 클라우드 환경에 맞게 조정 된 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 3 개의 서비스 계층은 각각 다른 아키텍처 모델을 사용 하 여 Azure SQL Database에 사용 됩니다. 이러한 서비스 계층은 다음과 같습니다.

- 예산 중심 작업을 위해 설계 [된 범용입니다](sql-database-service-tier-general-purpose.md).
- 매우 확장 가능한 저장소, 읽기 확장 및 빠른 데이터베이스 복원 기능을 제공 하는 대부분의 비즈니스 워크 로드에 맞게 설계 된 [Hyperscale](sql-database-service-tier-hyperscale.md)
- [중요 비즈니스용](sql-database-service-tier-business-critical.md)으로, 오류 및 빠른 장애 조치 (failover)에 대 한 복원 력이 높은 짧은 대기 시간 워크 로드 용으로 설계 되었습니다.

이 문서에서는 vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대 한 서비스 계층, 저장소 및 백업 고려 사항에 대해 설명 하는 차이점에 대해 설명 합니다.

## <a name="service-tier-comparison"></a>서비스 계층 비교

다음 표에서는 최신 세대에 대 한 서비스 계층의 주요 차이점에 대해 설명 합니다 (Gen5). 서비스 계층 특성은 Single Database 및 Managed Instance에서 다를 수 있습니다.

| | 리소스 종류 | 일반적인 용도 |  하이퍼스케일 | 중요 비즈니스 |
|:---:|:---:|:---:|:---:|:---:|
| **적합한 대상** | |  예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다. | 대부분의 비즈니스 워크로드. 자동 크기 조정 저장소 크기는 최대 100 TB, 유체 수직 및 수평 계산 크기 조정, 빠른 데이터베이스 복원입니다. | 트랜잭션 속도가 높고 IO 대기 시간이 낮은 OLTP 응용 프로그램 는 동시에 업데이트 된 여러 복제본을 사용 하 여 오류 및 빠른 장애 조치에 가장 높은 복원 력을 제공 합니다|
|  **리소스 종류에서 사용 가능:** ||단일 데이터베이스/탄력적 풀/관리되는 인스턴스 | 단일 데이터베이스 | 단일 데이터베이스/탄력적 풀/관리되는 인스턴스 |
| **컴퓨팅 크기**|단일 데이터베이스/탄력적 풀 | vCore 1~80개 | 1 ~ 80 vCores | vCore 1~80개 |
| | Managed Instance | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | 관리 되는 인스턴스 풀 | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | N/A |
| **스토리지 유형** | 전체 | 프리미엄 원격 스토리지(인스턴스별) | 로컬 SSD 캐시를 사용한 분리형 스토리지(인스턴스별) | 초고속 로컬 SSD 스토리지(인스턴스별) |
| **데이터베이스 크기** | 단일 데이터베이스/탄력적 풀 | 5GB~4TB | 최대 100TB | 5GB~4TB |
| | Managed Instance  | 32GB~8TB | N/A | 32GB~4TB |
| **스토리지 크기** | 단일 데이터베이스/탄력적 풀 | 5GB~4TB | 최대 100TB | 5GB~4TB |
| | Managed Instance  | 32GB~8TB | N/A | 32GB~4TB |
| **TempDB 크기** | 단일 데이터베이스/탄력적 풀 | [vCore 당 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [vCore 당 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [vCore 당 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Managed Instance  | [vCore 당 24gb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [저장소 크기에 따라](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) 최대 4 TB 제한 |
| **로그 쓰기 처리량** | 단일 데이터베이스 | [vCore 당 1.875 m b/초 (최대 30 m b/초)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100MB/초 | [vCore 당 6mb/s (최대 96 m b/초)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Managed Instance | [vCore 당 3MB/s (최대 22 m b/초)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [vcore 당 4mb/s (최대 48 m b/초)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**가용성**|전체| 99.99% |  [보조 복제본이 하나인 99.95%, 추가 복제본이 있는 99.99%](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [영역 중복 단일 데이터베이스가 포함 된 99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**백업**|전체|RA-GRS, 7-35일(기본값: 7일)| RA-GRS, 7 일, 상수 시간 지정 시간 복구 (PITR) | RA-GRS, 7-35일(기본값: 7일) |
|**메모리 내 OLTP** | | N/A | N/A | 사용할 수 있음 |
|**읽기 전용 복제본**| | 0  | 0 - 4 | 1 (기본 제공, 가격에 포함 됨) |
|**가격 책정/청구** | 단일 데이터베이스 | [Vcore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/single/) 청구 됩니다. <br/>IOPS에는 요금이 부과 되지 않습니다. | [각 복제본 및 사용 된 저장소에 대 한 Vcore에](https://azure.microsoft.com/pricing/details/sql-database/single/) 는 요금이 부과 됩니다. <br/>IOPS는 아직 청구 되지 않습니다. | [Vcore, 예약 된 저장소 및 백업 저장소가](https://azure.microsoft.com/pricing/details/sql-database/single/) 청구 됩니다. <br/>IOPS에는 요금이 부과 되지 않습니다. |
|| 관리되는 인스턴스 | [Vcore 및 예약 된 저장소](https://azure.microsoft.com/pricing/details/sql-database/managed/) 에는 요금이 부과 됩니다. <br/>IOPS에는 요금이 부과 되지 않습니다.<br/>백업 저장소에 아직 요금이 청구 되지 않았습니다. | N/A | [Vcore 및 예약 된 저장소](https://azure.microsoft.com/pricing/details/sql-database/managed/) 에는 요금이 부과 됩니다. <br/>IOPS에는 요금이 부과 되지 않습니다.<br/>백업 저장소에 아직 요금이 청구 되지 않았습니다. | 
|**할인 모델**| | [예약 인스턴스](sql-database-reserved-capacity.md)<br/>[Azure 하이브리드 혜택](sql-database-azure-hybrid-benefit.md) (개발/테스트 구독에서 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종 량](https://azure.microsoft.com/offers/ms-azr-0023p/) 제 개발/테스트 구독| [Azure 하이브리드 혜택](sql-database-azure-hybrid-benefit.md) (개발/테스트 구독에서 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종 량](https://azure.microsoft.com/offers/ms-azr-0023p/) 제 개발/테스트 구독| [예약 인스턴스](sql-database-reserved-capacity.md)<br/>[Azure 하이브리드 혜택](sql-database-azure-hybrid-benefit.md) (개발/테스트 구독에서 사용할 수 없음)<br/>[엔터프라이즈](https://azure.microsoft.com/offers/ms-azr-0148p/) 및 [종 량](https://azure.microsoft.com/offers/ms-azr-0023p/) 제 개발/테스트 구독|

자세한 내용은 [단일 데이터베이스 (vcore)](sql-database-vcore-resource-limits-single-databases.md), 단일 [데이터베이스 풀 (vcore)](sql-database-dtu-resource-limits-single-databases.md), [단일 데이터베이스 (dtu](sql-database-dtu-resource-limits-single-databases.md)), [dtu (단일 데이터베이스 풀)](sql-database-dtu-resource-limits-single-databases.md)및 [Managed Instance](sql-database-managed-instance-resource-limits.md) 페이지의 서비스 계층 간의 자세한 차이점을 참조 하세요.

> [!NOTE]
> Vcore 기반 구매 모델의 하이퍼 크기 조정 서비스 계층에 대 한 자세한 내용은 [대규모 service 계층](sql-database-service-tier-hyperscale.md)을 참조 하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 스토리지

다음 요소는 데이터 및 로그 파일에 사용 되는 저장소의 양에 영향을 주며 범용 및 중요 비즈니스용에 적용 됩니다. Hyperscale의 데이터 및 로그 저장소에 대 한 자세한 내용은 [hyperscale service 계층](sql-database-service-tier-hyperscale.md)을 참조 하세요.

- 할당 된 저장소는 데이터 파일 (MDF) 및 로그 파일 (LDF)에서 사용 됩니다.
- 각 단일 데이터베이스 계산 크기는 최대 데이터베이스 크기를 지원 하며 기본 최대 크기는 32 GB입니다.
- 필요한 단일 데이터베이스 크기 (MDF 파일의 크기)를 구성 하면 LDF 파일을 지원 하기 위해 30% 더 많은 추가 저장소가 자동으로 추가 됩니다.
- SQL Database 관리 되는 인스턴스의 저장소 크기는 32 GB의 배수로 지정 해야 합니다.
- 10gb와 지원 되는 최대의 단일 데이터베이스 크기를 선택할 수 있습니다.
  - Standard 또는 범용 서비스 계층의 저장소의 경우 10gb 증분으로 크기를 늘리거나 줄입니다.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층의 저장소에 대해 250-GB 증가값으로 크기를 늘리거나 줄입니다.
- 범용 서비스 계층에서 `tempdb` 연결 된 SSD를 사용 하며,이 저장소 비용은 vCore 가격에 포함 됩니다.
- 업무상 중요 한 서비스 계층에서 `tempdb`는 연결 된 SSD를 MDF 및 LDF 파일과 공유 하 고 `tempdb` 저장소 비용은 vCore 가격에 포함 됩니다.

> [!IMPORTANT]
> MDF 및 LDF 파일에 대해 할당 된 총 저장소에 대 한 요금이 청구 됩니다.

MDF 및 LDF 파일의 현재 총 크기를 모니터링 하려면 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)을 사용 합니다. 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="backups-and-storage"></a>백업 및 스토리지

데이터베이스 백업용 저장소는 SQL Database의 PITR (지정 시간 복원) 및 [LTR (장기 보존)](sql-database-long-term-retention.md) 기능을 지원 하기 위해 할당 됩니다. 이 스토리지는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다.

- **PITR**: 개별 데이터베이스 백업은 [읽기 액세스 지역 중복 (RA-GRS) 저장소](../storage/common/storage-designing-ha-apps-with-ragrs.md) 로 자동 복사 됩니다. 저장소 크기는 새 백업이 생성 될 때 동적으로 늘어납니다. 저장소는 매주 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에 사용 됩니다. 저장소 사용량은 데이터베이스의 변경 률과 백업 보존 기간에 따라 달라 집니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터베이스 크기의 100% (1x)와 같은 최소 저장소 크기는 추가 요금 없이 제공 됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database 최대 10 년 동안 전체 백업의 장기 보존을 구성 하는 옵션을 제공 합니다. LTR 정책을 설정 하는 경우 이러한 백업은 RA GRS 저장소에 자동으로 저장 되지만 백업 복사 빈도를 제어할 수 있습니다. 서로 다른 규정 준수 요구 사항을 충족 하기 위해 주별, 월별 및/또는 매년 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 선택한 구성에 따라 LTR 백업에 사용 되는 저장소의 양이 결정 됩니다. LTR 저장소의 비용을 예상 하려면 LTR 가격 계산기를 사용할 수 있습니다. 자세한 내용은 [장기 보존 SQL Database](sql-database-long-term-retention.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 범용 및 중요 비즈니스용 서비스 계층에서 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대 한 자세한 내용은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-single-databases.md)을 참조 하세요.
- 범용 및 중요 비즈니스용 서비스 계층에서 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대 한 자세한 내용은 [탄력적 풀에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요.
