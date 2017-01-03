---
title: "Azure PowerShell을 사용하여 데이터 보호 및 복구를 위한 Azure SQL 데이터베이스의 백업 및 복원 시작 | Microsoft 문서"
description: "이 자습서에서는 자동화된 백업에서 특정 시점으로 복원, Azure Recovery Services 자격 증명 모음에 자동화된 백업 저장 및 PowerShell을 사용하여 Azure Recovery Services 자격 증명 복원을 수행하는 방법을 보여 줍니다."
keywords: "sql 데이터베이스 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 570e32d6c5b8f2f247ab9f6cc41caa574400011f
ms.openlocfilehash: 5682f3fc02633558556461070f8deb52fff38e17


---


# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery-using-powershell"></a>PowerShell을 사용하여 데이터 보호 및 복구를 위한 백업 및 복원 시작

이 시작 자습서에서는 Azure PowerShell을 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

- 데이터베이스의 기존 백업 보기
- 이전 시점으로 데이터베이스 복원
- Azure Recovery Services 자격 증명 모음에 데이터베이스 백업 파일의 장기 보존 구성
- Azure Recovery Services 자격 증명 모음에서 데이터베이스 복원

**예상 시간**: 이 자습서의 경우 완료하는 데 약 30분 소요됩니다(이미 필수 조건을 충족한다고 가정).


## <a name="prerequisites"></a>필수 조건

* Azure 계정이 필요합니다. [무료 Azure 계정을 열거나](/pricing/free-trial/?WT.mc_id=A261C142F) 또는 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다. 

* 구독 소유자 또는 참가자 역할의 구성원인 계정을 사용하여 Azure에 연결해야 합니다. RBAC(역할 기반 액세스 제어)에 대한 자세한 내용은 [Azure Portal에서 액세스 관리 시작](../active-directory/role-based-access-control-what-is.md)을 참조하세요.

* 최신 Azure PowerShell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

* [Azure Portal 및 SQL Server Management Studio를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started.md) 또는 이와 동등한 [PowerShell 버전](sql-database-get-started-powershell.md)을 완료했습니다. 완료하지 않았으면 계속하기 전에 이 필수 자습서를 완료하거나 [PowerShell 버전](sql-database-get-started-powershell.md) 끝에서 PowerShell 스크립트를 실행합니다.

> [!TIP]
> [Azure Portal](sql-database-get-started-backup-recovery.md)을 사용하여 시작 자습서에서 이와 동일한 작업을 수행할 수 있습니다.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>데이터베이스의 서비스에서 생성된 백업에서 가장 오래된 복원 지점 보기

자습서의 이 섹션에서는 데이터베이스의 [서비스에서 생성된 자동화된 백업](sql-database-automated-backups.md)에서 가장 오래된 복원 지점에 대한 정보를 봅니다. 

가장 빠른 복원 지점과 사용 가능한 마지막 백업(현재 시간에서 6분 전) 사이의 모든 시점으로 데이터베이스를 복원할 수 있습니다. 

다음 코드 조각은 [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) cmdlet을 사용하여 복원할 데이터베이스의 가장 오래된 복원 지점을 가져옵니다. 시간은 UTC로 반환되지만 다음 코드 조각은 현지 시간으로 작업하는 방법을 보여 줍니다. 라이브 데이터베이스의 사용 가능한 마지막 복원 지점은 일반적으로 약 6분 전이므로 마지막 복원 지점은 현재 시간에서 6분을 뺀 값으로 간단히 설정합니다. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원

자습서의 이 섹션에서는 특정 시점을 기준으로 새 데이터베이스로 데이터베이스를 복원합니다. 

