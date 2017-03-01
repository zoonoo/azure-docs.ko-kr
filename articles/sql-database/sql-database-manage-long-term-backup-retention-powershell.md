---
title: "PowerShell: 장기 데이터베이스 백업 보존(Azure) 관리 | Microsoft 문서"
description: "PowerShell을 사용하여 Azure Recovery Services에서 자동화된 Azure SQL Database 백업을 장기 보존에서 구성, 관리 및 복원하는 방법에 대한 빠른 참조"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: carlrab; sstein
translationtype: Human Translation
ms.sourcegitcommit: f234e50d194c1b8b8deed8282a6ab1250b53d075
ms.openlocfilehash: c0f073578ecf067d115a143edf4e797c4e8b5200
ms.lasthandoff: 02/16/2017


---
# <a name="configure-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-powershell"></a>PowerShell을 사용하여 Azure Recovery Services 자격 증명 모음에 데이터베이스 백업의 장기 보존 구성
이 항목에서는 Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존을 구성, 관리 및 복원하는 방법에 대해 알아봅니다. [Azure Portal](sql-database-manage-long-term-backup-retention-portal.md)을 사용하여 이 작업을 수행할 수도 있습니다.

장기 백업 보존에 대한 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!TIP]
> 자습서는 [PowerShell을 사용하여 데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery-powershell.md)을 참조하세요.
>

## <a name="configure-long-term-backup-retention-using-powershell"></a>PowerShell을 사용하여 장기 백업 보존 구성

장기 보존을 구성할 때는 다음 단계를 수행하기 위해 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)이 필요합니다.

1. SQL Database 서버와 동일한 지역, 구독 및 리소스 그룹에서 Azure Recovery Services 자격 증명 모음을 만듭니다. ([New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault), [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties))
2. 자격 증명 모음에 Azure SQL Server를 등록합니다([Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault)).
3. 보존 정책을 만듭니다([New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy)).
4. 장기 백업 보존이 필요한 데이터베이스에 보호 정책을 적용합니다([Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy)).

자세한 내용은 [최대 10년 동안 Azure SQL Database 백업 저장](sql-database-long-term-retention.md)을 참조하세요.

자습서는 [PowerShell을 사용하여 데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery-powershell.md)을 참조하세요.

```
# Configure long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseToBackup = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location


# Create an Azure recovery services vault
#########################################
$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault


# Register your Azure SQL server to the vault
#############################################
Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Create a retention policy 
###########################
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values to 1 year (set to anytime between 1 week and 10 years)
#################################################################################
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 1

$retentionPolicyName = "myOneYearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
####################################################################
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
#######################
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy


# Apply the retention policy to the database to backup 
######################################################
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseToBackup -State "enabled" -ResourceId $policy.Id
```

## <a name="restore-from-long-term-backup-retention-using-powershell"></a>PowerShell을 사용하여 장기 백업 보존에서 복원

장기 백업 보존에서 데이터베이스를 복원하려면 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)이 필요하며 다음 cmdlet을 사용합니다.

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)
- [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase)


```
# Restore a database from long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseNeedingRestore = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$restoredDatabaseName = "{restored-db-name}"
$edition = "{restored-db-edition}"
$performanceLevel = "{restored-db-slo}"


# Set the vault context to the vault we want to restore from
############################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
##########################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters for specific backups
#############################################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups

if (!$availableBackups)
{
    Write-Host "No backups available"
}
else
{
    # Restore the most recent available backup: $availableBackups[0]
    ################################################################
    $restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName ` 
     -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
    $restoredDb
}
```

## <a name="view-long-term-backup-retention-information-using-the-powershell"></a>PowerShell을 사용하여 장기 백업 보존 정보 보기

장기 보존에서 백업을 보려면 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)이 필요하며 다음 cmdlet을 사용합니다.

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)

자세한 내용은 [최대 10년 동안 Azure SQL Database 백업 저장](sql-database-long-term-retention.md)을 참조하세요.

```
# View the available backups in long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$recoveryServiceVaultName = "{vault-name}"

# Set the vault context to the vault we want to query
#####################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with the container
####################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase


# Get all available backups
# Optionally, set the -StartDate and -EndDate parameters
########################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

## <a name="delete-long-term-retention-backups-using-powershell"></a>PowerShell을 사용하여 장기 보존 백업 삭제

>[!Warning]
>이 코드를 실행하기 전에 장기 보존 백업을 삭제할 것인지 확인합니다. 이 코드 조각은 원치 않는 비용 청구를 방지하기 위해 [장기 백업 보존](sql-database-long-term-retention.md) 더 이상 필요 없는 백업 및 자격 증명 모음을 사용하는 방법을 알아볼 때 생성된 모든 백업 및 자격 증명 모음을 정리하기 위한 것입니다.

장기 보존에서 백업을 삭제하려면 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)이 필요하며 다음 cmdlet을 사용합니다.

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Disable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/disable-azurermrecoveryservicesbackupprotection)
- [Unregister-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/unregister-azurermrecoveryservicesbackupcontainer)
- [Remove-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/remove-azurermrecoveryservicesvault)


자세한 내용은 [최대 10년 동안 Azure SQL Database 백업 저장](sql-database-long-term-retention.md)을 참조하세요.

```
# Delete long-term backups
##########################

$resourceGroupName = "{resource-group-name}"
$recoveryServiceVaultName = "{vault-name}"

$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

ForEach ($container in $containers)
{
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase

   ForEach ($item in $items)
   {
      # Remove the backups from the vault
      ###################################
      Disable-AzureRmRecoveryServicesBackupProtection -item $item -RemoveRecoveryPoints -ea SilentlyContinue
   }
   
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
# Delete the recovery services vault
####################################
Remove-AzureRmRecoveryServicesVault -Vault $vault
```

## <a name="next-steps"></a>다음 단계

- 백업에 Azure Portal을 사용하여 장기 백업 보존을 관리하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-manage-long-term-backup-retention-portal.md)를 참조하세요.
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
