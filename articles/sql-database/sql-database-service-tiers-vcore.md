---
title: Azure SQL Database 서비스 - vCore | Microsoft Docs
description: VCore 기반 구매 모델을 사용 하 여 독립적으로 계산 및 저장소 리소스를 크기 조정, 온-프레미스 성능, 일치 및 가격을 최적화할 수 있습니다.
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
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693338"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>VCore 서비스 계층 간에 선택 하 고 DTU 서비스 계층에서 마이그레이션

가상 코어 (vCore)-기반된 구매 모델 독립적으로 계산 및 저장소 리소스를 확장 하 고, 온-프레미스 성능 일치 시키고, 가격을 최적화할 수 있습니다. 또한 하드웨어 세대를 선택할 수 있습니다.

- **Gen4**: Intel E5-2673 v3에 따라 최대 논리적 cpu가 24 개 (Haswell) 2.4 GHz 프로세서, vCore = 1 PP (물리적 코어), 코어당 7GB 연결 SSD
- **Gen5**: 최대 80 논리적 Cpu 기반 Intel E5-2673 v4 (Broadwell) 2.3 GHz 프로세서, vCore = 1 LP (하이퍼 스레드), core, 고속 eNVM SSD 당 5.1 GB

4세대 하드웨어는 실질적으로 vCore당 더 많은 메모리를 제공합니다. 그러나 5세대 하드웨어를 사용하면 계산 리소스를 훨씬 더 강화할 수 있습니다.

> [!NOTE]
> DTU 기반 서비스 계층에 대 한 자세한 내용은 [서비스 계층 DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)합니다. DTU 기반 서비스 계층 및 vCore 기반 구매 모델의 차이점에 대 한 자세한 내용은 [모델을 구매 하는 Azure SQL Database](sql-database-purchase-models.md)합니다.

## <a name="service-tier-characteristics"></a>서비스 계층 특성

VCore 기반 구매 모델은 세 가지 서비스 계층을 제공: 범용, 하이퍼 스케일, 및 비즈니스에 중요 합니다. 이러한 서비스 계층은 다양 한 계산 크기, 고가용성 설계, 오류 격리 메서드, 형식 및 저장소의 크기 및 I/O 범위 구분 됩니다.

백업에 필요한 저장소와 보존 기간을 개별적으로 구성해야 합니다. 백업 보존 기간을 설정, Azure portal을 열고, (데이터베이스가 아니라) 서버로 이동으로 이동한 다음에 **백업 관리** > **정책 구성**  >   **지점에서 시간 복원 구성을** > **7-35 일 넘게**합니다.

다음 표에서 세 가지 계층 간의 차이점을 설명 합니다.

