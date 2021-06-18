---
title: 가상 머신의 일시 삭제
description: 가상 머신의 일시 삭제로 백업을 더욱 안전하게 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 5bc103f894a6cbcfc06cce4a5f07b70fcea5b1d6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678103"
---
# <a name="soft-delete-for-virtual-machines"></a>가상 머신의 일시 삭제

VM의 일시 삭제는 의도치 않은 삭제로부터 VM의 백업을 보호합니다. 백업이 삭제된 후에도 14일 동안 일시 삭제 상태로 보존됩니다.

> [!NOTE]
> 일시 삭제는 삭제된 백업 데이터만 보호합니다. 백업을 사용하지 않고 VM이 삭제된 경우 일시 삭제 기능은 데이터를 보존하지 않습니다. 모든 리소스는 전체 복원력을 보장하기 위해 Azure Backup으로 보호되어야 합니다.
>

## <a name="supported-regions"></a>지원되는 지역

일시 삭제는 현재 미국 서부, 동아시아, 캐나다 중부, 캐나다 동부, 프랑스 중부, 프랑스 남부, 한국 중부, 한국 남부, 영국 남부, 영국 서부, 오스트레일리아 동부, 오스트레일리아 남동부, 북유럽, 미국 서부, 미국 서부 2, 미국 중부, 동남 아시아, 미국 중북부, 미국 중남부, 일본 동부, 일본 서부, 인도 남부, 인도 중부, 인도 서부, 미국 동부 2, 스위스 북부, 스위스 서부, 노르웨이 서부, 노르웨이 동부 및 모든 국가별 지역에서 지원됩니다.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Azure Portal를 사용하는 VM의 일시 삭제

1. VM의 백업 데이터를 삭제하려면 백업을 중지해야 합니다. Azure Portal에서 Recovery Services 자격 증명 모음으로 이동하여 백업 항목을 마우스 오른쪽 클릭하고 **백업 중지** 를 선택합니다.

   ![Azure Portal 백업 항목 스크린샷](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 다음 창에는 백업 데이터를 삭제하거나 보존할 수 있는 옵션이 제공됩니다. **백업 데이터 삭제**, **백업 중지** 를 차례로 선택하면 VM 백업이 영구적으로 삭제되지 않습니다. 대신 백업 데이터는 일시 삭제된 상태에서 14일 동안 보존됩니다. **백업 데이터 삭제** 를 선택하면 이메일 삭제 경고가 구성된 이메일 ID로 전송되어 백업 데이터의 연장 보존 기간이 14일 남았음을 사용자에게 알립니다. 또한 삭제된 데이터를 복원하려면 2일 더 남았다는 이메일 경고가 12일째에 전송됩니다. 영구 삭제가 발생하고 데이터의 영구 삭제에 대해 알리는 마지막 이메일 경고가 전송되는 15일째가 될 때까지 삭제가 연기됩니다.

   ![Azure Portal의 백업 중지 화면 스크린샷](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 이 14일 동안 Recovery Services 자격 증명 모음에 일시 삭제된 VM 옆에 빨간색 "일시 삭제" 아이콘이 표시됩니다.

   ![Azure Portal에서 일시 삭제 상태인 VM 스크린샷](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 일시 삭제된 백업 항목이 자격 증명 모음에 보존되어 있는 경우 해당 시점에 자격 증명 모음을 삭제할 수 없습니다. 백업 항목이 영구적으로 삭제된 후 자격 증명 모음을 삭제하면 자격 증명 모음에 일시 삭제된 상태로 남아 있는 항목이 없습니다.

4. 일시 삭제된 VM을 복원하려면 먼저 삭제를 취소해야 합니다. 삭제를 취소하려면 일시 삭제된 VM을 선택하고 **삭제 취소** 옵션을 선택합니다.

   ![Azure Portal에서 삭제 취소된 VM 스크린샷](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   삭제 취소를 선택하면 경고를 표시하는 창이 나타나며 VM의 모든 복원 시점은 삭제 취소되고 복원 작업을 수행할 수 있게 됩니다. VM은 "데이터 보존을 사용하는 보호 중지" 상태로 유지되며 백업이 일시 중지되고 백업 정책이 적용되지 않은 상태로 백업 데이터가 영구적으로 유지됩니다.

   ![Azure Portal에서 삭제 취소 확인된 VM 스크린샷](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   이 시점에 선택한 복원 시점으로부터 **VM 복원** 을 선택하여 VM을 복원할 수도 있습니다.  

   ![Azure Portal의 VM 복원 옵션 스크린샷](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 사용자가 **백업 다시 시작** 작업을 수행한 후 가비지 수집기가 만료된 복구 지점만 실행 및 정리합니다.

5. 삭제 취소 프로세스가 완료되면 "데이터 보존을 사용하는 백업 중지" 상태로 돌아간 다음 **백업 다시 시작** 을 선택할 수 있습니다. **백업 다시 시작** 작업은 백업 및 보유 일정을 정의하는 사용자가 선택한 백업 정책과 연결된 활성 상태의 백업 항목을 다시 가져옵니다.

   ![Azure Portal의 백업 다시 시작 옵션 스크린샷](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure PowerShell을 사용하는 VM의 일시 삭제

> [!IMPORTANT]
> Azure PowerShell를 사용하여 일시 삭제를 사용하는 데 필요한 Az.RecoveryServices 최소 버전은 2.2.0입니다. 최신 버전을 받으려면 ```Install-Module -Name Az.RecoveryServices -Force```를 사용합니다.

Azure Portal에 대해 위에서 설명한 것처럼 Azure PowerShell를 사용할 때에도 단계 순서가 동일합니다.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell을 사용하여 백업 항목 삭제

[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용하여 백업 항목을 삭제합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

백업 항목의 'DeleteState'가 'NotDeleted'에서 'ToBeDeleted'로 바뀝니다. 백업 데이터는 14일 동안 보존됩니다. 삭제 작업을 되돌리려는 경우 삭제 실행 취소를 수행해야 합니다.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell을 사용하여 삭제 작업 실행 취소

먼저, 일시 삭제 상태(삭제 예정)인 관련 백업 항목을 가져옵니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

그런 다음 [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet을 사용하여 삭제 취소 작업을 수행합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

백업 항목의 ' DeleteState'가 ' NotDeleted'로 돌아갑니다. 하지만 보호는 여전히 중지됩니다. [백업을 다시 시작](./backup-azure-vms-automation.md#change-policy-for-backup-items)하여 보호를 다시 사용하도록 설정합니다.

## <a name="soft-delete-for-vms-using-rest-api"></a>REST API를 사용하는 VM의 일시 삭제

- [여기](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)에 언급된 REST API를 사용하는 백업을 삭제합니다.
- 이러한 삭제 작업을 실행 취소하려는 경우 [여기](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)에 언급된 단계를 참조하세요.

## <a name="how-to-disable-soft-delete"></a>일시 삭제를 사용하지 않도록 설정하는 방법

이 기능은 사용하도록 설정하는 것이 좋습니다. 일시 삭제를 사용하지 않도록 설정해야 하는 유일한 상황은 보호된 항목을 새 자격 증명 모음으로 이동할 예정이며 삭제하고 다시 보호하기 전에 필요한 14일을 기다릴 수 없는 경우(예: 테스트 환경)입니다. 일시 삭제를 사용하지 않도록 설정하는 방법에 대한 지침은 [일시 삭제 사용 및 사용 안 함](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 일시 삭제에 대한 [질문과 대답](backup-azure-security-feature-cloud.md#frequently-asked-questions) 확인
- [Azure Backup의 모든 보안 기능](security-overview.md) 확인
