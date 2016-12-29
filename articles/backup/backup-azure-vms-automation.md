---
title: "PowerShell을 사용하여 Resource Manager 배포 VM에 대한 백업 배포 및 관리 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure에서 Resource Manager 배포 VM에 대한 백업 배포 및 관리"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2016
ms.author: markgal; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: f6a1346b84521806e5523e331b2aeb11648bbe6d
ms.openlocfilehash: a7d2a73760cd015a5a67551f6f6ada8568ed75b8


---
# <a name="deploy-and-manage-backups-for-resource-manager-deployed-vms-using-powershell"></a>PowerShell을 사용하여 Resource Manager 배포 VM에 대한 백업 배포 및 관리
> [!div class="op_single_selector"]
> * [리소스 관리자](backup-azure-vms-automation.md)
> * [클래식](backup-azure-vms-classic-automation.md)
> 
> 

이 문서는 Azure PowerShell cmdlet을 사용하여 복구 서비스 자격 증명 모음으로부터 Azure VM(가상 컴퓨터)을 복원하고 백업하는 방법을 보여 줍니다. 복구 서비스 자격 증명 모음은 Azure Resource Manager 리소스이며 Azure 백업 및 Azure Site Recovery 서비스에서 데이터와 자산을 보호하는 데 사용됩니다. 복구 서비스 자격 증명 모음을 사용하여 Azure Resource Manager 배포 VM은 물론, Azure 서비스 관리자 배포 VM도 보호할 수 있습니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다. 이 문서는 리소스 관리자 모델을 사용하여 생성된 VM 사용에 대해 설명합니다.
> 
> 

이 문서는 PowerShell을 사용하여 VM을 보호하고, 복구 지점으로부터 데이터를 복원하는 단계를 안내합니다.

## <a name="concepts"></a>개념
Azure Backup Service를 잘 모르는 경우, [Azure 백업이란?](backup-introduction-to-azure-backup.md)에서 서비스에 대한 개요를 확인하세요. 시작하기 전에 먼저 Azure 백업 작업에 필요한 필수 조건 및 현재 VM 솔루션의 제한에 관한 기본 사항을 다루었는지 확인합니다.

PowerShell을 효과적으로 사용하기 위해 개체의 계층 구조와 시작하는 위치를 이해하는 데 필요합니다.

