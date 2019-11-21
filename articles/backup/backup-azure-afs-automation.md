---
title: Back up and restore Azure Files with PowerShell
description: In this article, learn how to back up and restore Azure Files using the Azure Backup service and PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 78000bc669eb7a61f8698ad8c39ef49f65b245a2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224172"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Back up and restore Azure Files with PowerShell

This article describes how to use Azure PowerShell to back up and recover an Azure Files file share using an [Azure Backup](backup-overview.md) Recovery Services vault.

이 문서에서는 다음 작업을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
>
> * Set up PowerShell and register the Azure Recovery Services Provider.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Configure backup for an Azure file share.
> * Run a backup job.
> * Restore a backed up Azure file share, or an individual file from a share.
> * Monitor backup and restore jobs.

## <a name="before-you-start"></a>시작하기 전에

* [Learn more](backup-azure-recovery-services-vault-overview.md) about Recovery Services vaults.
* Read about the preview capabilities for [backing up Azure file shares](backup-azure-files.md).
* Review the PowerShell object hierarchy for Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

The object hierarchy is summarized in the following diagram.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Review the **Az.RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

## <a name="set-up-and-install"></a>Set up and install

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Set up PowerShell as follows:

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.0.0입니다.

2. Find the Azure Backup PowerShell cmdlets with this command:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Review the aliases and cmdlets for Azure Backup, Azure Site Recovery, and the Recovery Services vault appear. Here's an example of what you might see. It's not a complete list of cmdlets.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Sign in to your Azure account with **Connect-AzAccount**.
5. On the web page that appears, you're prompted to input your account credentials.

    * Alternately, you can include your account credentials as a parameter in the **Connect-AzAccount** cmdlet with **-Credential**.
    * If you're a CSP partner working on behalf of a tenant, specify the customer as a tenant, using their tenantID or tenant primary domain name. 예: **Connect-AzAccount -Tenant** fabrikam.com.

6. Associate the subscription you want to use with the account, because an account can have several subscriptions.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verify that the providers registered successfully:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. In the command output, verify that **RegistrationState** changes to **Registered**. If it doesn't, run the **Register-AzResourceProvider** cmdlet again.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

