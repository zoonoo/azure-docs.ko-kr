---
title: PowerShell을 사용하여 Azure 파일 공유의 배포 및 백업 관리
description: PowerShell을 사용하여 Azure에서 Azure 파일 공유의 백업 배포 및 관리
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell, Azure 파일 백업, Azure 파일 복원,
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 90623981f67bbed15ade743192525676e58a0a83
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318429"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>PowerShell을 사용하여 Azure 파일 공유 백업 및 복원

이 문서는 Azure PowerShell cmdlet을 사용하여 Recovery Services 자격 증명 모음으로부터 Azure 파일 공유를 백업하고 복구하는 방법을 보여 줍니다. Recovery Services 자격 증명 모음은 Azure Backup 및 Azure Site Recovery 서비스에서 데이터와 자산을 보호하는 데 사용되는 Azure Resource Manager 리소스입니다.

## <a name="concepts"></a>개념

Azure Backup Service를 잘 모르는 경우, [Azure Backup이란?](backup-introduction-to-azure-backup.md) 문서에서 서비스에 대한 개요를 확인하세요. 시작하기 전에 [여기](backup-azure-files.md)에서 설명한 Azure 파일 공유를 백업하는 미리 보기 기능을 살펴보아야 합니다.

PowerShell을 효과적으로 사용하기 위해 개체의 계층 구조와 시작하는 위치를 이해하는 데 필요합니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRm.RecoveryServices.Backup PowerShell cmdlet 참조를 보려면 Azure 라이브러리의 [Azure Backup - Recovery Services cmdlet](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)을 참조하세요.

## <a name="setup-and-registration"></a>설정 및 등록

> [!NOTE]
> [여기](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0)에 언급된 대로 AzureRM 모듈의 새로운 기능 지원은 2018년 11월에 종료됩니다. 따라서 새 ‘Az’ PS 모듈을 통해 Azure 파일 공유 백업을 지원하고 있습니다. 또한 Az 모듈의 GA 릴리스에 등록되도록 계획하고 있습니다.

시작하려면

1. [‘Az’ PowerShell 최신 버전 다운로드](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0)(필요한 최소 버전: 0.7.0)

2. 다음 명령을 입력하여 사용할 수 있는 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음의 별칭과 cmdlet이 나타납니다. 다음 이미지는 표시되는 예이며 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다. 이 cmdlet은 계정 자격 증명을 묻는 웹 페이지를 엽니다.

    * 또는 **Connect-AzAccount** cmdlet에서 **-Credential** 매개 변수를 사용하여 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예:  **Connect-AzAccount -Tenant "fabrikam.com"**

4. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록해야 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 다음 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인할 수 있습니다.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    명령 출력에서 **RegistrationState**는 **등록됨**으로 변경해야 합니다. 그렇지 않으면 **Register-AzResourceProvider** cmdlet을 다시 실행합니다.

다음 작업은 PowerShell로 자동화할 수 있습니다.

* Recovery Services 자격 증명 모음 만들기
* Azure 파일 공유 백업 구성
* 백업 작업 트리거
* 백업 작업 모니터링
* Azure 파일 공유 복원
* Azure 파일 공유에서 개별 Azure 파일 복원

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다.

1. Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. **New-AzRecoveryServicesVault** cmdlet을 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md) 또는 [GRS(지역 중복 저장소)](../storage/common/storage-redundancy-grs.md) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testvault에 대한 -BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 **Get-AzRecoveryServicesVault**를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음 예제와 유사하며, 연관된 ResourceGroupName 및 위치가 제공됩니다.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다.

**Set-AzRecoveryServicesVaultContext**를 사용하여 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다. 다음 예제에서는 자격 증명 모음, *testvault*에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 더 이상 사용하지 않을 계획입니다. 대신, 아래에 언급된 대로 사용자가 자격 증명 모음 ID를 전달하는 것이 좋습니다.

또는 PowerShell 작업을 수행할 자격 증명 모음의 ID를 저장하고 가져오며 이를 관련 명령에 전달할 수 있습니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Azure 파일 공유 백업 구성

### <a name="create-protection-policy"></a>보호 정책 만들기

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 전에 얼마나 오래 유지할지를 정의합니다. 기본 보존 정책을 보려면 **Get-AzRecoveryServicesBackupRetentionPolicyObject**를 사용합니다.  마찬가지로 **Get-AzRecoveryServicesBackupSchedulePolicyObject**를 사용하여 기본 일정 정책을 볼 수 있습니다. **New-AzRecoveryServicesBackupProtectionPolicy** cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다. 일정 및 보존 정책 개체는 **New-AzRecoveryServicesBackupProtectionPolicy** cmdlet에 대해 입력으로 사용됩니다. 다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 예제에서는 이러한 변수를 사용하여 보호 정책, *NewPolicy*를 만들 때 매개 변수를 정의합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

다음 예제와 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

### <a name="enable-protection"></a>보호 사용

