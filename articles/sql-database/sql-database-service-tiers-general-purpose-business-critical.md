---
title: Azure SQL Database - 범용 및 중요 비즈니스용 | Microsoft Docs
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
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566702"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층

Azure SQL Database는 인프라 오류가 발생 한 경우에도 99.99%의 가용성을 보장 하기 위해 클라우드 환경에 맞게 조정 된 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 3 개의 서비스 계층은 각각 다른 아키텍처 모델을 사용 하 여 Azure SQL Database에 사용 됩니다. 이러한 서비스 계층은 다음과 같습니다.

- [일반적인 용도로](sql-database-service-tier-general-purpose.md), 대부분의 일반 작업을 위해 설계 되었습니다.
- 읽을 수 있는 복제본이 하나인 대기 시간이 짧은 워크 로드 용으로 설계 된 [업무상 중요](sql-database-service-tier-business-critical.md)합니다.
- [Hyperscale](sql-database-service-tier-hyperscale.md)은 읽기 가능한 여러 복제본이 포함 된 매우 큰 데이터베이스 (최대 100 TB) 용으로 설계 되었습니다.

이 문서에서는 vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대 한 저장소 및 백업 고려 사항을 설명 합니다.

> [!NOTE]
> Vcore 기반 구매 모델의 하이퍼 크기 조정 서비스 계층에 대 한 자세한 내용은 [대규모 service 계층](sql-database-service-tier-hyperscale.md)을 참조 하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 저장

데이터 및 로그 파일에 사용 되는 저장소 크기에 영향을 주는 요소는 다음과 같습니다.

- 할당 된 저장소는 데이터 파일 (MDF) 및 로그 파일 (LDF)에서 사용 됩니다.
- 각 단일 데이터베이스 계산 크기는 최대 데이터베이스 크기를 지원 하며 기본 최대 크기는 32 GB입니다.
- 필요한 단일 데이터베이스 크기 (MDF 파일의 크기)를 구성 하면 LDF 파일을 지원 하기 위해 30% 더 많은 추가 저장소가 자동으로 추가 됩니다.
- SQL Database 관리 되는 인스턴스의 저장소 크기는 32 GB의 배수로 지정 해야 합니다.
- 10gb와 지원 되는 최대의 단일 데이터베이스 크기를 선택할 수 있습니다.
  - Standard 또는 범용 서비스 계층의 저장소의 경우 10gb 증분으로 크기를 늘리거나 줄입니다.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층의 저장소에 대해 250-GB 증가값으로 크기를 늘리거나 줄입니다.
- 범용 서비스 계층에서는 연결 된 `tempdb` SSD를 사용 하며,이 저장소 비용은 vcore 가격에 포함 됩니다.
- 중요 비즈니스용 서비스 계층에서는 연결 `tempdb` 된 SSD와 MDF 및 LDF 파일을 공유 `tempdb` 하며, 저장소 비용은 vcore 가격에 포함 됩니다.

> [!IMPORTANT]
> MDF 및 LDF 파일에 대해 할당 된 총 저장소에 대 한 요금이 청구 됩니다.

MDF 및 LDF 파일의 현재 총 크기를 모니터링 하려면 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)를 사용 합니다. 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="backups-and-storage"></a>백업 및 저장소

데이터베이스 백업용 저장소는 SQL Database의 PITR (지정 시간 복원) 및 [LTR (장기 보존)](sql-database-long-term-retention.md) 기능을 지원 하기 위해 할당 됩니다. 이 저장소는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다.

- **PITR**: 개별 데이터베이스 백업은 [읽기 액세스 지역 중복 (GRS) 저장소](../storage/common/storage-designing-ha-apps-with-ragrs.md) 에 자동으로 복사 됩니다. 저장소 크기는 새 백업이 생성 될 때 동적으로 늘어납니다. 저장소는 매주 전체 백업, 일별 차등 백업 및 5 분 마다 복사 되는 트랜잭션 로그 백업에 사용 됩니다. 저장소 사용량은 데이터베이스의 변경 률과 백업 보존 기간에 따라 달라 집니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터베이스 크기의 100% (1x)와 같은 최소 저장소 크기는 추가 요금 없이 제공 됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database은 최대 10 년 동안 전체 백업의 장기 보존을 구성 하는 옵션을 제공 합니다. LTR 정책을 설정 하는 경우 이러한 백업은 RA GRS 저장소에 자동으로 저장 되지만 백업 복사 빈도를 제어할 수 있습니다. 서로 다른 규정 준수 요구 사항을 충족 하기 위해 주별, 월별 및/또는 매년 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 선택한 구성에 따라 LTR 백업에 사용 되는 저장소의 양이 결정 됩니다. LTR 저장소의 비용을 예상 하려면 LTR 가격 계산기를 사용할 수 있습니다. 자세한 내용은 [장기 보존 SQL Database](sql-database-long-term-retention.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 범용 및 중요 비즈니스용 서비스 계층에서 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대 한 자세한 내용은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-single-databases.md)을 참조 하세요.
- 범용 및 중요 비즈니스용 서비스 계층에서 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기에 대 한 자세한 내용은 [탄력적 풀에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요.