1. A vault is placed in a resource group. If you don't have an existing resource group, create a new one with the [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In this example, we create a new resource group in the West US region.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Use the [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet to create the vault. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specify the type of redundancy to use for the vault storage.

   * [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
   * The following example sets the **-BackupStorageRedundancy** option for the[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd for **testvault** set to **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

The output is similar to the following. Note that the associated resource group and location are provided.

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

Store the vault object in a variable, and set the vault context.

* Many Azure Backup cmdlets require the Recovery Services vault object as an input, so it's convenient to store the vault object in a variable.
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. Set it with [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). After the context is set, it applies to all subsequent cmdlets.

다음 예제에서는 **testvault**에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Fetch the vault ID

We plan on deprecating the vault context setting in accordance with Azure PowerShell guidelines. Instead, you can store or fetch the vault ID, and pass it to relevant commands. So, if you haven't set the vault context or want to specify the command to run for a certain vault, pass the vault ID as "-vaultID" to all relevant command as follows:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

A backup policy specifies the schedule for backups, and how long backup recovery points should be kept:

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* View the default backup policy retention using [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* View the default backup policy schedule using [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* You use the [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet to create a new backup policy. You input the schedule and retention policy objects.

By default, a start time is defined in the Schedule Policy Object. Use the following example to change the start time to the desired start time. The desired start time should be in UTC as well. The below example assumes the desired start time is 01:00 AM UTC for daily backups.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> You need to provide the start time in 30 minute multiples only. In the above example, it can be only "01:00:00" or "02:30:00". The start time cannot be "01:15:00"

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. It then uses those variables as parameters for a new policy (**NewAFSPolicy**). **NewAFSPolicy**는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

The output is similar to the following.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Enable backup

After you define the backup policy, you can enable the protection for the Azure file share using the policy.

### <a name="retrieve-a-backup-policy"></a>Retrieve a backup policy

You fetch the relevant policy object with [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use this cmdlet to get a specific policy, or to view the policies associated with a workload type.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Retrieve a policy for a workload type

The following example retrieves policies for the workload type **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

The output is similar to the following.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 **BackupTime** 필드의 표준 시간대는UTC(협정 세계시)입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

### <a name="retrieve-a-specific-policy"></a>Retrieve a specific policy

다음 정책은 **dailyafs**라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Enable backup and apply policy

Enable protection with [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). After the policy is associated with the vault, backups are triggered in accordance with the policy schedule.

The following example enables protection for the Azure file share **testAzureFileShare** in storage account **testStorageAcct**, with the policy **dailyafs**.

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

Use [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) to run an on-demand backup for a protected Azure file share.

1. Retrieve the storage account and file share from the container in the vault that holds your backup data with [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)을 사용하여 VM에 대한 정보를 가져옵니다.
3. Run an on-demand backup with[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Run the on-demand backup as follows:

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

### <a name="using-on-demand-backups-to-extend-retention"></a>Using on-demand backups to extend retention

On-demand backups can be used to retain your snapshots for 10 years. Schedulers can be used to run on-demand PowerShell scripts with chosen retention and thus take snapshots at regular intervals every week, month, or year. While taking regular snapshots, refer to the [limitations of on-demand backups](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) using Azure backup.

If you are looking for sample scripts, you can refer to the sample script on GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) using Azure Automation runbook that enables you to schedule backups on a periodic basis and retain them even up to 10 years.

### <a name="modify-the-protection-policy"></a>보호 정책 수정

To change the policy used for backing up the Azure file share, use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Specify the relevant backup item and the new backup policy.

다음 예제는 **testAzureFS** 보호 정책을 **dailyafs**에서 **monthlyafs**로 변경합니다.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Restore Azure file shares and files

You can restore an entire file share or specific files on the share. You can restore to the original location, or to an alternate location.

### <a name="fetch-recovery-points"></a>복구 지점 가져오기

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) to list all recovery points for the backed-up item.

In the following script:

* The variable **$rp** is an array of recovery points for the selected backup item from the past seven days.
* 배열은 인덱스 **0**의 가장 최근 복구 지점부터 시간이 역순으로 정렬됩니다.
* 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다.
* 예에서, **$rp[0]** 은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

The output is similar to the following.

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

After the relevant recovery point is selected, you restore the file share or file to the original location, or to an alternate location.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restore an Azure file share to an alternate location

Use the [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) to restore to the selected recovery point. Specify these parameters to identify the alternate location:

* **TargetStorageAccountName**: The storage account to which the backed-up content is restored. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName**: The file shares within the target storage account to which the backed-up content is restored.
* **TargetFolder**: The folder under the file share to which data is restored. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **ResolveConflict**: Instruction if there's a conflict with the restored data. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

Run the cmdlet with the parameters as follows:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Restore an Azure file to an alternate location

Use the [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) to restore to the selected recovery point. Specify these parameters to identify the alternate location, and to uniquely identify the file you want to restore.

* **TargetStorageAccountName**: The storage account to which the backed-up content is restored. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName**: The file shares within the target storage account to which the backed-up content is restored.
* **TargetFolder**: The folder under the file share to which data is restored. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **SourceFilePath**: The absolute path of the file, to be restored within the file share, as a string. 이 경로는 **Get-AzStorageFile** PowerShell cmdlet에 사용되는 경로와 동일합니다.
* **SourceFileType**: Whether a directory or a file is selected. **디렉터리** 또는 **파일**을 수락합니다.
* **ResolveConflict**: Instruction if there's a conflict with the restored data. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

The additional parameters (SourceFilePath and SourceFileType) are related only to the individual file you want to restore.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

This command returns a job with an ID to be tracked, as shown in the previous section.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restore Azure file shares and files to the original location

When you restore to an original location, you don't need to specify destination- and target-related parameters. **ResolveConflict**만 제공해야 합니다.

#### <a name="overwrite-an-azure-file-share"></a>Azure 파일 공유 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure 파일 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>백업 및 복원 작업 추적

On-demand backup and restore operations return a job along with an ID, as shown when you [ran an on-demand backup](#trigger-an-on-demand-backup). Use the [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet to track the job progress and details.

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

[Learn about](backup-azure-files.md) backing up Azure Files in the Azure portal.