![복구 서비스 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRmRecoveryServicesBackup PowerShell cmdlet 참조를 보려면 Azure 라이브러리의 [Azure 백업 - 복구 서비스 cmdlet](https://msdn.microsoft.com/library/mt723320.aspx) 을 참조하세요.
AzureRmRecoveryServicesVault PowerShell cmdlet 참조를 보려면 [Azure 복구 서비스 cmdlet](https://msdn.microsoft.com/library/mt643905.aspx)을 참조하세요.

## <a name="setup-and-registration"></a>설정 및 등록
시작하려면

1. [PowerShell 최신 버전 다운로드](https://github.com/Azure/azure-powershell/releases) (필요한 최소 버전: 1.4.0)
2. 다음 명령을 입력하여 사용할 수 있는 Azure 백업 PowerShell cmdlet을 찾습니다.

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


다음 작업은 PowerShell로 자동화할 수 있습니다.

* 복구 서비스 자격 증명 모음 만들기
* Azure VM 백업 또는 보호
* 백업 작업 트리거
* 백업 작업 모니터링
* Azure VM 복원

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기
다음 단계는 복구 서비스 자격 증명 모음을 만드는 과정을 안내합니다. 복구 서비스 자격 증명 모음은 백업 자격 증명 모음과 다릅니다.

1. 처음으로 Azure Backup을 사용하는 경우 **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록해야 합니다.
   
    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. 복구 서비스 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)** cmdlet을 사용하여 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  
   
    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** cmdlet을 사용하여 새 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.
   
    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. [LRS(로컬 중복 저장소)](../storage/storage-redundancy.md#locally-redundant-storage) 또는 [GRS(지역 중복 저장소)](../storage/storage-redundancy.md#geo-redundant-storage) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testVault에 대한 BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.
   
    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```
   
   > [!TIP]
   > 많은 Azure 백업 cmdlet에는 복구 서비스 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 복구 서비스 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.
   > 
   > 

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기
**[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)**를 사용하여 현재 구독의 모든 자격 증명 모음 목록을 볼 수 있습니다. 이 명령을 사용하여 새 자격 증명 모음이 만들어졌는지 확인하거나 구독에서 사용할 수 있는 자격 증명 모음을 확인할 수 있습니다.

Get-AzureRmRecoveryServicesVault 명령을 실행합니다. 그러면 구독의 모든 자격 증명 모음이 나열됩니다.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="backup-azure-vms"></a>Azure VM 백업
복구 서비스 자격 증명 모음을 만들었으므로 이를 사용하여 가상 컴퓨터를 보호할 수 있습니다. 그러나 보호를 적용하기 전에 자격 증명 모음 컨텍스트를 설정해야 하며 보호 정책을 확인하려고 할 수 있습니다. 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터 형식을 정의합니다. 보호 정책은 백업 작업이 실행될 때와 각 백업 스냅숏에 유지되는 기간에 대한 일정입니다.

VM에 대한 보호를 활성화하기 전에, 자격 증명 모음 컨텍스트를 설정해야 합니다. 컨텍스트는 모든 후속 cmdlet에 적용됩니다.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>보호 정책 만들기 
새 자격 증명 모음을 만들 때 기본 정책으로 제공됩니다. 이 정책은 매일 지정된 시간에 백업 작업을 트리거합니다. 기본 정책당 백업 스냅숏은 30일간 유지됩니다. 기본 정책을 사용하여 VM을 신속하게 보호하고, 나중에 다른 세부 정보로 정책을 편집할 수 있습니다.

자격 증명 모음에서 사용할 수 있는 정책의 목록을 보려면 **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)**을 사용합니다.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
> 
> 

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다.  보존 정책은 복구 지점을 Azure 백업에 얼마나 오래 유지할지를 정의합니다. 기본 보존 정책을 보려면 **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** 를 사용합니다.  마찬가지로 **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** 를 사용하여 기본 일정 정책을 볼 수 있습니다. 일정 및 보존 정책 개체는 **New-AzureRmRecoveryServicesBackupProtectionPolicy** cmdlet에 대해 입력으로 사용됩니다.

백업 보호 정책은 항목의 백업 수행 시점과 빈도를 정의합니다. New-AzureRmRecoveryServicesBackupProtectionPolicy cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다. 백업 정책은 Enable-AzureRmRecoveryServicesBackupProtection cmdlet에 대한 입력으로 사용됩니다.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>보호 사용
보호 활성화에는 항목 및 정책이라는 두 가지 개체가 관련됩니다. 두 개체 모두 자격 증명 모음에 대한 보호를 활성화해야 합니다. 정책이 자격 증명 모음과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

암호화되지 않은 ARM VM에 대한 보호를 사용하도록 설정하려면

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

암호화된 VM[BEK 및 KEK를 사용하여 암호화된] VM에 대한 보호를 사용하도록 설정하려면 Azure 백업 서비스에 대해 키 자격 증명 모음에서 키 및 암호를 읽을 수 있는 권한을 제공해야 합니다.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

ASM 기반 VM의 경우

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>보호 정책 수정
정책을 수정하기 위해서는, BackupSchedulePolicyObject 또는 BackupRetentionPolicy 개체를 수정하고 Set-AzureRmRecoveryServicesBackupProtectionPolicy를 사용하여 정책을 수정합니다.

다음 예제는 보존 카운트를 365개로 변경합니다.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## <a name="run-an-initial-backup"></a>초기 백업 실행
백업 일정은 항목에 대한 초기 백업에 전체 백업을 트리거합니다. 후속 백업의, 백업은 증분 복사본입니다. 초기 백업을 강제로 특정 시간에 수행하거나 즉시 수행하려는 경우 **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)** cmdlet을 사용합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: PowerShell에서 StartTime 및 EndTime 필드의 표준 시간대는 UTC입니다. 하지만, 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
> 
> 

## <a name="monitoring-a-backup-job"></a>백업 작업 모니터링
Azure 백업의 장기 실행 작업 대부분은 하나의 작업으로 모델링됩니다. 이는 Azure 포털이 항상 열려 있지 않아도 진행률을 쉽게 추적할 수 있게 해줍니다.

진행 중인 작업의 최신 상태를 가져오려면 Get-AzureRmRecoveryservicesBackupJob cmdlet을 사용합니다.

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료하기 위해 폴링하는 대신(불필요한 추가 코드) **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)** cmdlet을 사용합니다. 이 cmdlet은 작업이 완료되거나 지정된 시간 제한 값에 도달할 때까지 실행을 일시 중지합니다.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Azure VM 복원
Azure 포털을 사용하여 VM을 복원하는 방식과 PowerShell을 사용하여 VM을 복원하는 방식 간에는 주요 차이점이 있습니다. PowerShell을 사용하면 복구 지점에서 디스크 및 구성 정보가 만들어질 때 복원 작업이 완료됩니다. 복원 작업 중에 가상 컴퓨터는 만들어지지 않습니다. 디스크에서 가상 컴퓨터를 만들기 위한 지침이 제공됩니다. 그러나 VM을 완전히 복원하려면 다음 절차에 따라 작업을 진행해야 합니다.

* VM 선택
* 복구 지점 선택
* 디스크 복원
* 저장된 디스크에서 VM 만들기

아래 그래픽은 RecoveryServicesVault에서 BackupRecoveryPoint로 내려가면서 개체 계층 구조를 보여 줍니다.

![BackupContainer를 표시하는 복구 서비스 개체 계층 구조](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

백업 데이터를 복원하려면 백업 항목 및 지정 시간 데이터가 포함된 복구 지점을 식별합니다. 그런 후 **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** cmdlet을 사용하여 자격 증명 모음에서 고객의 계정으로 데이터를 복원합니다.

### <a name="select-the-vm"></a>VM 선택
올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택하려면 **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** cmdlet을 사용하고 **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)** cmdlet으로 파이프합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택
**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모를 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예를 들어 $rp[0]은 최신 복구 지점을 선택합니다.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>디스크 복원
**[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** cmdlet을 사용하여 백업 항목에 대한 데이터 및 구성을 복구 지점으로 복원합니다. 복구 지점을 식별한 후에는 **-RecoveryPoint** 매개 변수의 값으로 사용합니다. 앞의 예제 코드에서는 **$rp[0]** 이 사용할 복구 지점으로 선택되었습니다. 아래 샘플 코드에서는 **$rp[0]** 이 디스크로 복원하는 데 사용할 복구 지점으로 지정됩니다.

디스크 및 구성 정보를 복원하려면

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

복원 작업이 완료되면 **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** cmdlet을 사용하여 복원 작업의 세부 정보를 가져올 수 있습니다. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 포함됩니다.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

디스크를 복원한 후 VM을 만드는 방법에 자세한 내용을 보려면 다음 섹션으로 이동합니다.

### <a name="create-a-vm-from-restored-disks"></a>복원된 디스크에서 VM 만들기
디스크를 복원한 후 다음 단계를 사용하여 디스크에서 가상 컴퓨터를 만들고 구성합니다.

1. 복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.
   
    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```
2. Azure 저장소 컨텍스트를 설정하고 JSON 구성 파일을 복원합니다.
   
    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```
3. JSON 구성 파일을 사용하여 VM 구성을 만듭니다.
   
    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```
4. OS 디스크 및 데이터 디스크를 연결합니다.
   
      암호화되지 않은 VM의 경우
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
      PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
       ```
      For encrypted VMs, you need to specify [Key vault information](https://msdn.microsoft.com/library/dn868052.aspx) before you can attach disks.
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)     {     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach     }     ```
5. 네트워크 설정을 지정합니다.
   
    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 가상 컴퓨터를 만듭니다.
   
    ```
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>다음 단계
PowerShell을 사용하여 Azure 리소스와 연결하려는 경우 Windows Server를 보호하기 위한 PowerShell 문서, [Windows Server에 대한 백업 배포 및 관리](backup-client-automation.md)를 확인하세요. DPM 백업을 관리하기 위한 PowerShell 문서, [DPM에 대한 백업 배포 및 관리](backup-dpm-automation.md)도 있습니다. 이러한 문서 모두에 Resource Manager 배포용 버전뿐 아니라 클래식 배포용 버전도 포함되어 있습니다.  




<!--HONumber=Nov16_HO5-->


