---
title: "장기 보존 백업 및 Azure Recovery Services 자격 증명 모음 삭제 | Microsoft Docs"
description: "장기 보존 백업 및 Azure Recovery Services 자격 증명 모음을 삭제하는 방법에 대한 빠른 참조"
services: sql-database
documentationcenter: 
author: stevestein
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
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 5e5a868ece1fc661b0bf1a83de5a9a30e9852fb1


---
# <a name="delete-long-term-retention-backups"></a>장기 보존 백업 삭제

이 방법 항목에서는 장기 보존 백업 및 Azure Recovery Services 자격 증명 모음을 삭제하는 방법을 알아봅니다.

>[!Warning]
>이 코드를 실행하기 전에 장기 보존 백업을 삭제할 것인지 확인합니다. 이 코드 조각은 원치 않는 비용 청구를 방지하기 위해 [장기 백업 보존](sql-database-long-term-retention.md) 더 이상 필요 없는 백업 및 자격 증명 모음을 사용하는 방법을 알아볼 때 생성된 모든 백업 및 자격 증명 모음을 정리하기 위한 것입니다.

## <a name="delete-long-term-retention-backups-using-powershell"></a>PowerShell을 사용하여 장기 보존 백업 삭제

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

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Jan17_HO2-->


