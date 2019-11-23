---
title: automatic, geo-redundant backups
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 스토리지를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 5d5a2b9527dcb53b0315ac5fd964c6f30961f3e7
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405755"
---
# <a name="automated-backups"></a>자동화된 백업

SQL Database automatically creates the database backups that are kept between 7 and 35 days, and uses Azure [read-access geo-redundant storage (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) to ensure that they are preserved even if the data center is unavailable. These backups are created automatically. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. If your security rules require that your backups are available for an extended period of time (up to 10 years), you can configure a [long-term retention](sql-database-long-term-retention.md) on Singleton databases and Elastic pools.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database uses SQL Server technology to create [full backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) every week, [differential backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) every 12 hours, and [transaction log backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) every 5-10 minutes. The backups are stored in [RA-GRS storage blobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) that are replicated to a [paired data center](../best-practices-availability-paired-regions.md) for protection against a data center outage. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

- **Restore an existing database to a point-in-time in the past** within the retention period using the Azure portal, Azure PowerShell, Azure CLI, or REST API. In Single database and Elastic pools, this operation will create a new database in the same server as the original database. In Managed Instance, this operation can create a copy of the database or same or different Managed Instance under the same subscription.
  - **[Change Backup Retention Period](#how-to-change-the-pitr-backup-retention-period)** between 7 to 35 days to configure your backup policy.
  - **Change long-term retention policy up to 10 years** on Single Database and Elastic Pools using [the Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Restore a deleted database to the time it was deleted** or anytime within the retention period. The deleted database can only be restored in the same logical server or Managed Instance where the original database was created.
- **Restore a database to another geographical region**. 이러한 지리적 복원을 사용하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다.
- **Restore a database from a specific long-term backup** on Single Database or Elastic Pool if the database has been configured with a long-term retention policy (LTR). LTR allows you to restore an old version of the database using [the Azure portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) to satisfy a compliance request or to run an old version of the application. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
- 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 ‘데이터베이스 복제’는주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

You can try some of these operations using the following examples:

| | Azure 포털 | Azure PowerShell |
|---|---|---|
| Change backup retention | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Change Long-term backup retention | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Managed Instance - N/A  |
| Restore database from point-in-time | [단일 데이터베이스](sql-database-recovery-using-backups.md#point-in-time-restore) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 삭제된 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restore database from Azure Blob Storage | Single database - N/A <br/>Managed Instance - N/A  | Single database - N/A <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>백업은 얼마 동안 유지되나요?

All Azure SQL databases (single, pooled, and managed instance databases) have a default backup retention period of  **seven** days. You can [change backup retention period up to 35 days](#how-to-change-the-pitr-backup-retention-period).

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어, 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우, 4일 된 백업은 앞으로 3일 동안 더 저장됩니다.

최대 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 탄력적 풀과 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우, LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?

### <a name="backups-for-point-in-time-restore"></a>지정 시간 복원에 대한 백업

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 만들어지고, 차등 데이터베이스 백업은 일반적으로 12시간마다 만들어지고, 트랜잭션 로그 백업은 일반적으로 5~10분마다 만들어지며, 이러한 빈도는 컴퓨팅 크기와 데이터베이스 작업량에 따라 달라집니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. You cannot change or disable the backup jobs. 

PITR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="backups-for-long-term-retention"></a>장기 보존에 대한 백업

단일 및 풀링된 데이터베이스는 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 스토리지 컨테이너로 자동으로 복사됩니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 스토리지 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 스토리지 비용을 추정할 수 있습니다.

PITR과 마찬가질 LTR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="storage-costs"></a>스토리지 비용
For single databases and managed instances, a minimum backup storage amount equal to 100% of database size is provided at no extra charge. For elastic pools, a minimum backup storage amount equal to 100% of the allocated data storage for the pool is provided at no extra charge. 추가로 사용되는 백업 스토리지는 GB/월 단위로 요금이 청구됩니다. This additional consumption will depend on the workload and size of the individual databases.

You can use Azure subscription cost analysis to determine your current spending on backup storage.

![Backup storage cost analysis](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

If you go to your subscription and open Cost Analysis blade, you can select meter subcategory **mi pitr backup storage** to see your current backup cost and charge forecast. You can also include other meter subcategories such as **managed instance general purpose - storage** or **managed instance general purpose - compute gen5** to compare backup storage cost with other cost categories.

> [!Note]
> You can [change retention period to 7 days](#change-pitr-backup-retention-period-using-azure-portal) to reduce the backup storage cost.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요. 

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

데이터베이스가 TDE를 사용하여 암호화된 경우, LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL 데이터베이스에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL 데이터베이스는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="how-does-microsoft-ensure-backup-integrity"></a>백업 무결성은 Microsoft에서 어떻게 보장하나요?

On an ongoing basis, the Azure SQL Database engineering team automatically tests the restore of automated database backups of databases placed in Logical servers and Elastic pools (this is not available in Managed Instance). Upon point-in-time restore, databases also receive integrity checks using DBCC CHECKDB.

Managed Instance takes automatic initial backup with `CHECKSUM` of the databases restored using native `RESTORE` command or Data Migration Service once the migration is completed.

무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. Azure SQL Database의 데이터 무결성에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="how-do-automated-backups-impact-compliance"></a>자동화된 백업은 규정 준수에 어떻게 영향을 주나요?

기본 PITR 보존 기간이 35일인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우, 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 준수 요구 사항을 충족하지 못하는 경우, PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간은 어떻게 변경하나요?

You can change the default PITR backup retention period using the Azure portal, PowerShell, or the REST API. 지원되는 값은 7일, 14일, 21일, 28일 또는 35일입니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!WARNING]
> If you reduce the current retention period, all existing backups older than the new retention period are no longer available. 현재 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Change PITR backup retention period using Azure portal

To change the PITR backup retention period using the Azure portal, navigate to the server object whose retention period you wish to change within the portal and then select the appropriate option based on which server object you're modifying.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[단일 데이터베이스 및 탄력적 풀](#tab/single-database)

Change of PITR backup retention for single Azure SQL Databases is performed at the server level. Change made at the server level applies to databases on that server. To change PITR for Azure SQL Database server from Azure portal, navigate to the server overview blade, click on Manage Backups on the navigation menu, and then click on Configure retention at the navigation bar.

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

Change of PITR backup retention for SQL Database managed instance is performed at an individual database level. To change PITR backup retention for an instance database from Azure portal, navigate to the individual database overview blade, and then click on Configure backup retention at the navigation bar.

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API를 사용하여 PITR 보존 기간 변경

#### <a name="sample-request"></a>샘픔 요청

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
- To configure, manage, and restore from long-term retention of automated backups in Azure Blob storage using PowerShell, see [Manage long-term backup retention using PowerShell](sql-database-long-term-backup-retention-configure.md).
