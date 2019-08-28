---
title: Azure SQL Database 장기 백업 보존 관리 | Microsoft Docs
description: 자동화된 백업을 SQL Azure Storage에 저장한 다음, 복원하는 방법 알아보기
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
ms.date: 08/21/2019
ms.openlocfilehash: b90e364442e46269fc949ef4aecd9a756cff5595
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904632"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database 장기 백업 보존 관리

Azure SQL Database에서 [장기 백업 보존](sql-database-long-term-retention.md) 정책 (LTR)이 포함 된 단일 또는 풀링된 데이터베이스를 구성 하 여 최대 10 년 동안 별도의 Azure Blob storage 컨테이너에 데이터베이스 백업을 자동으로 유지할 수 있습니다. 그런 다음, Azure Portal이나 PowerShell에서 이러한 백업을 사용하여 데이터베이스를 복구할 수 있습니다.

> [!IMPORTANT]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md)는 현재 장기 백업 보존을 지원하지 않습니다.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Azure Portal를 사용 하 여 장기 백업을 관리 합니다.

다음 섹션에서는 Azure Portal을 사용하여 장기 보존을 구성하고, 장기 보존되는 백업을 보고, 장기 보존에서 백업을 복원하는 방법을 보여줍니다.

### <a name="configure-long-term-retention-policies"></a>장기 보존 정책 구성

서비스 계층의 보존 기간보다 긴 기간 동안 [자동화된 백업을 유지](sql-database-long-term-retention.md)하도록 SQL Database를 구성할 수 있습니다. 

1. Azure Portal에서 SQL Server를 선택한 다음, **백업 관리**를 클릭합니다. **정책 구성** 탭에서 *장기 백업 보존 정책을 설정하거나 수정할 데이터베이스에 대한 확인란을 선택합니다*. 데이터베이스 옆의 확인란을 선택하지 않으면 정책에 대한 변경 내용이 해당 데이터베이스에 적용되지 않습니다.  

   ![백업 관리 링크](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. **정책 구성** 창에서 매주, 매달 또는 매년 백업을 보존할지 여부를 선택하고 각각에 대해 보존 기간을 지정합니다. 

   ![정책 구성](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. 완료되면 **적용**을 클릭합니다.

> [!IMPORTANT]
> 장기 백업 보존 정책을 사용 하도록 설정 하는 경우 첫 번째 백업이 표시 되 고 복원 하는 데 사용할 수 있는 최대 7 일이 걸릴 수 있습니다. LTR backup cadance에 대 한 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조 하세요.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Azure Portal을 사용하여 백업을 보고 백업에서 복원

LTR 정책으로 특정 데이터베이스에 보존된 백업을 보고 해당 백업에서 복원합니다. 

1. Azure Portal에서 SQL Server를 선택한 다음, **백업 관리**를 클릭합니다. **사용 가능한 백업** 탭에서 사용 가능한 백업을 보려는 데이터베이스를 선택합니다.

   ![데이터베이스 선택](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. **사용 가능한 백업** 창에서 사용 가능한 백업을 검토합니다. 

   ![백업 보기](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. 복원에 사용할 백업을 선택한 다음, 새 데이터베이스 이름을 지정합니다.

   ![복원(restore)](./media/sql-database-long-term-retention/ltr-restore.png)

5. **확인**을 클릭하여 Azure SQL 스토리지의 백업에서 새 데이터베이스로 데이터베이스를 복원합니다.

6. 도구 모음에서 알림 아이콘을 클릭하여 복원 작업의 상태를 봅니다.

   ![복원 작업 진행률](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 복원 작업이 완료되면 **SQL Database** 페이지를 열어 새로 복원된 데이터베이스를 봅니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](sql-database-recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.
>

## <a name="use-powershell-to-manage-long-term-backups"></a>PowerShell을 사용 하 여 장기 백업 관리

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

다음 섹션에서는 PowerShell을 사용하여 장기 백업 보존을 구성하고 Azure SQL 스토리지에서 백업을 확인하고 Azure SQL 스토리지의 백업에서 복원하는 방법을 보여줍니다.


### <a name="rbac-roles-to-manage-long-term-retention"></a>장기 보존을 관리하는 RBAC 역할

**AzSqlDatabaseLongTermRetentionBackup** 및 **AzSqlDatabase**의 경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- SQL Server 참가자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:

   Microsoft .Sql/위치/longTermRetentionBackups/읽기 longTermRetentionServers/longTermRetentionBackups/read Microsoft .Sql/위치/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/읽기
 
**AzSqlDatabaseLongTermRetentionBackup**의 경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


> [!NOTE]
> SQL Server 참여자 역할에는 LTR 백업을 삭제할 수 있는 권한이 없습니다.

RBAC 권한은 *구독* 또는 *리소스 그룹* 범위에서 부여할 수 있습니다. 그러나 삭제 된 서버에 속하는 LTR 백업에 액세스 하려면 해당 서버의 *구독* 범위에서 사용 권한을 부여 해야 합니다.


### <a name="create-an-ltr-policy"></a>LTR 정책 만들기

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR 정책 보기
이 예제에서는 서버 내의 LTR 정책을 나열하는 방법을 보여줍니다.

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>LTR 정책 지우기
이 예제에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여줍니다.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

이 예제에서는 서버 내의 LTR 백업을 나열하는 방법을 보여줍니다. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 서버를 삭제 한 후 LTR 백업을 삭제 하려면 구독 범위 권한이 있어야 합니다. ‘장기 보존 백업 삭제’ 작업으로 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../azure-monitor/platform/alerts-activity-log.md)를 참조하세요.
>

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원
이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 이 인터페이스는 변경되지 않았지만 이제 리소스 ID 매개 변수에 LTR 백업 리소스 ID가 필요합니다. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> 서버를 삭제 한 후 LTR 백업에서 복원 하려면 서버 구독으로 범위가 지정 된 사용 권한이 있어야 하 고 해당 구독이 활성 상태 여야 합니다. 또한 선택적-ResourceGroupName 매개 변수를 생략 해야 합니다.  
>

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
