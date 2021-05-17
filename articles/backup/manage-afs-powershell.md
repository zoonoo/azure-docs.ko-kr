---
title: PowerShell을 사용하여 Azure 파일 공유 백업 관리
description: PowerShell을 사용하여 Azure Backup 서비스로 백업한 Azure 파일 공유를 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89000229"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>PowerShell을 사용하여 Azure 파일 공유 백업 관리

이 문서에서는 Azure PowerShell을 사용하여 Azure Backup 서비스로 백업한 Azure 파일 공유를 관리하고 모니터링하는 방법을 설명합니다.

> [!WARNING]
> AFS 백업의 경우 PowerShell 버전이 'Az.RecoveryServices 2.6.0'의 최소 버전으로 업그레이드되었는지 확인합니다. 자세한 내용은 이 변경에 대한 요구 사항을 설명하는 [섹션](backup-azure-afs-automation.md#important-notice-backup-item-identification)을 참조하세요.

## <a name="modify-the-protection-policy"></a>보호 정책 수정

Azure 파일 공유를 백업하는 데 사용되는 정책을 변경하려면 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection)을 사용합니다. 관련 백업 항목 및 새 백업 정책을 지정합니다.

다음 예제는 **testAzureFS** 보호 정책을 **dailyafs** 에서 **monthlyafs** 로 변경합니다.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>백업 및 복원 작업 추적

주문형 백업 및 복원 작업은 작업과 [주문형 백업을 실행](backup-azure-afs-automation.md#trigger-an-on-demand-backup)할 때 표시된 ID를 함께 반환합니다. [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet을 사용하면 작업 진행률 및 세부 정보를 추적할 수 있습니다.

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

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 *삭제*
* 이후의 모든 백업 작업은 중지하지만 복구 지점은 *그대로 유지*

복구 지점을 유지할 경우 Azure Backup에서 만드는 기본 스냅샷이 보관되므로 비용이 발생할 수 있습니다. 그러나 복구 지점을 유지할 경우 나중에 원할 때 파일 공유를 복원할 수 있다는 장점이 있습니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요. 모든 복구 지점을 삭제하기로 선택하면 파일 공유를 복원할 수 없습니다.

## <a name="stop-protection-and-retain-recovery-points"></a>보호 중지 및 복구 지점 보존

데이터를 유지하는 동안 보호를 중지하려면 [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) cmdlet을 사용합니다.

다음 예에서는 *afsfileshare* 파일 공유에 대한 보호를 중지하지만 모든 복구 지점을 유지합니다.

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

출력의 작업 ID 특성은 보호 중지 작업을 위해 백업 서비스에서 만든 작업의 작업 ID에 해당합니다. 작업의 상태를 추적하려면 [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet을 사용합니다.

## <a name="stop-protection-without-retaining-recovery-points"></a>복구 지점을 유지하지 않고 보호 중지

복구 지점을 유지하지 않고 보호를 중지하려면 [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) cmdlet을 사용하고 **-RemoveRecoveryPoints** 매개 변수를 추가합니다.

다음 예제에서는 복구 지점을 유지하지 않고 *afsfileshare* 파일 공유에 대한 보호를 중지합니다.

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

Azure Portal에서 Azure 파일 공유 백업을 관리하는 방법을 [알아봅니다](manage-afs-backup.md).
