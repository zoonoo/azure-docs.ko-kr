---
title: PowerShell을 통해 Azure 파일 복원
description: 이 문서에서는 Azure 백업 서비스 및 PowerShell을 사용하여 Azure 파일을 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086833"
---
# <a name="restore-azure-files-with-powershell"></a>PowerShell을 통해 Azure 파일 복원

이 문서에서는 Azure Powershell을 사용하여 [Azure Backup](backup-overview.md) 서비스에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원하는 방법을 설명합니다.

전체 파일 공유 또는 공유의 특정 파일을 복원할 수 있습니다. 원래 위치 또는 대체 위치로 복원할 수 있습니다.

> [!WARNING]
> PS 버전이 AFS 백업의 경우 'Az.RecoveryServices 2.6.0'의 최소 버전으로 업그레이드되었는지 확인합니다. 자세한 내용은 이 [변경에](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) 대한 요구 사항을 설명하는 섹션을 참조하십시오.

## <a name="fetch-recovery-points"></a>복구 지점 가져오기

[Get-AzRecoveryServices백업복구포인트를](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) 사용하여 백업된 항목의 모든 복구 지점을 나열합니다.

다음 스크립트에서:

* 변수 **$rp** 지난 7일 동안 선택한 백업 항목에 대한 복구 지점의 배열입니다.
* 배열은 인덱스 **0**의 가장 최근 복구 지점부터 시간이 역순으로 정렬됩니다.
* 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다.
* 예에서, **$rp[0]** 은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

출력은 다음과 유사합니다.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

관련 복구 지점을 선택한 후 파일 공유 또는 파일을 원래 위치 또는 대체 위치로 복원합니다.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure 파일 공유를 대체 위치로 복원

[복원-AzRecoveryServices백업항목을](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 사용하여 선택한 복구 지점으로 복원합니다. 다음 매개 변수를 지정하여 대체 위치를 식별합니다.

* **TargetStorageAccountName**: 백업된 콘텐츠가 복원되는 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName:** 백업된 콘텐츠가 복원되는 대상 저장소 계정 내에서 파일이 공유됩니다.
* **TargetFolder**: 데이터가 복원되는 파일 공유 아래의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **해결충돌**: 복원된 데이터와 충돌이 있는 경우 명령을 수행합니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

다음과 같이 매개 변수를 사용 하 고 cmdlet을 실행 합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure 파일을 대체 위치로 복원

[복원-AzRecoveryServices백업항목을](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 사용하여 선택한 복구 지점으로 복원합니다. 이러한 매개 변수를 지정하여 대체 위치를 식별하고 복원할 파일을 고유하게 식별합니다.

* **TargetStorageAccountName**: 백업된 콘텐츠가 복원되는 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName:** 백업된 콘텐츠가 복원되는 대상 저장소 계정 내에서 파일이 공유됩니다.
* **TargetFolder**: 데이터가 복원되는 파일 공유 아래의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **SourceFilePath**: 파일 공유 내에서 문자열로 복원할 파일의 절대 경로입니다. 이 경로는 **Get-AzStorageFile** PowerShell cmdlet에 사용되는 경로와 동일합니다.
* **SourceFileType**: 디렉터리 또는 파일이 선택되었는지 여부입니다. **디렉터리** 또는 **파일**을 수락합니다.
* **해결충돌**: 복원된 데이터와 충돌이 있는 경우 명령을 수행합니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

추가 매개 변수(SourceFilePath 및 SourceFileType)는 복원하려는 개별 파일과만 관련이 있습니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

이 명령은 이전 섹션과 같이 추적할 ID가 있는 작업을 반환합니다.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure 파일 공유 및 파일을 원래 위치로 복원

원래 위치로 복원할 때 대상 및 대상 관련 매개변수를 지정할 필요가 없습니다. **ResolveConflict**만 제공해야 합니다.

#### <a name="overwrite-an-azure-file-share"></a>Azure 파일 공유 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure 파일 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>다음 단계

Azure 포털에서 Azure 파일 복원에 [대해 자세히 알아봅니다.](restore-afs.md)
