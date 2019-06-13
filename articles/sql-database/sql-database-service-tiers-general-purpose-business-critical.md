---
title: Azure SQL Database - 범용 및 중요 비즈니스용 | Microsoft Docs
description: 문서에는 범용 및 중요 비즈니스용 서비스 계층에서 vCore 기반 구매 모델을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431333"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층

Azure SQL Database는 인프라 오류가 발생 하는 경우에 99.99% 가용성을 보장 하려면 클라우드 환경에 대 한 조정 되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 세 가지 서비스 계층은 각각 다른 아키텍처 모델을 사용 하 여 Azure SQL Database에 사용 됩니다. 이러한 서비스 계층은 다음과 같습니다.

- [범용](sql-database-service-tier-general-purpose.md), 가장 일반적인 워크 로드에 대 한 디자인 된 합니다.
- [중요 한 비즈니스](sql-database-service-tier-business-critical.md), 읽기 가능한 복제본 하나를 사용 하 여 대기 시간이 짧은 워크 로드에 대 한 디자인 된 합니다.
- [대규모](sql-database-service-tier-hyperscale.md), 매우 큰 데이터베이스에 대 한 디자인 된 (최대 100TB) 여러 개의 읽기 가능한 복제본을 사용 하 여 합니다.

이 문서에는 범용 및 중요 비즈니스용 서비스 계층에서 vCore 기반 구매 모델에 대 한 저장소 및 백업 고려 사항 설명합니다.

> [!NOTE]
> VCore 기반 구매 모델에서 대규모 서비스 계층에 대 한 자세한 내용은 [하이퍼 스케일 서비스 계층](sql-database-service-tier-hyperscale.md)합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 저장

다음 요소는 데이터 및 로그 파일에 사용 되는 저장소 양을 영향을 줍니다.

- 할당 된 저장소는 데이터 파일 (MDF) 및 로그 파일 (LDF)에서 사용 됩니다.
- 각 단일 데이터베이스 크기에서 지 원하는 최대 데이터베이스 크기를 32GB의 기본 최대 크기를 계산 합니다.
- 필요한 단일 데이터베이스 크기 (MDF 파일의 크기)를 구성한 경우 30% 더 추가 저장소 LDF 파일을 지원 하도록 자동으로 추가 됩니다.
- SQL Database 관리 되는 인스턴스에 대 한 저장소 크기는 32GB의 배수로 지정 되어야 합니다.
- 10GB와 지원 되는 최대 모든 단일 데이터베이스 크기를 선택할 수 있습니다.
  - 표준 또는 범용 서비스 계층의 저장소에 대 한는 크기는 10GB 증분 단위로 늘리거나 줄입니다.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층, 증가 또는 감소 크기 250 GB 단위로 저장소에 대 한 합니다.
- 범용 서비스 계층에서는 `tempdb` 연결형된 SSD, 및이 저장소의 비용이 vCore 가격에 포함 되어 사용 합니다.
- 비즈니스는 중요 한 서비스 계층 `tempdb` MDF 및 LDF 파일이 포함 된 연결형된 SSD를 공유 하며 `tempdb` 저장소 비용은 vCore 가격에 포함 됩니다.

> [!IMPORTANT]
> MDF 및 LDF 파일에 대 한 할당 된 총 저장소에 대 한 요금이 청구 됩니다.

MDF 및 LDF 파일의 현재 총 크기를 모니터링 하려면 사용 하 여 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)합니다. 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="backups-and-storage"></a>백업 및 저장소

지정 시간 복원 (PITR) 지원에 데이터베이스 백업 위한 저장소를 할당 하 고 [장기 보존 (LTR)](sql-database-long-term-retention.md) SQL Database의 기능입니다. 이 저장소는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다.

- **PITR**: 개별 데이터베이스 백업을 복사할 [읽기 액세스 지역 중복 (RA-GRS) 저장소](../storage/common/storage-designing-ha-apps-with-ragrs.md) 자동으로 합니다. 새 백업이 만들어지면 저장소 크기가 동적으로 증가 합니다. 저장소는 주별 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에서 사용 됩니다. 저장소 사용량 백업의 보존 기간은 데이터베이스의 변경 비율에 따라 달라 집니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터베이스 크기의 100% (1 x)에 최소 저장소 금액은 추가 비용 없이 제공 됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database는 최대 10 년에 대 한 전체 백업의 장기 보존을 구성 하는 옵션을 제공 합니다. LTR 정책에 설정한 경우 이러한 백업이 RA-GRS 저장소에 자동으로 저장 되 되지만 백업이 복사 되는 빈도 제어할 수 있습니다. 다양 한 규정 준수 요구 사항을 충족 하려면 주별, 월별 및/또는 연도별 백업에 대 한 다른 보존 기간을 선택할 수 있습니다. 선택한 구성에 따라 LTR 백업에 사용할 저장소의 양을 결정 합니다. LTR 저장소 비용을 계산 하려면 LTR 가격 계산기를 사용할 수 있습니다. 자세한 내용은 [SQL Database 장기 보존](sql-database-long-term-retention.md)합니다.

## <a name="next-steps"></a>다음 단계

- 특정에 대 한 세부 정보 크기 및 단일 데이터베이스 범용 및 중요 비즈니스용 서비스 계층에서 사용할 수 있는 저장소 크기를 계산, 참조 [단일 데이터베이스에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md)합니다.
- 특정에 대 한 세부 정보에는 크기 및 저장소 크기를 탄력적 풀 범용 및 중요 비즈니스용 서비스 계층에서 사용할 수 있는 계산, 참조 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md)합니다.
