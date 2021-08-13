---
title: Azure Powershell을 통해 Azure Blob 복원
description: Azure Powershell을 사용하여 Azure Blob을 모든 시점으로 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 653c3ce6fbfea4d890c3dc3e52fb1032a72b7eab
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803065"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-powershell"></a>Azure Powershell을 사용하여 Azure Blob을 시점으로 복원

이 문서에서는 Azure Backup을 사용하여 [Blob](blob-backup-overview.md)을 모든 시점으로 복원하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Blob에 대한 지원은 Az 5.9.0 버전에서 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Backup을 사용하여 Azure Blob을 복원하기 전에 [중요 사항](blob-restore.md#before-you-start)을 참조하세요.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Blob을 시점으로 복원

- 복원 작업 상태 추적

예제에서는 _testBkpVaultRG_ 리소스 그룹의 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restoring-azure-blobs-within-a-storage-account"></a>스토리지 계정 내에서 Azure Blob 복원

### <a name="fetching-the-valid-time-range-for-restore"></a>복원에 유효한 시간 범위 가져오기

Blob에 대한 작업 백업이 연속적이기 때문에 복원할 고유한 지점이 없습니다. 대신 Blob을 모든 시점으로 복원할 수 있는 유효한 시간 범위를 가져와야 합니다. 이 예제에서는 지난 30일 이내에 복원할 유효한 시간 범위를 확인해 보겠습니다.

```azurepowershell-interactive
$startDate = (Get-Date).AddDays(-30)
$endDate = Get-Date
```

먼저 [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 모든 인스턴스를 가져오고 관련 인스턴스를 식별합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Az.Resourcegraph 및 [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 여러 자격 증명 모음 및 구독의 여러 인스턴스에서 검색할 수도 있습니다.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureBlob -ProtectionStatus ProtectionConfigured
```

인스턴스가 식별되면 [Find-AzDataProtectionRestorableTimeRange](/powershell/module/az.dataprotection/find-azdataprotectionrestorabletimerange) 명령을 사용하여 관련 복구 범위를 가져옵니다.

```azurepowershell-interactive
Find-AzDataProtectionRestorableTimeRange -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName -StartTime $startDate -endTime $endDate

EndTime    : 2021-04-24T08:57:36.4149422Z
ObjectType : RestorableTimeRange
StartTime  : 2021-03-25T14:27:31.0000000Z

$DesiredPIT = (Get-Date -Date "2021-04-23T02:47:02.9500000Z")
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

복원 시점이 수정되면 복원할 여러 옵션이 있습니다. [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>모든 Blob을 시점으로 복원

이 옵션을 사용하면 선택한 시점으로 다시 롤백하여 스토리지 계정의 모든 블록 Blob이 복원됩니다. 많은 양의 데이터를 포함하거나 높은 변동이 발생하는 스토리지 계정은 복원하는 데 시간이 더 오래 걸릴 수 있습니다.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2]
```

#### <a name="restoring-selected-containers"></a>선택한 컨테이너 복원

이 옵션을 사용하면 최대 10개의 컨테이너를 찾고 선택하여 복원할 수 있습니다.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -ContainersList "abc","xyz"
```

#### <a name="restoring-containers-using-a-prefix-match"></a>접두사 일치를 사용하여 컨테이너 복원

이 옵션을 사용하면 접두사 일치를 사용하여 Blob의 하위 집합을 복원할 수 있습니다. 지정된 시점에 해당 Blob을 이전 상태로 되돌리기 위해 단일 컨테이너 내에서 또는 여러 컨테이너에서 사전순으로 최대 10개의 Blob 범위를 지정할 수 있습니다. 몇 가지 주의할 사항은 다음과 같습니다.

- 슬래시(/)를 사용하여 Blob 접두사에서 컨테이너 이름을 구분할 수 있습니다.
- 지정된 범위의 시작은 포함되지만 지정된 범위는 제외됩니다.

접두사를 사용하여 Blob 범위를 복원하는 방법에 대해 [자세히 알아보세요](blob-restore.md#use-prefix-match-for-restoring-blobs).

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -FromPrefixPattern "containerabc/aaa","containerabc/ccc" -ToPrefixPattern "containerabc/bbb","containerabc/ddd"
```

### <a name="trigger-the-restore"></a>복원 트리거

[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 위에서 준비한 요청으로 복원을 트리거합니다.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

Az.ResourceGraph를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.9.0&preserve-view=true) 명령을 사용하여 모든 백업 자격 증명 모음에서 관련 작업을 가져옵니다.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureBlob -Operation Restore
```

## <a name="next-steps"></a>다음 단계

[Azure Blob 백업 개요](blob-backup-overview.md)
