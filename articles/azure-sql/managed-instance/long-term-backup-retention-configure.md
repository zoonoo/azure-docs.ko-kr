---
title: 'Azure SQL Managed Instance: 장기 백업 보존'
description: PowerShell을 사용 하 여 Azure SQL Managed Instance에 대 한 별도의 Azure Blob storage 컨테이너에 자동화 된 백업을 저장 하 고 복원 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052029"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Azure SQL Managed Instance 장기 백업 보존 관리 (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance에서 LTR ( [장기 백업 보존](../database/long-term-retention-overview.md) 정책)을 공개 미리 보기 기능으로 구성할 수 있습니다. 이를 통해 최대 10 년 동안 별도의 Azure Blob storage 컨테이너에 데이터베이스 백업을 자동으로 유지할 수 있습니다. 그런 다음 PowerShell을 사용 하 여 이러한 백업을 사용 하 여 데이터베이스를 복구할 수 있습니다.

   > [!IMPORTANT]
   > 관리 되는 인스턴스의 경우 LTR은 현재 Azure 공용 지역의 공개 미리 보기에서 사용할 수 있습니다. 

다음 섹션에서는 PowerShell을 사용하여 장기 백업 보존을 구성하고 Azure SQL 스토리지에서 백업을 확인하고 Azure SQL 스토리지의 백업에서 복원하는 방법을 보여줍니다.


## <a name="using-the-azure-portal"></a>Azure Portal 사용

다음 섹션에서는 Azure Portal를 사용 하 여 장기 보존 정책을 설정 하 고, 사용 가능한 장기 보존 백업을 관리 하 고, 사용 가능한 백업에서 복원 하는 방법을 보여 줍니다.

### <a name="configure-long-term-retention-policies"></a>장기 보존 정책 구성

서비스 계층의 보존 기간 보다 긴 기간 동안 [자동화 된 백업을 보존](../database/long-term-retention-overview.md) 하도록 SQL Managed Instance를 구성할 수 있습니다.

1. Azure Portal에서 관리 되는 인스턴스를 선택 하 고 **백업** 을 클릭 합니다. **보존 정책** 탭에서 장기 백업 보존 정책을 설정 하거나 수정 하려는 데이터베이스를 선택 합니다. 선택 하지 않은 상태로 남아 있는 데이터베이스에는 변경 내용이 적용 되지 않습니다. 

   ![백업 관리 링크](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. **정책 구성** 창에서 매주, 매월 또는 매년 백업에 대해 원하는 보존 기간을 지정 합니다. ' 0 ' 보존 기간을 선택 하 여 장기 백업 보존을 설정 하지 않도록 지정 합니다.

   ![정책 구성](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. 완료되면 **적용** 을 클릭합니다.

> [!IMPORTANT]
> 장기 백업 보존 정책을 사용 하도록 설정 하는 경우 첫 번째 백업이 표시 되 고 복원 하는 데 사용할 수 있는 최대 7 일이 걸릴 수 있습니다. LTR backup cadance에 대 한 자세한 내용은 [장기 백업 보존](../database/long-term-retention-overview.md)을 참조 하세요.

### <a name="view-backups-and-restore-from-a-backup"></a>백업 보기 및 백업에서 복원

LTR 정책으로 특정 데이터베이스에 대해 유지 되는 백업을 보고 해당 백업에서 복원 합니다.

1. Azure Portal에서 관리 되는 인스턴스를 선택 하 고 **백업** 을 클릭 합니다. **사용 가능한 백업** 탭에서 사용 가능한 백업을 보려는 데이터베이스를 선택합니다. **관리** 를 클릭합니다.

   ![데이터베이스 선택](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. **백업 관리** 창에서 사용 가능한 백업을 검토 합니다.

   ![백업 보기](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. 복원 하려는 백업을 선택 하 **고 복원을 클릭 한** 다음 복원 페이지에서 새 데이터베이스 이름을 지정 합니다. 이 페이지에는 백업 및 원본이 미리 채워집니다. 

   ![복원에 사용할 백업 선택](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![복원](./media/long-term-backup-retention-configure/ltr-restore.png)

1. **검토 + 만들기** 를 클릭 하 여 복원 세부 정보를 검토 합니다. 그런 다음, **만들기** 를 클릭 하 여 선택한 백업에서 데이터베이스를 복원 합니다.

1. 도구 모음에서 알림 아이콘을 클릭하여 복원 작업의 상태를 봅니다.

   ![복원 작업 진행률](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 복원 작업이 완료 되 면 **Managed Instance 개요** 페이지를 열어 새로 복원 된 데이터베이스를 확인 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](../database/recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.


## <a name="using-powershell"></a>PowerShell 사용
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL Database에서 계속 지원 되지만, Az. Sql 모듈에서 향후 개발을 수행 합니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

다음 섹션에서는 PowerShell을 사용 하 여 장기 백업 보존을 구성 하 고, Azure storage에서 백업을 보고, Azure storage의 백업에서 복원 하는 방법을 보여 줍니다.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>장기 보존을 관리 하기 위한 Azure RBAC 역할

**AzSqlInstanceDatabaseLongTermRetentionBackup** 및 **AzSqlInstanceDatabase** 의 경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- Managed Instance 참가자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

**AzSqlInstanceDatabaseLongTermRetentionBackup** 의 경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Managed Instance 참여자 역할에는 LTR 백업을 삭제할 수 있는 권한이 없습니다.

Azure RBAC 권한은 *구독* 또는 *리소스 그룹* 범위에서 부여할 수 있습니다. 그러나 삭제 된 인스턴스에 속한 LTR 백업에 액세스 하려면 해당 인스턴스의 *구독* 범위에서 사용 권한을 부여 해야 합니다.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>LTR 정책 만들기

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>LTR 정책 보기

이 예에서는 단일 데이터베이스에 대 한 인스턴스 내에서 LTR 정책을 나열 하는 방법을 보여 줍니다.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

이 예에서는 인스턴스의 모든 데이터베이스에 대 한 LTR 정책을 나열 하는 방법을 보여 줍니다.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>LTR 정책 지우기

이 예제에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여줍니다.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

이 예에서는 인스턴스 내의 LTR 백업을 나열 하는 방법을 보여 줍니다.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 인스턴스를 삭제 한 후 LTR 백업을 삭제 하려면 구독 범위 권한이 있어야 합니다. ' 장기 보존 백업 삭제 ' 작업을 필터링 하 여 Azure Monitor의 각 삭제에 대 한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 이 인터페이스는 변경 되지 않았지만 이제 리소스 ID 매개 변수에는 LTR 백업 리소스 ID가 필요 합니다.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> 인스턴스가 삭제 된 후 LTR 백업에서 복원 하려면 인스턴스 구독으로 범위가 지정 된 사용 권한이 있어야 하며 해당 구독은 활성 상태 여야 합니다. 또한 선택적-ResourceGroupName 매개 변수를 생략 해야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](../database/recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](../database/automated-backups-overview.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](../database/long-term-retention-overview.md) 참조
