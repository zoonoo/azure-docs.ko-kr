---
title: 백업 및 Azure Backup 및 PowerShell을 사용 하 여 Azure Files를 복원 합니다.
description: 백업 하 고 Azure Backup 및 PowerShell을 사용 하 여 Azure Files를 복원 합니다.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 986414d0bac24d0c7e37b34df473346742fa97fd
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204192"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>백업 및 PowerShell 사용 하 여 Azure 파일 복원

이 문서에서는 백업 및 복구를 Azure Files 파일 공유를 사용 하 여 Azure PowerShell을 사용 하는 방법에 설명 합니다는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음입니다. 

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * PowerShell을 설정 하 고 Azure Recovery Services 공급자를 등록 합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure 파일 공유에 대 한 백업을 구성 합니다.
> * 백업 작업을 실행 합니다.
> * Azure 파일 공유 또는 개별 파일 공유에서 백업 된를 복원 합니다.
> * 백업을 모니터링 하 고 작업을 복원 합니다.


## <a name="before-you-start"></a>시작하기 전에

- [자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음에 대 한 합니다.
- 에 대 한 미리 보기 기능을 읽어보세요 [Azure 파일 공유 백업](backup-azure-files.md)합니다.
- Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 됩니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

검토 합니다 **Az.RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) Azure 라이브러리에서 참조 합니다.


## <a name="set-up-and-install"></a>설정 및 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음과 같이 PowerShell을 설정 합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.0.0입니다.

2. 이 명령 사용 하 여 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. 별칭을 검토 하 고 Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음 cmdlet을 표시 합니다. 표시 될 수 있습니다의 예는 다음과 같습니다. Cmdlet의 전체 목록은 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. 사용 하 여 Azure 계정에 로그인 **Connect AzAccount**합니다.
4. 표시 되는 웹 페이지에서 계정 자격 증명을 입력 하는 것인지 묻는 메시지가 나타납니다.

    - 또는 매개 변수로 계정 자격 증명을 포함할 수 있습니다는 **Connect AzAccount** cmdlet을 사용 해야 **-자격 증명**합니다.
    - CSP 파트너 테 넌 트를 대신 하 여 작업 인 경우 테 넌 트를 해당 tenantID 또는 테 넌 트 기본 도메인 이름을 사용 하 여 고객을 지정 합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

4. 계정에 여러 구독이 있을 수 있으므로 계정을 사용 하려는 구독을 연결 합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 공급자가 성공적으로 등록 되었는지 확인 합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. 명령 출력에서 확인 **RegistrationState** 변경 내용 **Registered**합니다. 실행 하지 않는 경우에 **레지스터 AzResourceProvider** cmdlet을 다시 합니다.



## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

- Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다. 

