---
title: 자동 지역 중복 백업
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 스토리지를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 7858d626282b3090e6c95af9c06b3a0a7e603462
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773589"
---
# <a name="automated-backups"></a>자동화된 백업

Azure SQL Database는 구성된 보존 기간 동안 유지되는 데이터베이스 백업을 자동으로 만듭니다. Azure [RA-GRS(읽기 액세스 지역 중복 스토리지)](../storage/common/storage-redundancy.md)를 사용하여 데이터 센터를 사용할 수 없는 경우에도 백업이 유지되도록 합니다.

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 보안 규칙에서 오랫동안(최대 10년) 백업을 사용할 수 있도록 요구하는 경우 싱글톤 데이터베이스 및 Elastic Database 풀에 대해 [장기 보존](sql-database-long-term-retention.md)을 구성할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용하여 1주마다 [전체 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), 12시간마다 [차등 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), 5~10분마다 [트랜잭션 로그 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)을 만듭니다. 백업은 데이터 센터 가동 중단으로부터 보호하기 위해 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 복제되는 [RA-GRS 스토리지 Blob](../storage/common/storage-redundancy.md)에 저장됩니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 판단합니다.

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

- Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 보존 기간 내에 **과거의 특정 시점으로 기존 데이터베이스를 복원합니다**. 단일 데이터베이스 및 Elastic Database 풀의 경우 이 작업은 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만듭니다. 관리되는 인스턴스에서 이 작업을 수행하면 동일한 구독에서 데이터베이스의 복사본 또는 같거나 다른 관리되는 인스턴스를 만들 수 있습니다.
- 삭제된 시점 또는 보존 기간 내의 모든 시간으로 **삭제된 데이터베이스를 복원합니다**. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 논리 서버 또는 관리되는 인스턴스에만 복원할 수 있습니다.
- **데이터베이스를 다른 지리적 지역으로 복원합니다**. 이러한 지리적 복원을 사용하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다.
- LTR(장기 보존 정책)을 사용하여 데이터베이스를 구성한 경우 단일 데이터베이스 또는 Elastic Database 풀의 **특정 장기 백업에서 데이터베이스를 복원합니다**. LTR을 사용하면 [Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) 또는 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)을 사용해 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 애플리케이션을 실행할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제*라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL Server의 ‘데이터베이스 복제’는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

다음 예제를 사용하여 이러한 작업 중 일부를 시도할 수 있습니다.

