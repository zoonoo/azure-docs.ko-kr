---
title: Azure SQL Database 자동, 지역 중복 백업 | Microsoft Docs
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 7afc1170ba2503c8a8c97be9a19459c92e331449
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453582"
---
# <a name="automated-backups"></a>자동화된 백업

SQL Database는 7-35일 동안 유지되는 데이터베이스 백업을 자동으로 만들고 Azure RA-GRS(읽기 액세스 지역 중복 스토리지)를 사용하여 데이터 센터를 사용할 수 없는 경우에도 백업이 유지되도록 합니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 백업을 위해 아무 작업도 수행할 필요가 없으며 [백업 보존 기간을 변경](#how-to-change-the-pitr-backup-retention-period)할 수 있습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 보안 규칙에서 오랫동안(최대 10년) 백업을 사용할 수 있도록 요구하는 경우, [장기 보존](sql-database-long-term-retention.md)을 구성할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용하여 PITR(지정 시간 복원)의 목적으로 [전체](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [차등](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 및 [트랜잭션 로그](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 백업을 만듭니다. 트랜잭션 로그 백업은 일반적으로 5~10분마다 발생하고, 차등 백업은 일반적으로 12시간마다 발생하며, 이러한 빈도는 계산 크기와 데이터베이스 작업량에 따라 달라집니다. 전체, 차등 및 트랜잭션 로그 백업을 통해 데이터베이스를 호스트하는 동일한 서버로 특정 시점에 대해 데이터베이스를 복원할 수 있습니다. 백업은 데이터 센터 가동 중단으로부터 보호하기 위해 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 복제되는 RA-GRS 저장소 Blob에 저장됩니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

- 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 이 작업으로 원본 데이터베이스와 같은 서버에 새 데이터베이스가 만들어집니다.
- 삭제된 시간 또는 보존 기간 내 임의 시간으로 삭제된 데이터베이스를 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버에만 복원할 수 있습니다.
- 데이터베이스를 다른 지리적 지역으로 복원합니다. 이러한 지리적 복원을 사용하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다.
- LTR(장기 보존 정책)을 사용하여 데이터베이스를 구성한 경우, 특정 장기 백업에서 데이터베이스를 복원합니다. LTR을 사용하면 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 애플리케이션을 실행할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
- 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 ‘데이터베이스 복제’는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

## <a name="how-long-are-backups-kept"></a>백업은 얼마 동안 유지되나요?

각 SQL Database에는 구매 모델 및 서비스 계층에 따라 달라지는 7-35일의 기본 백업 보존 기간이 있습니다. SQL Database 서버에서 데이터베이스의 백업 보존 기간을 업데이트할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조하세요.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어, 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우, 4일 된 백업은 앞으로 3일 동안 더 저장됩니다.

최대 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 탄력적 풀과 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우, LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

### <a name="default-backup-retention-period"></a>기본 백업 보존 기간

#### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU 기반 구매 모델을 사용하여 만든 데이터베이스의 기본 보존 기간은 서비스 계층에 따라 달라집니다.

- 기본 서비스 계층은 1주일입니다.
- 표준 서비스 계층은 5주입니다.
- 프리미엄 서비스 계층은 5주입니다.

#### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

[vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용하는 경우 기본 백업 보존 기간은 7일입니다(단일, 풀링된 및 인스턴스 데이터베이스). 모든 Azure SQL Database(단일, 풀링된 및 인스턴스 데이터베이스)에 대해 [백업 보존 기간을 최대 35일로 변경](#how-to-change-the-pitr-backup-retention-period)할 수 있습니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간보다 오래된 기존의 모든 백업을 더 이상 사용할 수 없게 됩니다. 현재 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다.

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?

### <a name="backups-for-point-in-time-restore"></a>지정 시간 복원에 대한 백업

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 만들어지고, 차등 데이터베이스 백업은 일반적으로 12시간마다 만들어지고, 트랜잭션 로그 백업은 일반적으로 5~10분마다 만들어지며, 이러한 빈도는 계산 크기와 데이터베이스 작업량에 따라 달라집니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다.

PITR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="backups-for-long-term-retention"></a>장기 보존에 대한 백업

단일 및 풀링된 데이터베이스는 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 저장소 컨테이너로 자동으로 복사됩니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 저장소 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 저장소 비용을 추정할 수 있습니다.

PITR과 마찬가질 LTR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="storage-costs"></a>저장소 비용
기본적으로 7일 분량의 자동화된 데이터베이스 백업이 RA-GRS 표준 Blob Storage에 복사됩니다. 저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 트랜잭션 로그의 크기는 데이터베이스 변동률에 따라 다릅니다. 데이터베이스 크기의 100%와 같은 최소 스토리지 양은 추가 요금 없이 제공됩니다. 추가로 사용되는 백업 스토리지의 경우 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요. 

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

데이터베이스가 TDE를 사용하여 암호화된 경우, LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL Database에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL Database는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="how-does-microsoft-ensure-backup-integrity"></a>백업 무결성은 Microsoft에서 어떻게 보장하나요?

Azure SQL Database 엔지니어링 팀은 지속적으로 서비스 전체에서 데이터베이스의 자동화된 데이터베이스 백업에 대한 복원을 자동으로 테스트합니다. 복원 시 데이터베이스도 DBCC CHECKDB를 사용하여 무결성 검사를 받습니다. 무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. Azure SQL Database의 데이터 무결성에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="how-do-automated-backups-impact-compliance"></a>자동화된 백업은 규정 준수에 어떻게 영향을 주나요?

기본 PITR 보존 기간이 35일인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우, 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 준수 요구 사항을 충족하지 못하는 경우, PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간은 어떻게 변경하나요?

Azure Portal, PowerShell 또는 REST API를 사용하여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 지원되는 값은 7, 14, 21, 28 또는 35일입니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Azure Portal을 사용하여 PITR 백업 보존 기간 변경

Azure Portal을 사용하여 PITR 백업 보존 기간을 변경하려면 Portal 내에서 보존 기간을 변경하려는 서버 개체로 이동한 후, 수정할 서버 개체에 따라 적절한 옵션을 선택합니다.

#### <a name="change-pitr-for-a-sql-database-server"></a>SQL Database 서버의 PITR 변경

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Managed Instance의 PITR 변경

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

> [!IMPORTANT]
> 이 API는 [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview) 버전부터 AzureRM.Sql PowerShell 모듈에 포함됩니다.

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API를 사용하여 PITR 보존 기간 변경

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
- Azure Portal을 사용하여 지정 시간으로 복원하려면 [Azure Portal을 사용하여 지정 시간으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복원하려면 [PowerShell을 사용하여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
- Azure Portal을 사용하여 Azure Blob Storage에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Blob 저장소의 장기 보존된 자동화된 백업에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
