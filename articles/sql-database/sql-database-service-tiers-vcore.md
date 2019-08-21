---
title: Azure SQL Database 서비스 - vCore | Microsoft Docs
description: VCore 기반 구매 모델을 사용 하면 계산 및 저장소 리소스를 독립적으로 확장 하 고, 온-프레미스 성능과 일치 하 고, 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: a23f71a38324d9751846f1308f79d3a4e746fd85
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637277"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>VCore 서비스 계층 중에서 선택 하 고 DTU 서비스 계층에서 마이그레이션

VCore (가상 코어) 기반 구매 모델을 사용 하면 계산 및 저장소 리소스를 독립적으로 확장 하 고, 온-프레미스 성능과 일치 하 고, 가격을 최적화할 수 있습니다. 또한 하드웨어 세대를 선택할 수 있습니다.

- **Gen4**: Intel E5-2673 v3 (Haswell) 2.4 GHz 프로세서, vCore = 1mb (물리적 코어), 코어 당 7GB, 연결 된 SSD를 기반으로 하는 최대 24 개의 논리 Cpu
- **Gen5**: Intel E5-2673 v4 (Broadwell) 2.3 GHz 프로세서, vCore = 1 LP (하이퍼 스레드), 5.1 GB/코어, fast eNVM SSD를 기반으로 하는 최대 80 논리 Cpu

4세대 하드웨어는 실질적으로 vCore당 더 많은 메모리를 제공합니다. 그러나 5세대 하드웨어를 사용하면 컴퓨팅 리소스를 훨씬 더 강화할 수 있습니다.

> [!IMPORTANT]
> 새 Gen4 데이터베이스는 오스트레일리아 동부 또는 브라질 남부 지역에서 더 이상 지원 되지 않습니다.
> [!NOTE]
> DTU 기반 서비스 계층에 대 한 자세한 내용은 [dtu 기반 구매 모델에 대 한 서비스 계층](sql-database-service-tiers-dtu.md)을 참조 하세요. DTU 기반 및 vCore 기반 구매 모델에 대 한 서비스 계층 간의 차이점에 대 한 자세한 내용은 [Azure SQL Database 구매 모델](sql-database-purchase-models.md)을 참조 하세요.

## <a name="service-tier-characteristics"></a>서비스 계층 특징

VCore 기반 구매 모델은 세 가지 서비스 계층, 즉 범용, 하이퍼 규모 및 중요 비즈니스용 서비스 계층을 제공 합니다. 이러한 서비스 계층은 다양 한 계산 크기, 고가용성 디자인, 내결함성 방법, 저장소의 형식 및 크기 및 i/o 범위로 구분 됩니다.

백업에 필요한 스토리지와 보존 기간을 개별적으로 구성해야 합니다. 백업 보존 기간을 설정 하려면 Azure Portal을 열고 서버 (데이터베이스가 아님)로 이동한 다음 **백업** > 관리로 이동 합니다.**정책** > 지정**시간 복원 구성** > **7- 35 일**.

다음 표에서는 세 계층 간의 차이점을 설명 합니다.

