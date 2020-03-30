---
title: PowerShell을 통해 Azure 파일 공유 백업 관리
description: PowerShell을 사용하여 Azure 백업 서비스에서 백업하는 Azure 파일 공유를 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083172"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>PowerShell을 통해 Azure 파일 공유 백업 관리

이 문서에서는 Azure PowerShell을 사용하여 Azure Backup 서비스에서 백업하는 Azure 파일 공유를 관리하고 모니터링하는 방법을 설명합니다.

> [!WARNING]
> PS 버전이 AFS 백업의 경우 'Az.RecoveryServices 2.6.0'의 최소 버전으로 업그레이드되었는지 확인합니다. 자세한 내용은 이 [변경에](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) 대한 요구 사항을 설명하는 섹션을 참조하십시오.

## <a name="modify-the-protection-policy"></a>보호 정책 수정

Azure 파일 공유를 백업하는 데 사용되는 정책을 변경하려면 [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)을 사용합니다. 관련 백업 항목과 새 백업 정책을 지정합니다.

다음 예제는 **testAzureFS** 보호 정책을 **dailyafs**에서 **monthlyafs**로 변경합니다.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>백업 및 복원 작업 추적

온디맨드 백업 및 복원 작업은 주문형 백업을 실행할 때와 같이 ID와 함께 작업을 [반환합니다.](backup-azure-afs-automation.md#trigger-an-on-demand-backup) [Get-AzRecovery서비스백업작업](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet을 사용하여 작업 진행 상황 및 세부 정보를 추적합니다.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 모든 향후 백업 작업을 중지하고 모든 복구 지점을 *삭제합니다.*
* 모든 향후 백업 작업을 중지하지만 복구 지점을 *남겨 둡니다.*

Azure Backup에서 만든 기본 스냅숏이 유지되기 때문에 저장소에 복구 지점을 두는 것과 관련된 비용이 발생할 수 있습니다. 그러나 복구 지점을 남기면 원하는 경우 나중에 파일 공유를 복원할 수 있습니다. 복구 지점을 떠나는 비용에 대한 자세한 내용은 [가격 세부 정보를](https://azure.microsoft.com/pricing/details/storage/files/)참조하십시오. 모든 복구 지점을 삭제하도록 선택하면 파일 공유를 복원할 수 없습니다.

## <a name="stop-protection-and-retain-recovery-points"></a>보호 중지 및 복구 지점 유지

데이터를 유지하면서 보호를 중지하려면 [비활성화-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) cmdlet을 사용합니다.

다음 예제는 *afsfileshare* 파일 공유에 대한 보호를 중지하지만 모든 복구 지점을 유지합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

출력의 Job ID 특성은 "보호 중지" 작업에 대한 백업 서비스에서 만든 작업의 작업 ID에 해당합니다. 작업의 상태를 추적하려면 [Get-AzRecovery서비스백업작업](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0) cmdlet을 사용합니다.

## <a name="stop-protection-without-retaining-recovery-points"></a>복구 지점을 유지하지 않고 보호 중지

복구 지점을 유지하지 않고 보호를 중지하려면 [비활성화-AzRecovery서비스백업보호](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) cmdlet을 사용하고 **-RemoveRecoveryPoints** 매개 변수를 추가합니다.

다음 예제는 복구 지점을 유지하지 않고 *afsfileshare* 파일 공유에 대한 보호를 중지합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>다음 단계

Azure 포털에서 Azure 파일 공유 백업 관리에 [대해 자세히 알아봅니다.](manage-afs-backup.md)