1. 자격 증명 모음 리소스 그룹에 배치 됩니다. 기존 리소스 그룹에서 사용 하 여 새로 만들 필요가 합니다 [새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)합니다. 이 예제에서는 미국 서 부 지역에 새 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. 사용 합니다 [새로 만들기-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) 자격 증명 모음을 만드는 cmdlet입니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 자격 증명 모음 저장소에 사용할 중복 유형을 지정 합니다.

   - [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
   - 다음 예제에서는 합니다 **-BackupStorageRedundancy** 에 대 한 옵션을[집합 AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd에 대 한 **testvault** 로  **GeoRedundant**합니다.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 비슷합니다. 제공 되는 연결 된 리소스 그룹 및 위치 note 합니다.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

자격 증명 모음 개체를 변수에 저장 하 고 자격 증명 모음 컨텍스트를 설정 합니다.

- 많은 Azure Backup cmdlet는 자격 증명 모음 개체를 변수에 저장 하는 것이 유용 하므로 Recovery Services 자격 증명 모음 개체를 입력으로 필요 합니다.
- 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. 사용 하 여 설정 [집합 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)합니다. 컨텍스트를 설정한 후 모든 후속 cmdlet에 적용 됩니다.


다음 예제에서는 **testvault**에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 인출 합니다.

Azure PowerShell 지침에 따라 설정 된 자격 증명 모음 컨텍스트를 사용 중단에 예정입니다. 대신 저장 하 고 또는 자격 증명 모음 ID를 가져올 하 고, 다음과 같이 관련 명령에 전달할 수 있습니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책을 백업에 대 한 일정을 지정 하 고 백업 복구 지점을 얼마나 오래 유지 하는 키를 누릅니다.

- 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
- 뷰를 사용 하 여 기본 백업 정책이 보존 [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)합니다.
- 뷰를 사용 하 여 기본 백업 정책이 일정 [Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)합니다.
-  사용할 합니다 [새로 만들기-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) 새 백업 정책을 만들려면 cmdlet. 일정 및 보존 정책 개체를 입력 합니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수 매개 변수로 새 정책의 (**NewAFSPolicy**). **NewAFSPolicy**는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 비슷합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>백업을 사용 하도록 설정

백업 정책을 정의한 후에 정책을 사용 하 여 Azure 파일 공유에 대 한 보호를 사용할 수 있습니다.

### <a name="retrieve-a-backup-policy"></a>백업 정책을 검색 합니다.

사용 하 여 관련 정책 개체를 가져올 [Get AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)합니다. 특정 정책을 받거나 워크 로드 유형과 연결 된 정책을 보려면이 cmdlet을 사용 합니다.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>워크 로드 형식에 대 한 정책 검색

다음 예제에서는 워크 로드 형식에 대 한 정책 검색 **AzureFiles**합니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

출력은 다음과 비슷합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> PowerShell에서 **BackupTime** 필드의 표준 시간대는UTC(협정 세계시)입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

### <a name="retrieve-a-specific-policy"></a>특정 정책을 검색 합니다.

다음 정책은 **dailyafs**라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>백업을 사용 하도록 설정 하 고 정책 적용

사용 하 여 보호를 사용 하도록 설정 [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)합니다. 정책 자격 증명 모음을 사용 하 여 연결 되 면 백업 정책 일정에 따라 트리거됩니다.

다음 예제에서는 Azure 파일 공유에 대 한 보호 **testAzureFileShare** storage 계정에 **testStorageAcct**, 정책과 **dailyafs**합니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

명령은 보호 구성 작업을 마칠 때까지 대기하며 다음과 같은 출력을 제공합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

사용 하 여 [백업 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) 보호 된 Azure 파일 공유에 대 한 주문형 백업을 실행할 합니다.

1. 검색을 사용 하 여 백업 데이터를 보유 하는 자격 증명 모음에서 컨테이너에서 저장소 계정 및 파일 공유 [Get AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)합니다.
2. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)을 사용하여 VM에 대한 정보를 가져옵니다.
3. 사용 하 여 주문형 백업을 실행할[백업 AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)합니다.

다음과 같이 주문형 백업을 실행 합니다.
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

백업을 수행하는 동안 Azure 파일 공유 스냅샷이 사용되기 때문에 일반적으로 명령에 이 출력이 반환될 때까지 작업이 완료됩니다.

### <a name="modify-the-protection-policy"></a>보호 정책 수정

Azure 파일 공유 백업에 대 한 사용 정책을 변경 하려면 [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)합니다. 관련 백업 항목 및 새 백업 정책을 지정 합니다.

다음 예제는 **testAzureFS** 보호 정책을 **dailyafs**에서 **monthlyafs**로 변경합니다.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Azure 파일 공유 및 파일 복원

전체 파일 공유 또는 특정 파일 공유를 복원할 수 있습니다. 원래 위치 또는 대체 위치에 복원할 수 있습니다. 

### <a name="fetch-recovery-points"></a>복구 지점 가져오기

사용 하 여 [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) 백업된 항목에 대 한 모든 복구 지점을 나열 합니다.

다음 스크립트:

- 변수의 **$rp** 은 지난 7 일에서 선택한 백업 항목에 대 한 복구 지점의 배열입니다.
- 배열은 인덱스 **0**의 가장 최근 복구 지점부터 시간이 역순으로 정렬됩니다.
- 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다.
- 예에서, **$rp[0]** 은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

출력은 다음과 비슷합니다.

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
관련 복구 지점 선택 된 후 복원한 파일 공유 또는 파일을 원래 위치 또는 대체 위치에 있습니다.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure 파일 공유를 대체 위치로 복원

사용 합니다 [복원 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 선택한 복구 지점으로 복원 합니다. 대체 위치를 식별 하려면 이러한 매개 변수를 지정 합니다. 

- **TargetStorageAccountName**: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
- **TargetFileShareName**: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
- **TargetFolder**: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
- **ResolveConflict**: 복원된 데이터와 충돌이 있는 경우 적용되는 지침입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

다음과 같이 cmdlet 매개 변수를 사용 하 여 실행 합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure 파일을 대체 위치로 복원

사용 합니다 [복원 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 선택한 복구 지점으로 복원 합니다. 대체 위치를 확인 하 고 고유 하 게 복원 하려는 파일을 식별 하려면 이러한 매개 변수를 지정 합니다.

* **TargetStorageAccountName**: 백업된 콘텐츠가 복원되는 스토리지 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName**: 백업된 콘텐츠가 복원되는 대상 스토리지 계정 내의 파일 공유입니다.
* **TargetFolder**: 데이터가 복원되는 파일 공유 아래에 있는 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **SourceFilePath**: 파일 공유 내에서 복원되는 파일의 절대 경로(문자열)입니다. 이 경로는 **Get-AzStorageFile** PowerShell cmdlet에 사용되는 경로와 동일합니다.
* **SourceFileType**: 디렉터리 또는 파일이 선택되었는지 여부입니다. **디렉터리** 또는 **파일**을 수락합니다.
* **ResolveConflict**: 복원된 데이터와 충돌이 있는 경우 적용되는 지침입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

(원본 파일 경로 및 SourceFileType) 추가 매개 변수는 복원 하려는 개별 파일에만 관련 됩니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

이 명령은 이전 섹션에 표시 된 대로 추적, ID 사용 하 여 작업을 반환 합니다.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure 파일 공유 및 파일을 원래 위치로 복원

원래 위치로 복원 하면 대상 및 대상에 관련 된 매개 변수를 지정할 필요가 없습니다. **ResolveConflict**만 제공해야 합니다.

#### <a name="overwrite-an-azure-file-share"></a>Azure 파일 공유 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure 파일 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>백업 및 복원 작업 추적

주문형 백업 및 복원 작업 때 표시 되는 ID와 함께 작업을 반환 하면 [-주문형 백업 실행](#trigger-an-on-demand-backup)합니다. 사용 된 [Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) 의 작업 진행 상태 및 세부 정보를 추적 하는 cmdlet.

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
## <a name="next-steps"></a>다음 단계
[에 대 한 자세한](backup-azure-files.md) Azure portal에서 Azure Files 백업.