||**범용**|**업무상 중요**|**대규모**|
|---|---|---|---|
|가장 적합한 대상|대부분의 비즈니스 워크로드. 는 예산 지향적이 고 균형이 조정 되며 확장 가능한 계산 및 저장소 옵션을 제공 합니다.|I/o 요구 사항이 높은 비즈니스 응용 프로그램입니다. 에서는 여러 개의 격리 된 복제본을 사용 하 여 오류에 대 한 가장 높은 복원 력을 제공 합니다|확장성이 뛰어난 저장소 및 읽기 확장 요구 사항에 대 한 대부분의 비즈니스 워크 로드.|
|컴퓨팅|**프로 비전 된 계산**:<br/>Gen4: 1 개에서 24 개 까지의 vCores<br/>Gen5: 2 ~ 80 vCores<br/>**서버**를 사용 하지 않는 계산:<br/>Gen5: 0.5-4 개 vCores|**프로 비전 된 계산**:<br/>Gen4: 1 개에서 24 개 까지의 vCores<br/>Gen5: 2 ~ 80 vCores|**프로 비전 된 계산**:<br/>Gen4: 1 개에서 24 개 까지의 vCores<br/>Gen5: 2 ~ 80 vCores|
|메모리|**프로 비전 된 계산**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB<br/>**서버**를 사용 하지 않는 계산:<br/>Gen5: vCore당 3GB|**프로 비전 된 계산**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB |**프로 비전 된 계산**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB|
|저장 공간|원격 저장소를 사용 합니다.<br/>**프로 비전 된 단일 데이터베이스 계산**:<br/>5GB~4TB<br/>서버를 사용 하지 않는 **단일 데이터베이스 계산**:<br/>5GB-1TB<br/>**관리 되는 인스턴스**: 32GB~8TB |로컬 SSD 저장소를 사용 합니다.<br/>**프로 비전 된 단일 데이터베이스 계산**:<br/>5GB~4TB<br/>**관리 되는 인스턴스**:<br/>32GB~4TB |필요에 따라 저장소를 유연 하 게 자동 증가 는 최대 100 TB의 저장소를 지원 합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 저장소에 로컬 SSD 저장소를 사용 합니다. Azure 원격 저장소를 최종 장기 데이터 저장소로 사용 합니다. |
|I/o 처리량 (근사치)|**단일 데이터베이스**: 7000 최대 IOPS를 사용 하는 vCore 당 500 IOPS<br/>**관리 되는 인스턴스**: [파일의 크기](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)에 따라 달라 집니다.|vCore당 5000 IOPS(최대 200,000 IOPS)|Hyperscale은 여러 수준에서 캐싱을 사용 하는 다중 계층 아키텍처입니다. 유효 IOPs는 워크 로드에 따라 달라 집니다.|
|가용성|복제본 1 개, 읽기 확장 복제본 없음|3개 복제본, 1개 [읽기 크기 조정 복제본](sql-database-read-scale-out.md),<br/>영역 중복 HA (고가용성)|1 읽기/쓰기 복제본 및 0-4 [읽기 확장 복제본](sql-database-read-scale-out.md)|
|예비|[읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 일 (기본적으로 7 일)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35일(기본값: 7일)|Azure 원격 저장소의 스냅숏 기반 백업 복원은 빠른 복구를 위해 이러한 스냅샷을 사용합니다. 백업은 즉시 수행 되며 계산 i/o 성능에 영향을 주지 않습니다. 복원은 빠르게 수행 하 고 데이터의 크기를 조정 하는 작업이 아닙니다 (몇 시간 또는 몇 일이 아닌 분 소요).|
|메모리 내|지원되지 않음|지원됨|지원되지 않음|
|||

> [!NOTE]
> Azure 무료 계정과 함께 기본 서비스 계층에서 무료 Azure SQL database를 다운로드할 수 있습니다. 자세한 내용은 [Azure 무료 계정을 사용 하 여 관리 되는 클라우드 데이터베이스 만들기](https://azure.microsoft.com/free/services/sql-database/)를 참조 하세요.

- VCore 리소스 제한에 대 한 자세한 내용은 [단일 데이터베이스의 Vcore 리소스 제한](sql-database-vcore-resource-limits-single-databases.md) 및 [관리 되는 인스턴스의 vcore 리소스 제한](sql-database-managed-instance.md#vcore-based-purchasing-model)을 참조 하세요.
- 범용 및 중요 비즈니스용 서비스 계층에 대 한 자세한 내용은 [범용 및 중요 비즈니스용 서비스 계층](sql-database-service-tiers-general-purpose-business-critical.md)을 참조 하세요.
- Vcore 기반 구매 모델의 하이퍼 크기 조정 서비스 계층에 대 한 자세한 내용은 [대규모 service 계층](sql-database-service-tier-hyperscale.md)을 참조 하세요.  

## <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서는 [SQL Server에 대 한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 SQL Database에 대 한 할인 된 요금으로 기존 라이선스를 교환할 수 있습니다. 이 Azure 혜택을 통해 소프트웨어 보증이 있는 온-프레미스 SQL Server 라이선스를 사용 하 여 Azure SQL Database 최대 30%까지 절약할 수 있습니다.

![pricing](./media/sql-database-service-tiers/pricing.png)

Azure 하이브리드 혜택를 사용 하면 SQL database 엔진 자체 (기본 계산 가격 책정)에 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 비용을 지불 하도록 선택 하거나 기본 인프라와 SQL Server에 대해 비용을 지불할 수 있습니다. 라이선스 (라이선스 포함 가격).

Azure Portal 또는 다음 Api 중 하나를 사용 하 여 라이선스 모델을 선택 하거나 변경할 수 있습니다.

- PowerShell을 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- REST API를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [데이터베이스 - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [데이터베이스 - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU 기반 모델에서 vCore 기반 모델로 마이그레이션

### <a name="migrate-a-database"></a>데이터베이스 마이그레이션

Dtu 기반 구매 모델에서 vCore 기반 구매 모델로 데이터베이스를 마이그레이션하는 것은 DTU 기반 구매 모델에서 standard 및 premium 서비스 계층 간에 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다.

### <a name="migrate-databases-with-geo-replication-links"></a>지역에서 복제 링크를 사용 하 여 데이터베이스 마이그레이션

DTU 기반 모델에서 vCore 기반 구매 모델로 마이그레이션하는 것은 standard 및 premium 서비스 계층에 있는 데이터베이스 간의 지역에서 복제 관계를 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다. 마이그레이션 중에 지역에서 복제를 중지할 필요는 없지만 다음 시퀀싱 규칙을 따라야 합니다.

- 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드해야 합니다.
- 다운그레이드하는 경우 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 보조 데이터베이스를 다운그레이드해야 합니다.

두 탄력적 풀 간의 지역에서 복제를 사용 하는 경우 하나의 풀을 기본 풀로 지정 하 고 다른 풀을 보조 데이터베이스로 지정 하는 것이 좋습니다. 이 경우 탄력적 풀을 마이그레이션하는 경우 동일한 시퀀싱 지침을 사용 해야 합니다. 그러나 주 데이터베이스와 보조 데이터베이스를 모두 포함 하는 탄력적 풀을 사용 하는 경우 사용률이 높은 풀을 주 데이터베이스로 처리 하 고 그에 따라 시퀀싱 규칙을 따릅니다.  

다음 표에서는 특정 마이그레이션 시나리오에 대 한 지침을 제공 합니다.

|현재 서비스 계층|대상 서비스 계층|마이그레이션 유형|사용자 작업|
|---|---|---|---|
|표준|범용|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Premium|중요 비즈니스용|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|표준|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|표준|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|Premium|범용|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용|Premium|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|범용|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
||||

\*표준 계층의 모든 100 Dtu에는 vCore가 하나 이상 필요 하며, 프리미엄 계층의 모든 125 Dtu에는 vCore가 하나 이상 필요 합니다.

### <a name="migrate-failover-groups"></a>장애 조치 (failover) 그룹 마이그레이션

여러 데이터베이스가 있는 장애 조치 그룹을 마이그레이션하려면 주 데이터베이스와 보조 데이터베이스를 개별적으로 마이그레이션해야 합니다. 이 과정에서 동일한 고려 사항과 순서 지정 규칙이 적용됩니다. 데이터베이스를 vCore 기반 구매 모델로 변환한 후에는 장애 조치 (failover) 그룹이 동일한 정책 설정에 계속 적용 됩니다.

### <a name="create-a-geo-replication-secondary-database"></a>지역에서 복제 보조 데이터베이스 만들기

주 데이터베이스에 사용한 것과 동일한 서비스 계층을 사용 하 여 지역에서 복제 보조 데이터베이스 (지역 보조 데이터베이스)를 만들 수 있습니다. 로그 생성 비율이 높은 데이터베이스의 경우 주 데이터베이스와 동일한 계산 크기로 지역 보조 데이터베이스를 만드는 것이 좋습니다.

단일 주 데이터베이스에 대 한 탄력적 풀에서 지역 보조 데이터베이스를 만드는 경우 풀에 대 한 `maxVCore` 설정이 주 데이터베이스의 계산 크기와 일치 하는지 확인 합니다. 다른 탄력적 풀에서 주 데이터베이스에 대 한 지역 보조 데이터베이스를 만드는 경우 풀에 동일한 `maxVCore` 설정을 설정 하는 것이 좋습니다.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>데이터베이스 복사본을 사용 하 여 DTU 기반 데이터베이스를 vCore 기반 데이터베이스로 변환

대상 컴퓨팅 크기가 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 경우 제한 또는 특별한 순서 지정 없이 DTU 기반 컴퓨팅 크기의 데이터베이스를 vCore 기반 컴퓨팅 크기의 데이터베이스에 복사할 수 있습니다. 데이터베이스 복사본은 복사 작업을 시작할 때 데이터의 스냅숏을 만들고 원본과 대상 간의 데이터를 동기화 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-single-databases.md)을 참조 하세요.
- 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목에 대해서는 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)을 참조 하세요.
