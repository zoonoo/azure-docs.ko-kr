---
title: Azure VM의 SQL Server 및 Azure VM 워크로드의 SAP HANA 일시 삭제
description: Azure VM의 SQL Server 및 Azure VM 워크로드의 SAP HANA 일시 삭제를 통해 백업을 더욱 안전하게 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254298"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM의 SQL Server 및 Azure VM 워크로드의 SAP HANA 일시 삭제

이제 Azure Backup에서 Azure VM의 SQLServer 및 Azure VM 워크로드의 SAP HANA 일시 삭제 기능을 제공합니다. 이 기능은 이미 지원되는 [Azure 가상 머신 일시 삭제 시나리오](soft-delete-virtual-machines.md)에 추가로 제공됩니다.

[일시 삭제](backup-azure-security-feature-cloud.md)는 삭제 후에도 백업 데이터를 보호하는 보안 기능입니다. 일시 삭제를 사용하면 악의적인 작업자가 데이터베이스 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 14일 동안 추가로 보존됩니다. 이렇게 하면 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. “일시 삭제” 상태의 백업 데이터를 14일 동안 추가로 보존하는 고객에게 별도로 청구되는 요금은 없습니다.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Azure Portal을 사용한 Azure VM의 SQL Server 일시 삭제

>[!NOTE]
>위 지침은 Azure VM의 SAP HANA에도 적용됩니다.

1. SQL Server에서 데이터베이스의 백업 데이터를 삭제하려면 백업을 중지해야 합니다. Azure Portal에서 Recovery Services 자격 증명 모음, 백업 항목으로 차례로 이동한 다음 **백업 중지** 를 선택합니다.

   ![백업 중지](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 다음 창에는 백업 데이터를 삭제하거나 보존할 수 있는 옵션이 제공됩니다. **백업 데이터 삭제** 를 선택하는 경우 데이터베이스 백업이 영구적으로 삭제되지 않습니다. 대신, 백업 데이터는 일시 삭제된 상태로 14일 동안 보존됩니다. 15일까지 삭제가 지연되고 데이터베이스의 백업 상태를 사용자에게 알리는 정기 경고 메일이 1일, 12일, 15일에 전송됩니다.

   ![백업 데이터 삭제](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. 14일 동안 Recovery Services 자격 증명 모음에 일시 삭제된 항목 옆에는 빨간색 “일시 삭제” 아이콘이 표시됩니다.

   ![일시 삭제된 항목](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 일시 삭제된 DB를 복원하려면 먼저 삭제를 취소해야 합니다. 삭제를 취소하려면 일시 삭제된 DB를 선택하고 **삭제 취소** 옵션을 선택합니다.

   ![데이터베이스 삭제 취소](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   삭제 취소를 선택하면 데이터베이스의 모든 복원 시점이 삭제 취소되고 복원 작업에 사용할 수 있게 된다고 경고하는 창이 나타납니다. 백업 항목은 백업이 일시 중지되고 적용되는 백업 정책 없이 백업 데이터가 영구적으로 보존되는 “데이터 보존을 사용하는 보호 중지” 상태로 보존됩니다.

   ![삭제 취소 경고](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. 이때 선택한 일시 삭제된 백업 항목에 대해 **복원** 을 선택하여 데이터를 복원할 수도 있습니다.

   ![VM 복원](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 삭제 취소 프로세스가 완료되면 “데이터 보존을 사용하는 백업 중지” 상태로 돌아가며, **백업 다시 시작** 을 선택할 수 있습니다. **백업 다시 시작** 작업은 백업 및 보유 일정을 정의하는 사용자가 선택한 백업 정책과 연결된 활성 상태의 백업 항목을 다시 가져옵니다.

   ![백업 다시 시작](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Azure PowerShell을 사용한 VM의 SQL Server 일시 삭제

>[!NOTE]
>Azure PowerShell을 통해 일시 삭제를 사용하는 데 필요한 Az.RecoveryServices 버전은 2.2.0 이상입니다. 최신 버전을 다운로드하려면 `Install-Module -Name Az.RecoveryServices -Force`를 사용합니다.

Azure PowerShell을 사용하는 단계 순서는 위에서 설명한 Azure Portal와 동일합니다.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell을 사용하여 백업 항목 삭제

[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용하여 백업 항목을 삭제합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

백업 항목의 **DeleteState** 가 **NotDeleted** 에서 **ToBeDeleted** 로 변경됩니다. 백업 데이터는 14일 동안 보존됩니다. 삭제 작업을 되돌리려는 경우 삭제 실행 취소를 수행해야 합니다.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell을 사용하여 삭제 작업 실행 취소

먼저, 일시 삭제 상태(삭제 예정)인 관련 백업 항목을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

그런 다음 [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet을 사용하여 삭제 작업을 실행 취소합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

백업 항목의 **DeleteState** 가  **NotDeleted** 로 돌아갑니다. 하지만 보호는 여전히 중지된 상태입니다. 보호를 다시 사용하도록 설정하려면 백업을 다시 시작합니다.

## <a name="how-to-disable-soft-delete"></a>일시 삭제를 사용하지 않도록 설정하는 방법

이 기능은 사용하도록 설정하는 것이 좋습니다. 일시 삭제를 사용하지 않도록 설정해야 하는 유일한 상황은 보호된 항목을 새 자격 증명 모음으로 이동할 예정이며 삭제하고 다시 보호하기 전에 필요한 14일을 기다릴 수 없는 경우(예: 테스트 환경)입니다. 일시 삭제를 사용하지 않도록 설정하는 방법에 대한 지침은 [일시 삭제 사용 및 사용 안 함](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 일시 삭제에 대한 [질문과 대답](backup-azure-security-feature-cloud.md#frequently-asked-questions) 확인
- [Azure Backup의 모든 보안 기능](security-overview.md) 확인
