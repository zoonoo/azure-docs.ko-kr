---
title: Azure PowerShell를 통해 Azure Managed Disks 복원
description: Azure PowerShell를 사용 하 여 Azure Managed Disks를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630444"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure Managed Disks 복원

이 문서에서는 Azure Backup으로 만든 복원 지점에서 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) 를 복원 하는 방법을 설명 합니다.

현재는 백업이 수행 된 원본 디스크를 대체 하 여 복원의 OLR (Original-Location 복구) 옵션이 지원 되지 않습니다. 복구 지점에서 복원 하 여 백업이 수행 된 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR (Alternate-Location 복구) 라고 하며,이를 통해 원본 디스크와 복원 된 (새) 디스크를 모두 유지할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만들기 위해 복원

- 복원 작업 상태 추적

예의 리소스 그룹 "testBkpVaultRG"에서 기존 백업 자격 증명 모음 "TestBkpVault"를 참조 합니다.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>새 디스크를 만들기 위해 복원

### <a name="setting-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 Id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 백업에서 복원 하려면 백업 자격 증명 모음의 관리 되는 id에 디스크를 복원 하는 리소스 그룹에 대 한 사용 권한 집합이 필요 합니다.

백업 자격 증명 모음에는 시스템 할당 관리 id가 사용 됩니다 .이 id는 리소스 당 하나로 제한 되며이 리소스의 수명 주기에 연결 됩니다. Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 관리 되는 id에 대 한 권한을 부여할 수 있습니다. 관리 id는 Azure 리소스에만 사용할 수 있는 특수 형식의 서비스 사용자입니다. [관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

[여기](restore-managed-disks.md#restore-to-create-a-new-disk)에 설명 된 대로 디스크가 복원/생성 되는 대상 리소스 그룹에서 자격 증명 모음의 시스템 할당 관리 id에 대 한 관련 사용 권한을 할당 합니다.

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점을 가져오는 중

[AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 인스턴스를 가져오고 관련 인스턴스를 식별 합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az. Resourcegraph** 및 [AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 여러 자격 증명 모음 및 구독에서 여러 인스턴스를 검색할 수도 있습니다.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

인스턴스가 식별 되 면 관련 복구 지점을 페치합니다.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

대상 리소스 그룹을 사용 하 여 만들 새 디스크의 ARM ID를 구성 하 고, [위에서](#setting-up-permissions)자세히 설명 된 사용 권한을 할당 하 고, 필요한 디스크 이름을 구성 합니다. 예를 들어 다른 구독을 사용 하 여 리소스 그룹 **targetrg** 에서 디스크의 이름을 **PSTestDisk2** 으로 지정할 수 있습니다.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

[AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 관련 된 모든 세부 정보와 함께 복원 요청을 준비 합니다.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>복원 트리거

[AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 위에 준비한 요청으로 복원을 트리거합니다.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>작업 추적

[AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 작업을 추적 합니다. 모든 작업을 나열 하 고 특정 작업 세부 정보를 가져올 수 있습니다.

**Az. ResourceGraph** 를 사용 하 여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 백업 자격 증명 모음에서 수행할 수 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup FAQ](disk-backup-faq.md)