보호 정책을 정의하면 이 정책으로 Azure 파일 공유를 보호할 수 있습니다.

먼저 **Get-AzRecoveryServicesBackupProtectionPolicy** cmdlet을 사용하여 관련 정책 개체를 가져옵니다. 특정 정책을 받거나 워크로드 유형과 연결된 정책을 보려면 이 cmdlet을 사용할 수 있습니다.

다음 예제에서는 워크로드 유형, AzureFiles에 대한 정책을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

다음 예제와 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

다음 정책은 “dailyafs”라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

**Enable-AzRecoveryServicesBackupProtection**을 사용하여 지정된 정책으로 항목을 보호할 수 있습니다. 정책이 자격 증명 모음과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

다음 예제에서는 dailyafs 정책을 사용하여 스토리지 계정 “testStorageAcct”로 Azure 파일 공유, “testAzureFileShare”를 보호할 수 있습니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

명령은 보호 구성 작업이 완료될 때까지 대기하며 다음과 같은 출력을 제공합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

**Backup-AzRecoveryServicesBackupItem**을 사용하여 보호된 Azure 파일 공유에 대한 백업 작업을 트리거합니다. 다음 명령을 사용하여 스토리지 계정 및 파일 공유를 검색하고 주문형 백업을 트리거합니다.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

이 명령은 다음 예제와 같이 ID로 추적되는 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

백업을 수행하는 동안 Azure 파일 공유 스냅숏을 활용하므로 일반적으로 명령이 이 출력을 반환할 때까지 작업이 완료됩니다.

### <a name="modify-protection-policy"></a>보호 정책 수정

Azure 파일 공유를 보호하는 정책을 변경하려면 관련 백업 항목 및 새 보호 정책과 함께 **Enable-AzRecoveryServicesBackupProtection**을 사용합니다.

다음 예제는 “testAzureFS”의 보호 정책을 “dailyafs”에서 “monthlyafs”로 변경합니다.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Azure 파일 공유/Azure 파일 복원

전체 파일 공유를 원래 위치나 대체 위치로 복원할 수 있습니다. 마찬가지로 파일 공유의 개별 파일도 복원할 수 있습니다.

### <a name="fetching-recovery-points"></a>복구 지점 가져오기

**Get-AzRecoveryServicesBackupRecoveryPoint** cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 다음 스크립트에서 변수 **$rp**는 과거 7일부터 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

다음 예제와 유사하게 출력됩니다.

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

관련 복구 지점이 선택되면 아래 설명된 대로 계속해서 파일 공유/파일을 대체/원래 위치에 복원합니다.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Azure 파일 공유를 대체 위치에 복원

#### <a name="restoring-an-azure-file-share"></a>Azure 파일 공유 복원

다음 정보를 제공하여 대체 위치를 식별합니다.

* ***TargetStorageAccountName***: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* ***TargetFileShareName***: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
* ***TargetFolder***: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* ***ResolveConflict***: 복원된 데이터와 충돌하는 경우에 적용되는 지침입니다. “덮어쓰기” 또는 “건너뛰기” 허용

백업된 파일 공유를 대체 위치에 복원하려면 이 매개 변수를 복원 명령에 제공합니다.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

이 명령은 다음 예제에 표시된 대로 ID가 추적되는 작업을 반환합니다.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Azure 파일 복원

전체 파일 공유 대신 개별 파일을 복원하려는 경우 다음 매개 변수를 제공하여 개별 파일을 고유하게 식별해야 합니다.

* ***TargetStorageAccountName***: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* ***TargetFileShareName***: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
* ***TargetFolder***: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* ***SourceFilePath***: 파일 공유 내에서 복원되는 파일의 절대 경로(문자열)입니다. ```Get-AzStorageFile``` PS cmdlet에서 사용되는 것과 동일한 경로입니다.
* ***SourceFileType***: 디렉터리 또는 파일이 선택되었는지 여부입니다. “디렉터리” 또는 “파일” 허용
* ***ResolveConflict***: 복원된 데이터와 충돌하는 경우에 적용되는 지침입니다. “덮어쓰기” 또는 “건너뛰기” 허용

살펴본 대로 추가 매개 변수는 복원할 개별 파일에만 관련됩니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

또한 위에 표시된 대로 ID로 추적되는 작업을 반환합니다.

### <a name="restore-azure-file-shares-to-original-location"></a>Azure 파일 공유를 원래 위치에 복원

원래 위치 복원의 경우 모든 대상/목표 관련 매개 변수를 지정하지 않아도 됩니다. ```ResolveConflict```만 제공해야 함

#### <a name="overwrite-an-azure-file-share"></a>Azure 파일 공유 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure 파일 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>백업 및 복원 작업 추적

주문형 백업 및 복원 작업은 [위](#trigger-an-on-demand-backup)에 표시된 ID와 함께 작업을 반환합니다. ```Get-AzRecoveryServicesBackupJobDetails``` commandlet을 사용하여 작업의 진행 상태를 추적하고 자세한 정보를 가져옵니다.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
