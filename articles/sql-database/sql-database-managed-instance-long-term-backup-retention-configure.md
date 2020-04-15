---
title: '관리되는 인스턴스: 장기 백업 보존(PowerShell)'
description: PowerShell을 사용하여 Azure SQL Database 관리 형 인스턴스에 대해 별도의 Azure Blob 저장소 컨테이너에 자동화된 백업을 저장하고 복원하는 방법을 알아봅니다.
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
ms.date: 04/14/2020
ms.openlocfilehash: 0af322d589efd48cc224c69cef8e96fb887d9868
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384221"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Azure SQL Database 관리 인스턴스 장기 백업 보존(PowerShell) 관리

Azure SQL Database 관리 인스턴스에서 [장기 백업 보존](sql-database-long-term-retention.md#managed-instance-support) 정책(LTR)을 제한된 공개 미리 보기 기능으로 구성할 수 있습니다. 이렇게 하면 최대 10년 동안 별도의 Azure Blob 저장소 컨테이너에 데이터베이스 백업을 자동으로 유지할 수 있습니다. 그런 다음 PowerShell을 사용하여 이러한 백업을 사용하여 데이터베이스를 복구할 수 있습니다.

   > [!IMPORTANT]
   > 관리되는 인스턴스에 대한 LTR은 현재 미리 보기가 제한되어 있으며 사례별로 EA 및 CSP 구독에 사용할 수 있습니다. 등록을 요청하려면 **백업, 복원 및 비즈니스 연속성/장기 백업 보존**에 대한 지원 항목 아래에 [Azure 지원 티켓을](https://azure.microsoft.com/support/create-ticket/) 만드십시오. 


다음 섹션에서는 PowerShell을 사용하여 장기 백업 보존을 구성하고 Azure SQL 스토리지에서 백업을 확인하고 Azure SQL 스토리지의 백업에서 복원하는 방법을 보여줍니다.

## <a name="rbac-roles-to-manage-long-term-retention"></a>장기 보존을 관리하는 RBAC 역할

**Get-AzSqlInstance데이터베이스장기보존백업** 및 **복원-AzSqlInstanceDatabase의**경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 스컨드드 인스턴스 기여자 역할 또는
- 다음과 같은 권한이 있는 사용자 지정 역할:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read``` ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read```
   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read```

**제거-AzSqlInstance데이터베이스장기보존백업의**경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 다음과 같은 권한을 가진 사용자 지정 역할:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

> [!NOTE]
> SManaged 인스턴스 기여자 역할에는 LTR 백업을 삭제할 수 있는 권한이 없습니다.

*RBAC* 권한은 구독 또는 *리소스 그룹* 범위에서 부여될 수 있습니다. 그러나 삭제된 인스턴스에 속하는 LTR 백업에 액세스하려면 해당 인스턴스의 *구독* 범위에서 권한을 부여해야 합니다.

```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

## <a name="create-an-ltr-policy"></a>LTR 정책 만들기

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
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16

```

## <a name="view-ltr-policies"></a>LTR 정책 보기

이 예제에서는 인스턴스 내에서 LTR 정책을 나열하는 방법을 보여 주며

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup

```

## <a name="clear-an-ltr-policy"></a>LTR 정책 지우기

이 예제에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여줍니다.

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR 백업 보기

이 예제에서는 인스턴스 내에서 LTR 백업을 나열하는 방법을 보여 주며 있습니다.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase

```

## <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 인스턴스가 삭제된 후 LTR 백업을 삭제하려면 구독 범위 권한이 있어야 합니다. '장기 보존 백업 삭제' 작업을 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../azure-monitor/platform/alerts-activity-log.md)를 참조하세요.

## <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 이 인터페이스는 변경되지 않았지만 이제 리소스 ID 매개 변수에 LTR 백업 리소스 ID가 필요합니다.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName

```

> [!IMPORTANT]
> 인스턴스가 삭제된 후 LTR 백업에서 복원하려면 인스턴스 구독에 대한 범위가 조정된 권한이 있어야 하며 해당 구독이 활성 상태여야 합니다. 또한 선택적 -ResourceGroupName 매개 변수를 생략해야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