| | Azure 포털 | Azure PowerShell |
|---|---|---|
| 백업 보존 변경 | [단일 데이터베이스](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [관리되는 인스턴스](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [단일 데이터베이스](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[관리되는 인스턴스](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 장기 백업 보존 변경 | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>관리되는 인스턴스 - N/A  | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md)<br/>관리되는 인스턴스 - N/A  |
| 지정 시간에서 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md#point-in-time-restore) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [관리되는 인스턴스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 삭제된 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [관리되는 인스턴스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage에서 데이터베이스 복원 | 단일 데이터베이스 - N/A <br/>관리되는 인스턴스 - N/A  | 단일 데이터베이스 - N/A <br/>[관리되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Backup 주기

### <a name="point-in-time-restore"></a>지정 시간 복원

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 생성되고 차등 데이터베이스 백업은 일반적으로 12시간마다 생성됩니다. 트랜잭션 로그 백업은 일반적으로 5~10분마다 생성됩니다. 트랜잭션 로그 백업의 빈도는 컴퓨팅 크기와 데이터베이스 작업의 양을 기준으로 합니다. 

첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 이 백업은 일반적으로 30분 이내에 완료되지만 데이터베이스의 크기가 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 백업 작업을 변경하거나 사용하지 않도록 설정할 수 없습니다.

### <a name="default-backup-retention-period"></a>기본 백업 보존 기간

PITR 백업은 지역 중복 스토리지를 사용하여 보호됩니다. 자세한 내용은 [Azure Storage 중복 옵션](../storage/common/storage-redundancy.md)을 참조하세요.

PITR에 대한 자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="long-term-retention"></a>장기 보존

단일 및 풀링된 데이터베이스의 경우 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년까지 구성할 수 있습니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 스토리지 컨테이너로 자동 복사됩니다. 다양한 규정 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 스토리지 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 스토리지 비용을 추정할 수 있습니다.

PITR 백업과 마찬가지로 LTR도 지역 중복 스토리지를 사용하여 보호됩니다. 자세한 내용은 [Azure Storage 중복 옵션](../storage/common/storage-redundancy.md)을 참조하세요.

LTR에 대한 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="backup-storage-consumption"></a>백업 스토리지 사용량

단일 데이터베이스의 경우 이 수식은 총 백업 스토리지 사용량을 계산하는 데 사용됩니다.

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Elastic Database 풀의 경우 총 백업 스토리지 크기는 풀 수준에서 집계되며 다음과 같이 계산됩니다.

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

보존 기간 이전에 발생하는 백업은 타임스탬프에 따라 자동으로 제거됩니다. 차등 백업 및 로그 백업은 효과적인 백업을 위해 이전 전체 백업이 필요하기 때문에 주간 청크에서 함께 제거됩니다.

Azure SQL Database는 전체 보존 기간 내 백업 스토리지를 누적 값으로 계산합니다. 매시간 이 값은 Azure 청구 파이프라인에 보고되며, 매시간 사용량을 집계하여 각 월말에 사용량을 계산하는 데 사용됩니다. 데이터베이스를 삭제한 후 백업 기간이 늘면서 소비량은 줄어듭니다. 백업이 보존 기간보다 오래된 후에는 요금이 청구됩니다.

   > [!IMPORTANT]
   > 데이터베이스가 삭제된 경우에도 데이터베이스의 백업은 지정된 보존 기간에 유지됩니다. 데이터베이스를 삭제하고 다시 만들면 스토리지 및 계산 비용이 절약되는 경우도 많지만, Microsoft는 삭제될 때마다 삭제된 각 데이터베이스에 대해 지정된 보존 기간에 백업을 유지하기 때문에 백업 스토리지 비용이 커질 수 있습니다.

### <a name="monitor-consumption"></a>사용량 모니터링

각 백업 유형(전체, 차등 및 로그)은 데이터베이스 모니터링 블레이드에서 별도의 메트릭으로 보고됩니다. 다음 다이어그램에서는 단일 데이터베이스에 대한 백업 스토리지 사용량을 모니터링하는 방법을 보여 줍니다. 이 기능은 현재 관리되는 인스턴스에 사용할 수 없습니다.

![Azure Portal에서 데이터베이스 백업 사용량 모니터링](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>백업 스토리지 사용량 미세 조정

초과 백업 스토리지 사용량은 개별 데이터베이스의 워크로드 및 크기에 따라 달라집니다. 백업 스토리지 사용량을 줄이기 위해 다음과 같은 몇 가지 튜닝 기법을 고려합니다.

* [백업 보존 기간](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)을 사용자 요구에 따라 가능한 최소 기간으로 줄입니다.
* 인덱스 다시 빌드와 같은 대량 쓰기 작업은 수행하지 않는 것이 좋습니다.
* 대량 데이터 로드 작업의 경우 [클러스터형 columnstore 인덱스](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) 사용을 고려하고, 비클러스터형 인덱스 수를 줄이고, 행 개수가 100만 정도인 대량 로드 작업을 고려합니다.
* 범용 서비스 계층에서 프로비저닝된 데이터 스토리지는 초과 백업 스토리지의 가격보다 비용이 저렴합니다. 초과 백업 스토리지 비용이 지속적으로 증가하는 경우 데이터 스토리지를 늘려서 백업 스토리지를 절약할 수 있습니다.
* 임시 결과를 저장하기 위해 ETL 논리에서 영구 테이블 대신 TempDB를 사용합니다. (관리되는 인스턴스에만 적용할 수 있음)
* 중요한 데이터(예: 개발 또는 테스트 데이터베이스)를 포함하지 않는 데이터베이스의 경우 TDE 암호화 해제를 고려합니다. 암호화되지 않은 데이터베이스에 대한 백업은 일반적으로 더 높은 압축 비율로 압축됩니다.

> [!IMPORTANT]
> 분석 데이터 마트 \ 데이터 웨어하우스 워크로드의 경우 [클러스터형 columnstore 인덱스](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)를 사용하고, 비클러스터형 인덱스 수를 줄이고, 초과된 백업 스토리지 사용을 줄이기 위해 100만에 대한 행 수를 포함하는 대량 로드 작업을 수행하는 것이 좋습니다.

## <a name="storage-costs"></a>스토리지 비용

스토리지 가격은 사용하는 모델이 DTU 모델인지 또는 vCore 모델인지에 따라 달라집니다.

### <a name="dtu-model"></a>DTU 모델

DTU 모델을 사용하는 경우 데이터베이스 및 Elastic Database 풀의 백업 스토리지에 대해서는 추가 요금이 부과되지 않습니다.

### <a name="vcore-model"></a>vCore 모델

단일 데이터베이스의 경우 데이터베이스 크기의 100%와 같은 최소 백업 스토리지 용량이 추가 요금 없이 제공됩니다. Elastic Database 풀 및 관리되는 인스턴스의 경우 각각 풀 또는 인스턴스 크기에 할당된 데이터 스토리지의 100%와 같은 최소 백업 스토리지 양이 추가 비용 없이 제공됩니다. 추가로 사용되는 백업 스토리지의 경우 GB/월 단위로 요금이 청구됩니다. 이러한 추가 사용량은 개별 데이터베이스의 워크로드 및 크기에 따라 달라집니다.

Azure SQL Database는 전체 보존 기간 내 백업 스토리지를 누적 값으로 계산합니다. 매시간 이 값은 Azure 청구 파이프라인에 보고되며, 해당 파이프라인에서는 이와 같은 매시간 사용량을 집계하여 월말에 사용량을 산정합니다. 데이터베이스를 삭제한 후에는 Microsoft에서 백업 보존 기간이 늘수록 사용량을 줄입니다. 백업이 보존 기간보다 오래된 후에는 요금이 청구됩니다. 모든 로그 백업과 차등 백업은 전체 보존 기간에 유지되므로 수정 사항이 많은 데이터베이스의 백업 요금은 높아집니다.

데이터베이스의 누적 백업 스토리지 용량이 744GB이고, 이 양이 전체 월에 걸쳐 일정하게 유지된다고 가정해보겠습니다. 이 누적 스토리지 사용량을 시간당 사용량으로 변환하려면 744(매월 31일 X 매일 24시간)로 나눕니다. 따라서 SQL Database는 데이터베이스에서 매시간 1GB의 PITR 백업을 사용한 것으로 보고합니다. Azure 청구는 이 사용량을 집계하고 전체 월에 대해 744GB의 사용량을 표시합니다. 비용은 해당 지역의 $/GB/월 요금에 따라 결정됩니다.

이제 좀 더 복잡한 예를 들겠습니다. 데이터베이스의 보존이 월 중간에 14일로 증가했다고 가정해보겠습니다. 이와 같은 증가로 인해 전체 백업 스토리지의 용량이 2배에 해당하는 1,488GB로 증가한다고 가정합니다. SQL Database는 1~372시간에 대해 1GB의 사용량을 보고합니다. 373~744시간에 대한 사용량은 2GB로 보고합니다. 이 사용량은 최종 청구 1,116GB/월로 집계됩니다.

### <a name="monitor-costs"></a>비용 모니터링

백업 스토리지 비용을 이해하려면 Azure Portal의 **비용 관리 + 청구**로 이동하여 **비용 관리**를 선택한 다음, **비용 분석**을 선택합니다. 원하는 구독을 **범위**로 선택하고 관심 있는 기간 및 서비스를 필터링합니다.

**서비스 이름**에 대한 필터를 추가한 다음, 드롭다운 목록에서 **SQL 데이터베이스**를 선택합니다. **미터 하위 범주** 필터를 사용하여 서비스에 대한 청구 카운터를 선택합니다. 단일 데이터베이스 또는 Elastic Database 풀의 경우 **단일/탄력적 풀 PITR 백업 스토리지**를 선택합니다. 관리되는 인스턴스의 경우 **MI PITR 백업 스토리지**를 선택합니다. **스토리지** 및 **계산** 하위 범주에도 관심이 갈지 모르지만, 이 둘은 백업 스토리지 비용과는 관련이 없습니다.

![백업 스토리지 비용 분석](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Backup 보존

모든 Azure SQL 데이터베이스(단일, 풀링된 및 관리되는 인스턴스 데이터베이스)는 기본 백업 보존 기간이 7일입니다. 최대 35일로 [백업 보존 기간을 변경할 수 있습니다](#change-the-pitr-backup-retention-period).

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우 4일 된 백업은 앞으로 3일간 더 저장됩니다.

최대 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 Elastic Database 풀과 데이터베이스도 삭제되며 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우 LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

> [!NOTE]
> Azure Portal을 통해 단일 풀링된 데이터베이스 및 관리되는 인스턴스에 대해 구성할 수 있는 최소 PITR 백업 보존 기간은 7일입니다. As.SQL 모듈 v2.6.0 이상으로 PowerShell을 사용하는 경우에만 관리되는 인스턴스에 대해 최소 PITR 백업 보존을 1일로 구성할 수 있습니다.

## <a name="encrypted-backups"></a>암호화된 백업

데이터베이스가 TDE를 사용하여 암호화된 경우 LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL 데이터베이스에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL 데이터베이스는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="backup-integrity"></a>백업 무결성

Azure SQL Database 엔지니어링 팀은 지속적으로 논리 서버 및 Elastic Database 풀에 배치된 데이터베이스의 자동화된 데이터베이스 백업에 대한 복원을 자동으로 테스트합니다. (이 테스트는 관리되는 인스턴스에 제공되지 않음) 지정 시간 복원 시 데이터베이스도 DBCC CHECKDB 무결성 검사를 받습니다.

관리되는 인스턴스는 마이그레이션이 완료된 후 네이티브 `RESTORE` 명령 또는 Azure 데이터 마이그레이션 서비스를 사용하여 복원된 데이터베이스의 `CHECKSUM`을 사용하여 자동 초기 백업을 수행합니다.

무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. 자세한 내용은 [Azure SQL Database에서 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="compliance"></a>규정 준수

DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 규정 준수 요구 사항을 충족하지 못하는 경우 PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [PITR 백업 보존 기간 변경](#change-the-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간 변경

Azure Portal, PowerShell 또는 REST API를 사용하여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간보다 오래된 기존의 모든 백업을 더 이상 사용할 수 없게 됩니다. 현재 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간 끝에 도달할 때까지 기존 백업을 유지합니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스의 LTR을 구성한 경우에는 LTR이 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Azure Portal을 사용하여 PITR 백업 보존 기간 변경

Azure Portal을 사용하여 PITR 백업 보존 기간을 변경하려면 포털에서 보존 기간을 변경하려는 서버 개체로 이동합니다. 그런 다음, 수정하는 서버 개체에 따라 적절한 옵션을 선택합니다.

#### <a name="single-database-and-elastic-database-pools"></a>[단일 데이터베이스 및 Elastic Database 풀](#tab/single-database)

단일 Azure SQL 데이터베이스의 PITR 백업 보존에 대한 변경 내용은 서버 수준에서 수행됩니다. 서버 수준에서 변경한 내용은 서버의 데이터베이스에 적용됩니다. Azure Portal에서 Azure SQL Database 서버의 PITR 보존을 변경하려면 서버 개요 블레이드로 이동합니다. 왼쪽 창에서 **백업 관리**를 선택하고 화면 맨 위에 있는 **보존 구성**을 선택합니다.

![PITR 보존 변경, 서버 수준](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[관리되는 인스턴스](#tab/managed-instance)

SQL Database 관리되는 인스턴스의 PITR 백업 보존에 대한 변경 내용은 개별 데이터베이스 수준에서 수행됩니다. Azure Portal에서 인스턴스 데이터베이스의 PITR 백업 보존 기간을 변경하려면 개별 데이터베이스 개요 블레이드로 이동합니다. 그런 다음, 화면 맨 위에 있는 **백업 보존 구성**을 선택합니다.

![PITR 보존 변경, 관리되는 인스턴스](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.SQL 모듈을 대상으로 이루어집니다. 자세한 내용은 [AzureRM.SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈의 명령에 대한 인수는 AzureRm 모듈의 인수와 실질적으로 동일합니다.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>REST API를 사용하여 PITR 백업 보존 기간 변경

#### <a name="sample-request"></a>샘플 요청

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>샘플 응답

상태 코드: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

자세한 내용은 [백업 보존 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- [Azure Portal을 사용하여 지정 시간으로 데이터베이스를 복원](sql-database-recovery-using-backups.md)하는 방법에 대해 자세히 알아봅니다.
- [PowerShell을 사용하여 지정 시간으로 데이터베이스를 복원](scripts/sql-database-restore-database-powershell.md)하는 방법에 대해 자세히 알아봅니다.
- Azure Portal을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
