---
title: Azure PowerShell을 통해 Azure Managed Disks 복원
description: Azure PowerShell을 사용하여 Azure Managed Disks를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520040"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Managed Disks 복원

이 문서에서는 Azure Backup이 만든 복원 지점에서 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)를 복원하는 방법을 설명합니다.

현재, 백업을 수행한 기존 원본 디스크를 교체하여 복원하는 OLR(원래 위치 복구) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 백업을 수행한 원본 디스크와 동일한 리소스 그룹이나 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR(대체 위치 복구)이라고 하며 이는 원본 디스크와 복원된 새 디스크를 모두 유지하는 데 도움이 됩니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만드는 복원

- 복원 작업 상태 추적

예제에서는 ‘testBkpVaultRG’ 리소스 그룹의 기존 백업 자격 증명 모음 ‘TestBkpVault’를 참조합니다.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>새 디스크를 만드는 복원

### <a name="setting-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 디스크를 복원할 리소스 그룹에 대한 권한 집합이 필요합니다.

백업 자격 증명 모음에는 시스템 할당 관리 ID가 사용됩니다. 이 ID는 리소스당 하나로 제한되며 이 리소스의 수명 주기에 연결됩니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 관리 ID에 대한 권한을 부여할 수 있습니다. 관리 ID는 Azure 리소스에서만 사용할 수 있는 특수 유형의 서비스 주체입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

[여기](restore-managed-disks.md#restore-to-create-a-new-disk)에 설명된 대로 디스크가 복원/생성되는 대상 리소스 그룹에서 자격 증명 모음의 시스템 할당 관리 ID와 관련된 권한을 할당합니다.

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 인스턴스를 가져오고 관련 인스턴스를 식별합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az.Resourcegraph** 및 [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 여러 자격 증명 모음 및 구독의 여러 인스턴스에서 검색할 수도 있습니다.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

인스턴스가 식별되면 관련 복구 지점을 가져옵니다.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

[위에서](#setting-up-permissions) 자세히 설명한 것처럼 권한이 할당된 대상 리소스 그룹과 필요한 디스크 이름을 사용하여 만들 새 디스크의 ARM ID를 구성합니다. 예를 들어 구독이 다른 **targetrg** 리소스 그룹에서 디스크의 이름을 **PSTestDisk2** 로 지정할 수 있습니다.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>복원 트리거

[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 위에서 준비한 요청으로 복원을 트리거합니다.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

**Az.ResourceGraph** 를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 백업 자격 증명 모음에서 관련 작업을 가져옵니다.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup FAQ](disk-backup-faq.yml)