||**범용**|**중요 비즈니스용**|**Hyperscale**|
|---|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 제품 예산 지향, 분산 및 확장 가능한 계산 및 저장소 옵션입니다.|높은 I/O 요구를 사용 하 여 비즈니스 응용 프로그램입니다. 격리 된 여러 복제본을 사용 하 여 오류에 가장 높은 복원 력을 제공 합니다.|확장성이 우수한 저장소 및 읽기-배율 요구 사항을 사용 하 여 대부분의 비즈니스 워크 로드.|
|컴퓨팅|**계산을 프로 비전**:<br/>Gen4: 1 ~ 24 개 Vcore<br/>Gen5: 2 ~ 80 개 Vcore<br/>**서버 리스 계산**:<br/>Gen5: 0.5-vcore 4 개|**계산을 프로 비전**:<br/>Gen4: 1 ~ 24 개 Vcore<br/>Gen5: 2 ~ 80 개 Vcore|**계산을 프로 비전**:<br/>Gen4: 1 ~ 24 개 Vcore<br/>Gen5: 2 ~ 80 개 Vcore|
|메모리|**계산을 프로 비전**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB<br/>**서버 리스 계산**:<br/>Gen5: vCore당 3GB|**계산을 프로 비전**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB |**계산을 프로 비전**:<br/>Gen4: vCore당 7GB<br/>Gen5: vCore당 5.1GB|
|Storage|원격 저장소를 사용 합니다.<br/>**계산을 프로 비전 된 단일 데이터베이스**:<br/>5GB~4TB<br/>**단일 데이터베이스 서버 리스 계산**:<br/>5GB-1TB<br/>**관리 되는 인스턴스**: 32GB~8TB |로컬 SSD 저장소를 사용합니다.<br/>**계산을 프로 비전 된 단일 데이터베이스**:<br/>5GB~4TB<br/>**관리 되는 인스턴스**:<br/>32GB~4TB |필요에 따라 저장소의 유연한 증가 합니다. 최대 100TB의 저장소를 지원합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 저장소에 대 한 로컬 SSD 저장소를 사용합니다. 최종 장기 데이터 저장소로 Azure 원격 저장소를 사용합니다. |
|I/O 처리량 (근사치)|**단일 데이터베이스**: 7000 최대 IOPS 사용 하 여 vCore 당 500 IOPS입니다.<br/>**관리 되는 인스턴스**: 에 따라 달라 집니다 [파일의 크기](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)합니다.|vCore당 5000 IOPS(최대 200,000 IOPS)|대규모 여러 수준의 캐싱 사용 하 여 다중 계층 아키텍처를 사용 하는 경우 유효 IOPs 워크 로드에 따라 달라 집니다.|
|가용성|1 개 복제본, 읽기-배율 복제본이 없는|3개 복제본, 1개 [읽기 크기 조정 복제본](sql-database-read-scale-out.md),<br/>영역 중복 HA (고가용성)|읽기 / 쓰기 복제본 1 개 및 0-4 [읽기-배율 복제본](sql-database-read-scale-out.md)|
|Backup|[읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 일 (기본적으로 7 일)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35일(기본값: 7일)|Azure 원격 저장소의 스냅숏 기반 백업을 합니다. 복원은 빠른 복구를 위해 이러한 스냅샷을 사용합니다. 백업과 즉각적 계산 I/O 성능에 영향을 하지 않습니다. 복원 빠르며 (수행 하지 않고 시간 또는 일 분) 크기의 데이터 작업을 되지 않습니다.|
|메모리 내|지원되지 않음|지원됨|지원되지 않음|
|||

> [!NOTE]
> Azure 체험 계정와 함께에서 기본 서비스 계층에서 무료 Azure SQL 데이터베이스를 가져올 수 있습니다. 자세한 내용은 [Azure 무료 계정으로 관리 되는 클라우드 데이터베이스 만들기](https://azure.microsoft.com/free/services/sql-database/)합니다.

- VCore 리소스 제한에 대 한 자세한 내용은 참조 하세요. [단일 데이터베이스에서 vCore 리소스 제한](sql-database-vcore-resource-limits-single-databases.md) 하 고 [관리 되는 인스턴스의 리소스 제한 vCore](sql-database-managed-instance.md#vcore-based-purchasing-model)합니다.
- 범용 및 중요 비즈니스용 서비스 계층에 대 한 자세한 내용은 참조 하세요. [범용 및 중요 비즈니스용 서비스 계층](sql-database-service-tiers-general-purpose-business-critical.md)합니다.
- VCore 기반 구매 모델에서 대규모 서비스 계층에 대 한 자세한 내용은 참조 하세요. [하이퍼 스케일 서비스 계층](sql-database-service-tier-hyperscale.md)합니다.  

## <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서 사용 하 여 SQL Database에서 할인 된 요금에 대 한 기존 라이선스를 교환할 수 있습니다 [SQL Server 용 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)합니다. 이 Azure 혜택을 사용 하면 Software Assurance를 사용 하 여 온-프레미스 SQL Server 라이선스를 사용 하 여 Azure SQL Database에서 최대 30%를 저장할 수 있습니다.

![가격 책정](./media/sql-database-service-tiers/pricing.png)

Azure Hybrid Benefit을 사용 하 여 자체 (기본 계산 가격 책정), SQL 데이터베이스 엔진에 대 한 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 지불 하도록 선택할 수 있습니다. 또는 기본 인프라와 SQL Server에 대 한 요금을 지불할 수 있습니다. 라이선스 (라이선스 포함 가격 책정)입니다.

선택 하거나 Azure portal을 사용 하거나 다음 Api 중 하나를 사용 하 여 라이선스 모델을 변경할 수 있습니다.

- 설정 하거나 PowerShell을 사용 하 여 라이선스 유형을 업데이트 합니다.

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 설정 또는 Azure CLI를 사용 하 여 라이선스 유형을 업데이트 합니다.

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 설정 하거나 REST API를 사용 하 여 라이선스 유형을 업데이트 합니다.

  - [데이터베이스 - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [데이터베이스 - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU 기반 모델에서 vCore 기반 모델로 마이그레이션

### <a name="migrate-a-database"></a>데이터베이스 마이그레이션

DTU 기반 구매 모델에서 데이터베이스를 vCore 기반 구매 모델으로 업그레이드 또는 DTU 기반 구매 모델의 표준 및 프리미엄 서비스 계층 간의 다운 그레이드 하는 것과 비슷합니다.

### <a name="migrate-databases-with-geo-replication-links"></a>지역에서 복제 링크를 사용 하 여 데이터베이스 마이그레이션

DTU 기반 모델에서 vCore 기반 구매 모델에 마이그레이션 업그레이드 하거나 표준 및 프리미엄 서비스 계층의 데이터베이스 간의 지역에서 복제 관계를 다운 그레이드 하는 것과 비슷합니다. 마이그레이션 중에 지리적 복제를 중지 하려면 없지만 이러한 순서 지정 규칙을 따라야 합니다.

- 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드해야 합니다.
- 다운그레이드하는 경우 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 보조 데이터베이스를 다운그레이드해야 합니다.

두 개의 탄력적 풀 간에 지역에서 복제를 사용 중인 경우 주 및 보조 복제본으로 다른 하나의 풀을 지정 하는 것이 좋습니다. 이 경우 탄력적 풀을 마이그레이션하는 경우에 동일한 시퀀싱 지침을 사용 해야 합니다. 그러나 주 및 보조 데이터베이스를 포함 하는 탄력적 풀에 있는 경우 주 높은 사용률을 사용 하 여 풀을 처리 하 고 그에 따라 순서 지정 규칙을 따릅니다.  

다음 표에서 특정 마이그레이션 시나리오에 대 한 지침을 제공합니다.

|현재 서비스 계층|대상 서비스 계층|마이그레이션 유형|사용자 작업|
|---|---|---|---|
|Standard|범용 가상 컴퓨터|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Premium|중요 비즈니스용|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Standard|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|Standard|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|Premium|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|Premium|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
||||

\* 표준 계층의 모든 100 Dtu 하나 이상의 vCore를 하며 모든 125dtu 프리미엄 계층에서 vCore 1 개 이상 필요 합니다.

### <a name="migrate-failover-groups"></a>장애 조치 그룹 마이그레이션

여러 데이터베이스가 있는 장애 조치 그룹을 마이그레이션하려면 주 데이터베이스와 보조 데이터베이스를 개별적으로 마이그레이션해야 합니다. 이 과정에서 동일한 고려 사항과 순서 지정 규칙이 적용됩니다. 데이터베이스를 vCore 기반 구매 모델, 변환 된 후에 동일한 정책 설정을 사용 하 여 장애 조치 그룹 적용 유지 됩니다.

### <a name="create-a-geo-replication-secondary-database"></a>지역에서 복제 보조 데이터베이스 만들기

주 데이터베이스에 사용한 것과 동일한 서비스 계층을 사용 하 여 지역에서 복제 보조 데이터베이스 (geo-secondary)를 만들 수 있습니다. 높은 로그 생성 속도 사용 하 여 데이터베이스에 대 한 주 데이터베이스와 같은 계산 크기를 사용 하 여 보조를 만드는 것이 좋습니다.

단일 주 데이터베이스에 대 한 탄력적 풀에 지역 보조 복제본을 만들려는 경우를 `maxVCore` 주 데이터베이스의 계산 크기와 일치 하는 풀에 대 한 설정입니다. 다른 탄력적 풀의 기본 지역에서 보조를 만드는 경우 풀 동일한를 포함 하는 것이 좋습니다 `maxVCore` 설정 합니다.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>데이터베이스 복사를 사용 하 여 DTU 기반 데이터베이스를 vCore 기반 데이터베이스로 변환

대상 컴퓨팅 크기가 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 경우 제한 또는 특별한 순서 지정 없이 DTU 기반 컴퓨팅 크기의 데이터베이스를 vCore 기반 컴퓨팅 크기의 데이터베이스에 복사할 수 있습니다. 데이터베이스 복사본이 복사 작업의 시작 시간을 기준으로 데이터의 스냅숏을 만들고 원본과 대상 간에 데이터를 동기화 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 특정 계산 크기 및 단일 데이터베이스에 대 한 사용 가능한 저장소 크기 선택 [단일 데이터베이스에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md)합니다.
- 특정 계산 크기 및 탄력적 풀에 대 한 사용 가능한 저장소 크기 선택 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)합니다.