>[!NOTE]
>복원할 서버를 특정 시점으로 변경할 수 없습니다. 다른 서버로 복원하려면 [지역 복원](sql-database-disaster-recovery.md#recover-using-geo-restore)을 사용합니다. 또한 [탄력적 데이터베이스 풀](sql-database-elastic-jobs-overview.md) 또는 다른 가격 책정 계층으로 복원할 수 있습니다. 

다음 코드 조각은 [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) cmdlet을 사용하여 이전 코드 조각에서 설정한 $databaseToRestore를 복원합니다. **-PointInTime** 매개 변수에는 *12/09/2016 20:00:00*과 비슷한 UTC 형식의 시간 값이 필요합니다. 이 코드 조각은 현지 시간을 변환합니다.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 –ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> 여기서는 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)를 통해 복원된 데이터베이스에 연결하여 [기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경할 수 있도록 복원된 데이터베이스에서 일부 데이터를 추출](sql-database-recovery-using-backups.md#point-in-time-restore)하는 등 필요한 작업을 수행할 수 있습니다.

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존 구성 

자습서의 이 섹션에서는 서비스 계층에 대한 보존 기간보다 긴 기간(최대 10년) 동안 [자동화된 백업을 보존하는 Azure Recovery Services 자격 증명 모음을 구성](sql-database-long-term-retention.md)합니다. 

### <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

> [!IMPORTANT]
> 자격 증명 모음은 Azure SQL 논리 서버와 동일한 지역에 있어야 하고 논리 서버와 동일한 리소스 그룹을 사용해야 합니다.

다음 코드 조각은 [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) 및 [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) cmdlet을 사용하여 새 복구 서비스 자격 증명 모음을 만들고 자격 증명 모음의 백업에 대한 중복 수준을 설정합니다. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>장기 보존 백업을 위해 방금 만든 복구 자격 증명 모음을 사용하도록 서버 설정

다음 코드 조각은 [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) cmdlet을 사용하여 이전에 만든 복구 서비스 자격 증명 모음을 특정 Azure SQL 서버와 연결합니다.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName –ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>보존 정책 만들기

보존 정책은 데이터베이스 백업을 보관할 기간을 설정하는 곳입니다. 

다음 코드 조각은 [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject)를 사용하여 새 정책을 만들기 위한 템플릿으로 사용하는 기본 보존 정책을 가져옵니다. 2년 동안 백업을 보존하도록 템플릿을 설정한 다음 [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy)를 실행하여 최종적으로 새 정책을 만듭니다. 

일부 cmdlet은 [Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)를 실행하기 전에 자격 증명 모음 컨텍스트를 설정해야 관련된 몇 가지 코드 조각에서 이 cmdlet을 볼 수 있습니다. 정책이 자격 증명 모음의 일부이기 때문에 컨텍스트를 설정해야 합니다. 각 자격 증명 모음에 대해 여러 보존 정책을 만든 다음 원하는 정책을 특정 데이터베이스에 적용할 수 있습니다. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName –WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>이전에 정의한 보존 정책을 사용하도록 데이터베이스 구성

다음 코드 조각은 [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) cmdlet을 사용하여 새 정책을 특정 데이터베이스에 적용합니다.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName $resourceGroupName –ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> 구성되면 백업은 다음 7일 동안 자격 증명 모음에 표시됩니다. 백업이 자격 증명 모음에 표시된 후에 이 자습서를 계속합니다.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>백업 정보 및 장기 보존 백업 보기

자습서의 이 섹션에서는 [장기 백업 보존](sql-database-long-term-retention.md)에서 데이터베이스 백업에 대한 정보를 봅니다. 

다음 코드 조각은 [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) 및 [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint) cmdlet을 사용하여 자격 증명 모음에서 정보를 쿼리합니다.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>장기 백업 보존의 백업에서 데이터베이스 복원

자습서의 이 섹션에서는 Azure Recovery Services 자격 증명 모음의 백업에서 새 데이터베이스로 데이터베이스를 복원합니다.

이 자습서의 앞부분에 있는 특정 시점 코드 조각과 마찬가지로 장기 백업 보존에서 복원하려면 [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) cmdlet을 사용하지만, 여기서 시간에는 이전에 사용된 **-FromPointInTimeBackup** 매개 변수와는 반대로 **-FromLongTermRetentionBackup** 매개 변수를 사용합니다.

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](sql-database-recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>이전 시점에서 복원하고 PowerShell을 사용하여 장기 백업 보존에서 구성 및 복원하기 위한 Azure PowerShell 스크립트 완성

> [!NOTE]
> 이 스크립트의 끝에서 가장 최근의 백업을 복원하려고 시도할 때 자격 증명 모음에 백업이 없으면 *null 배열에 대한 인덱스를 만들 수 없습니다.* 예외가 발생하게 됩니다.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 –ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName –ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName –WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName $resourceGroupName –ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](https://azure.microsoft.com/en-us/documentation/articles/)(sql-database-automated-backups.MD) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Dec16_HO3-->


