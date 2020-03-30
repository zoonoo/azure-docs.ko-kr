---
title: 클라우드 워크로드를 보호하는 데 도움이 되는 보안 기능
description: Azure Backup에서 보안 기능을 사용하여 백업을 보다 안전하게 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 57c9fd76ae32aea49f480f2a88d8296538d8052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156075"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Azure Backup을 사용하는 클라우드 워크로드를 보호하는 데 도움이 되는 보안 기능

맬웨어, 랜섬웨어 및 침입과 같은 보안 문제에 대한 우려는 증가하고 있습니다. 이러한 보안 문제는 돈과 데이터 측면 모두에서 비용이 많이 들 수 있습니다. 이러한 공격을 막기 위해 Azure Backup은 이제 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다.

이러한 기능 중 하나는 소프트 삭제입니다. 소프트 삭제를 사용하면 악의적인 행위자가 VM의 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 14일 동안 유지되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "소프트 삭제" 상태에서 백업 데이터를 14일 더 보존해도 고객에게 비용이 발생하지 않습니다. 또한 Azure는 [저장소 서비스 암호화를](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 사용하여 백업된 모든 데이터를 암호화하여 데이터를 더욱 안전하게 보호합니다.

Azure 가상 시스템에 대한 소프트 삭제 보호를 일반적으로 사용할 수 있습니다.

>[!NOTE]
>Azure VM의 SQL 서버에 대한 소프트 삭제 및 Azure VM 워크로드의 SAP HANA에 대한 소프트 삭제를 미리 보기에서 사용할 수 있습니다.<br>
>미리 보기에 등록하려면AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>소프트 삭제

### <a name="soft-delete-for-vms"></a>VM에 대한 소프트 삭제

VM에 대한 소프트 삭제는 VM의 백업을 의도하지 않은 삭제로부터 보호합니다. 백업이 삭제된 후에도 14일 동안 일시 삭제 상태로 유지됩니다.

> [!NOTE]
> 소프트 삭제는 삭제된 백업 데이터만 보호합니다. 백업 없이 VM을 삭제하면 소프트 삭제 기능이 데이터를 보존하지 않습니다. 모든 리소스는 전체 복원력을 보장하기 위해 Azure Backup으로 보호되어야 합니다.
>

### <a name="supported-regions"></a>지원되는 지역

소프트 삭제는 현재 미국 서부 중부, 동아시아, 캐나다 중부, 캐나다 동부, 프랑스 중부, 프랑스 한국, 한국 중부, 한국 남부, 영국 남부, 영국 서부, 호주 동부, 호주 남동부, 북유럽, 미국 서부, 미국 서부, 미국 중부, 남부에서 지원됩니다. 동아시아, 미국 중남부, 미국 중남부, 일본 동부, 일본 서부, 인도 남부, 인도 중부, 인도 서부, 미국 동부 2, 스위스 북부, 스위스 서부 및 모든 국가 지역.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Azure 포털을 사용하는 VM에 대한 소프트 삭제

1. VM의 백업 데이터를 삭제하려면 백업을 중지해야 합니다. Azure 포털에서 복구 서비스 자격 증명 모음으로 이동하여 백업 항목을 마우스 오른쪽 단추로 클릭하고 **백업 중지를**선택합니다.

   ![Azure 포털 백업 항목의 스크린샷](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 다음 창에서는 백업 데이터를 삭제하거나 유지할 수 있습니다. **백업 데이터 삭제를** 선택한 다음 **백업 중지를**선택하면 VM 백업이 영구적으로 삭제되지 않습니다. 대신 백업 데이터는 일시 삭제된 상태에서 14일 동안 유지됩니다. **백업 데이터 삭제를** 선택하면 백업 데이터에 대한 보존 기간이 14일 남아 있음을 알리는 이메일 삭제 경고가 구성된 전자 메일 ID로 전송됩니다. 또한 삭제된 데이터를 부활시키기 위해 이틀이 더 남았다는 알림이 12일에 전송됩니다. 삭제는 영구 삭제가 발생하고 데이터의 영구 삭제에 대한 최종 이메일 경고가 전송되는 15일까지 지연됩니다.

   ![Azure 포털의 스크린샷, 백업 중지 화면](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 14일 동안 복구 서비스 볼트에서 일시 삭제된 VM옆에 빨간색 "소프트 삭제" 아이콘이 표시됩니다.

   ![Azure 포털의 스크린샷, VM의 연약한 삭제 상태](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 일시 삭제된 백업 항목이 볼트에 있는 경우 해당 시점에 볼트를 삭제할 수 없습니다. 백업 항목이 영구적으로 삭제되고 볼트에 남아 있는 일시 삭제 된 상태의 항목이 없는 경우 볼트 삭제를 시도하십시오.

4. 일시 삭제된 VM을 복원하려면 먼저 삭제취소해야 합니다. 삭제를 취소하려면 일시 삭제된 VM을 선택한 다음 삭제 취소 옵션을 **선택합니다.**

   ![Azure 포털의 스크린샷, 삭제 취소 VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   삭제 취소를 선택하면 VM의 모든 복원 지점이 삭제 취소되고 복원 작업을 수행할 수 있다는 경고가 표시됩니다. VM은 백업이 일시 중지되고 백업 데이터가 유효하지 않은 상태로 영원히 유지되는 "데이터 유지 를 사용한 중지 보호" 상태로 유지됩니다.

   ![Azure 포털의 스크린샷, VM 삭제 취소 확인](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   이 시점에서 선택한 복원 지점에서 VM 복원을 선택하여 **VM을 복원할** 수도 있습니다.  

   ![Azure 포털의 스크린샷, VM 복원 옵션](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 가비지 수집기는 사용자가 **다시 시작 백업** 작업을 수행한 후에만 만료된 복구 지점을 실행하고 정리합니다.

5. 삭제 취소 프로세스가 완료되면 상태가 "유지 데이터로 백업 중지"로 돌아온 다음 **백업 다시 시작을**선택할 수 있습니다. **백업 다시 시작** 작업은 백업 및 보존 일정을 정의하는 사용자가 선택한 백업 정책과 연결된 활성 상태로 백업 항목을 다시 가져옵니다.

   ![Azure 포털의 스크린샷, 백업 다시 시작 옵션](./media/backup-azure-security-feature-cloud/resume-backup.png)

이 순서도는 Soft Delete를 사용하도록 설정한 경우 백업 항목의 다양한 단계와 상태를 보여 주며 다음과 같은 단계를 보여 주며 다음과 같은 단계를 보여 주며 다음과 같은 단계를 보여 주며 있습니다.

![일시 삭제된 백업 항목의 수명 주기](./media/backup-azure-security-feature-cloud/lifecycle.png)

자세한 내용은 아래자주 [묻는 질문](backup-azure-security-feature-cloud.md#frequently-asked-questions) 섹션을 참조하십시오.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure PowerShell을 사용하는 VM에 대한 소프트 삭제

> [!IMPORTANT]
> Azure PS를 사용하여 소프트 삭제를 사용하는 데 필요한 Az.RecoveryServices 버전은 최소 2.2.0입니다. 최신 ```Install-Module -Name Az.RecoveryServices -Force``` 버전을 얻는 데 사용합니다.

Azure Portal에 대해 위에서 설명한 대로 Azure PowerShell을 사용하는 동안 단계 순서는 동일합니다.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell을 사용하여 백업 항목 삭제

[비활성화-AzRecovery서비스백업보호](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet을 사용하여 백업 항목을 삭제합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

백업 항목의 '삭제 상태'가 '삭제되지 않음'에서 'ToBeDeleted'로 변경됩니다. 백업 데이터는 14일 동안 유지됩니다. 삭제 작업을 되돌리려면 삭제 취소를 수행해야 합니다.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell을 사용하여 삭제 작업 취소

먼저, 일시 삭제 상태(즉, 삭제될 예정)에 있는 관련 백업 항목을 가져옵니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

그런 다음 실행 [취소-AzRecovery서비스BackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS cmdlet을 사용하여 실행 취소 작업을 수행합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

백업 항목의 '삭제 상태'가 '삭제되지 않음'으로 되돌아갑니다. 그러나 보호는 여전히 중지됩니다. [백업을 다시 시작하여](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) 보호를 다시 활성화합니다.

### <a name="soft-delete-for-vms-using-rest-api"></a>REST API를 사용하는 VM에 대한 소프트 삭제

- [여기에](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)언급 된 대로 REST API를 사용하여 백업을 삭제합니다.
- 사용자가 이러한 삭제 작업을 취소하려면 [여기에](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)언급된 단계를 참조하십시오.

## <a name="disabling-soft-delete"></a>소프트 삭제 사용 설정 사용 설정

새로 생성된 볼트에서 소프트 삭제가 기본적으로 활성화되어 실수로 또는 악의적인 삭제로부터 백업 데이터를 보호합니다.  이 기능을 사용하지 않도록 설정하는 것은 권장되지 않습니다. 소프트 삭제를 사용하지 않도록 설정해야 하는 유일한 상황은 보호된 항목을 새 볼트로 옮길 계획이며 테스트 환경과 같이 삭제 및 다시 보호하기 전에 필요한 14일을 기다릴 수 없는 경우입니다. 볼트 소유자만 이 기능을 비활성화할 수 있습니다. 이 기능을 사용하지 않도록 설정하면 나중에 보호된 항목을 모두 삭제하면 복원할 수 없는 즉시 제거됩니다. 이 기능을 사용하지 않도록 설정하기 전에 일시 삭제된 상태로 있는 백업 데이터는 14일 동안 일시 삭제된 상태로 유지됩니다. 영구적으로 즉시 삭제하려면 삭제를 취소하고 다시 삭제해야 영구적으로 삭제됩니다.

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure 포털을 사용하여 소프트 삭제 를 사용하지 않도록 설정

소프트 삭제를 사용하지 않으려면 다음 단계를 따르십시오.

1. Azure 포털에서 자격 증명 모음으로 이동한 다음 **설정** -> **속성으로**이동합니다.
2. 속성 창에서 보안 **설정** -> **업데이트를**선택합니다.  
3. 보안 설정 창에서 **소프트 삭제**에서 '비활성화 를 선택 **합니다.**

![소프트 삭제 사용 안 함](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure PowerShell을 사용하여 소프트 삭제 를 사용하지 않도록 설정

> [!IMPORTANT]
> Azure PS를 사용하여 소프트 삭제를 사용하는 데 필요한 Az.RecoveryServices 버전은 최소 2.2.0입니다. 최신 ```Install-Module -Name Az.RecoveryServices -Force``` 버전을 얻는 데 사용합니다.

비활성화하려면 [설정-AzRecoveryServicesVault백업속성](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS cmdlet을 사용합니다.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API를 사용하여 소프트 삭제 를 사용하지 않도록 설정

REST API를 사용하여 소프트 삭제 기능을 사용하지 않으려면 [여기에](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)언급된 단계를 참조하십시오.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>일시 삭제된 백업 항목을 영구적으로 삭제

이 기능을 비활성화하기 전에 일시 삭제 된 상태의 백업 데이터는 일시 삭제 된 상태로 유지됩니다. 즉시 영구적으로 삭제하려면 삭제를 취소하고 다시 삭제하여 영구적으로 삭제하십시오.

### <a name="using-azure-portal"></a>Azure Portal 사용

다음 단계를 수행하세요.

1. [소프트 삭제를 사용하지 않도록 설정하는](#disabling-soft-delete)단계에 따라 .
2. Azure 포털에서 자격 증명 모음으로 이동하여 **백업 항목으로**이동한 다음 삭제된 소프트 VM을 선택합니다.

   ![삭제된 VM 소프트 선택](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. 삭제 취소 옵션을 **선택합니다.**

   ![삭제 취소 선택](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. 창이 나타납니다. **삭제 취소를 선택합니다.**

   ![삭제 취소 선택](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. **백업 데이터 삭제를** 선택하여 백업 데이터를 영구적으로 삭제합니다.

   ![백업 데이터 삭제 선택](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 복구 지점을 삭제할 지 확인하려면 백업 항목의 이름을 입력합니다.

   ![백업 항목의 이름 입력](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 항목의 백업 데이터를 삭제하려면 **을 선택합니다.** 알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

소프트 삭제를 사용하지 않도록 설정하기 전에 항목이 삭제된 경우 해당 항목은 일시 삭제된 상태가 됩니다. 삭제 작업을 즉시 삭제하려면 삭제 작업을 취소한 다음 다시 수행해야 합니다.

일시 삭제된 상태의 항목을 식별합니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

그런 다음 소프트 삭제가 활성화되었을 때 수행된 삭제 작업을 반대로 합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

이제 소프트 삭제가 비활성화되었으므로 삭제 작업으로 인해 백업 데이터가 즉시 제거됩니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API 사용

소프트 삭제를 사용하지 않도록 설정하기 전에 항목이 삭제된 경우 해당 항목은 일시 삭제된 상태가 됩니다. 삭제 작업을 즉시 삭제하려면 삭제 작업을 취소한 다음 다시 수행해야 합니다.

1. 먼저 [여기에](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)언급 된 단계로 삭제 작업을 취소합니다.
2. 그런 다음 [여기에](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)언급 된 단계를 사용하여 REST API를 사용하여 소프트 삭제 기능을 사용하지 않도록 설정합니다.
3. 그런 다음 [여기에](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)언급 된 대로 REST API를 사용하여 백업을 삭제합니다.

## <a name="encryption"></a>암호화

백업된 모든 데이터는 Azure Storage 암호화를 사용하여 클라우드에 저장될 때 자동으로 암호화되어 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. 미사용 데이터는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 AES 암호화를 사용하여 암호화되며 FIPS 140-2를 준수합니다.

미사용 암호화 외에도 전송 중의 모든 백업 데이터가 HTTPS를 통해 전송됩니다. 항상 Azure 백본 네트워크에 남아 있습니다.

자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화를](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)참조하십시오. 암호화에 대해 궁금한 점을 질문에 답하려면 [Azure 백업 FAQ를](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) 참조하십시오.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>플랫폼 관리 키를 사용한 백업 데이터 암호화

기본적으로 모든 데이터는 플랫폼 관리 키를 사용하여 암호화됩니다. 이 암호화를 활성화하기 위해 끝에서 명시적 작업을 수행할 필요가 없으며 복구 서비스 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>고객 관리 키를 사용하여 백업 데이터 암호화

Azure 가상 컴퓨터를 백업할 때 이제 소유 및 관리되는 키를 사용하여 데이터를 암호화할 수 있습니다. Azure Backup을 사용하면 Azure 키 볼트에 저장된 RSA 키를 사용하여 백업을 암호화할 수 있습니다. 백업을 암호화하는 데 사용되는 암호화 키는 원본에 사용되는 암호화 키와 다를 수 있습니다. 데이터는 AES 256 기반 데이터 암호화 키(DEK)를 사용하여 보호되며, 이 키는 키를 사용하여 보호됩니다. 이렇게 하면 데이터와 키를 완전히 제어할 수 있습니다. 암호화를 허용하려면 복구 서비스 자격 증명 모음에 Azure 키 자격 증명 모음의 암호화 키에 대한 액세스 권한이 부여되어야 합니다. 필요할 때마다 키를 비활성화하거나 액세스를 취소할 수 있습니다. 그러나 볼트의 항목을 보호하기 전에 키를 사용하여 암호화를 사용하도록 설정해야 합니다.

>[!NOTE]
>이 기능은 현재 사용 가능 여부가 제한되어 있습니다. 고객 관리 키를 사용하여 AskAzureBackupTeam@microsoft.com 백업 데이터를 암호화하려면 이 설문 [조사를](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) 작성하고 이메일을 보내주십시오. 이 기능을 사용하는 기능은 Azure Backup 서비스의 승인을 받아야 합니다.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>고객 관리 키를 사용하여 암호화된 관리 디스크 VM 백업

또한 Azure Backup을 사용하면 서버 측 암호화에 키를 사용하는 Azure VM을 백업할 수 있습니다. 디스크 를 암호화하는 데 사용되는 키는 Azure 키 자격 증명 모음에 저장되고 사용자로부터 관리됩니다. 고객 관리 키를 사용하는 서버 측 암호화는 Azure Disk 암호화와 다르며, ADE는 BitLocker(Windows용) 및 DM-Crypt(Linux용)를 활용하여 게스트 내 암호화를 수행하기 때문에 SSE는 스토리지 서비스의 데이터를 암호화하여 OS 또는 또는 VM용 이미지입니다. 자세한 내용은 [고객 관리 키가 있는 관리 디스크 암호화를](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) 참조하십시오.

### <a name="backup-of-vms-encrypted-using-ade"></a>ADE를 사용하여 암호화된 VM 백업

Azure Backup을 사용하면 Azure 디스크 암호화를 사용하여 해당 OS 또는 데이터 디스크가 암호화된 Azure 가상 컴퓨터를 백업할 수도 있습니다. ADE는 Windows VM용 BitLocker와 Linux VM용 DM-Crypt를 사용하여 게스트 내 암호화를 수행합니다. 자세한 내용은 [Azure Backup을 통해 암호화된 가상 시스템 백업 및 복원을](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)참조하십시오.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>기타 보안 기능

### <a name="protection-of-azure-backup-recovery-points"></a>Azure 백업 복구 지점 보호

복구 서비스 자격 증명 모음에서 사용하는 저장소 계정은 격리되어 있으며 악의적인 목적으로 사용자가 액세스할 수 없습니다. 액세스는 복원과 같은 Azure 백업 관리 작업을 통해서만 허용됩니다. 이러한 관리 작업은 RBAC(역할 기반 액세스 제어)를 통해 제어됩니다.

자세한 내용은 [역할 기반 액세스 제어 를 사용하여 Azure 백업 복구 지점을 관리합니다.](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="for-soft-delete"></a>소프트 삭제의 경우

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>모든 볼트에서 소프트 삭제 기능을 사용하도록 설정해야 합니까?

아니요, 모든 복구 서비스 자격 증명 모음에 대해 기본적으로 빌드되고 활성화되어 있습니다.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>삭제 작업이 완료된 후 내 데이터가 일시 삭제 된 상태로 유지될 일 수를 구성 할 수 있습니까?

아니요, 삭제 작업 후 14일의 추가 보존으로 고정됩니다.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>이 추가 14일 보존에 대한 비용을 지불해야 하나요?

아니요, 이 14일 추가 보존은 소프트 삭제 기능의 일부로 무료로 제공됩니다.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>데이터가 일시 삭제 상태일 때 복원 작업을 수행할 수 있습니까?

아니요, 복원하려면 삭제된 소프트 리소스의 삭제를 취소해야 합니다. 삭제 취소 작업은 리소스를 중지 **보호로** 다시 가져와 데이터 상태를 유지하여 언제든지 복원할 수 있습니다. 가비지 수집기는 이 상태에서 일시 중지된 상태로 유지됩니다.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>스냅샷이 볼트의 복구 지점과 동일한 수명 주기를 따르나요?

예.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>일시 삭제된 리소스에 대해 예약된 백업을 다시 트리거하면 어떻게 해야 합니까?

삭제 취소 다음 다시 시작 작업이 리소스를 다시 보호합니다. 다시 시작 작업은 백업 정책을 연결하여 예약된 백업을 선택한 보존 기간과 트리거합니다. 또한 가비지 수집기는 다시 시작 작업이 완료되는 즉시 실행됩니다. 만료 날짜가 지난 복구 지점에서 복원을 수행하려면 다시 시작 작업을 시작하기 전에 복원하는 것이 좋습니다.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>볼트에 일시 삭제된 항목이 있는 경우 볼트를 삭제할 수 있습니까?

볼트에 일시 삭제된 상태에 백업 항목이 있는 경우 복구 서비스 자격 증명 모음을 삭제할 수 없습니다. 삭제된 항목은 삭제 작업 후 14일 후에 영구적으로 삭제됩니다. 14일 동안 기다릴 수 없는 경우 [소프트 삭제를 사용하지 않도록 설정하고](#disabling-soft-delete)삭제된 항목을 삭제 취소한 다음 다시 삭제하여 영구적으로 삭제합니다. 보호된 항목이 없고 삭제된 항목이 없는지 확인한 후 볼트를 삭제할 수 있습니다.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>삭제 후 14일 의 일시 삭제 기간보다 일찍 데이터를 삭제할 수 있습니까?

아니요. 삭제된 항목은 강제로 삭제할 수 없으며 14일 후에 자동으로 삭제됩니다. 이 보안 기능은 실수로 또는 악의적인 삭제로부터 백업된 데이터를 보호하는 데 사용할 수 있습니다.  VM에서 다른 작업을 수행하기 전에 14일 동안 기다려야 합니다.  소프트 삭제 된 항목은 요금이 부과됩니다.  14일 이내에 일시 삭제로 표시된 VM을 새 볼트로 다시 보호해야 하는 경우 Microsoft 지원에 문의하십시오.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell 또는 CLI에서 소프트 삭제 작업을 수행할 수 있습니까?

소프트 삭제 작업은 [PowerShell](#soft-delete-for-vms-using-azure-powershell)을 사용하여 수행할 수 있습니다. 현재 CLI는 지원되지 않습니다.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Azure VM의 SQL Server 및 Azure VM의 SAP HANA와 같은 다른 클라우드 워크로드에 대해 소프트 삭제가 지원되고 있습니까?

아니요. 현재 소프트 삭제는 Azure 가상 시스템에 대해서만 지원됩니다.

## <a name="next-steps"></a>다음 단계

- Azure [백업에 대한 보안 제어에](backup-security-controls.md)대해 읽어보십시오.
