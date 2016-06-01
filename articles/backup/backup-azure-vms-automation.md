<properties
   pageTitle="PowerShell을 사용하여 ARM VM에 대한 백업 관리 및 배포 | Microsoft Azure"
   description="PowerShell을 사용하여 ARM VM에 대해 Azure에서 백업 관리 및 배포"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# PowerShell을 사용하여 ARM VM에 대한 백업 관리 및 배포

> [AZURE.SELECTOR]
- [Resource Manager PowerShell](backup-azure-vms-automation.md)
- [클래식 PowerShell](backup-azure-vms-classic-automation.md)

이 문서는 Azure PowerShell을 사용하여 복구 서비스 자격 증명 모음으로부터 Azure 가상 컴퓨터(VM)를 복원하고 백업하는 방법을 보여줍니다. 복구 서비스 자격 증명 모음은 Azure Resource Manager(ARM) 리소스이며 Azure 백업 및 Azure Site Recovery 서비스에서 데이터와 자산을 보호하는 데 사용됩니다. ARM 배포에서 작업하는 경우 복구 서비스 자격 증명 모음을 사용합니다. 복구 서비스 자격 증명 모음을 사용하여 Azure 서비스 관리자(ASM)로 배포된 VM은 물론 ARM VM을 보호할 수 있습니다.

>[AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../resource-manager-deployment-model.md) 모델이 있습니다. 이 문서는 리소스 관리자 모델을 사용하여 생성된 VM 사용에 대해 설명합니다.

이 문서는 PowerShell을 사용하여 VM을 보호하고, 복구 지점으로부터 데이터를 복원하는 단계를 안내합니다.

## 개념

Azure 백업 서비스를 잘 모르는 경우, [Azure 백업이란?](backup-introduction-to-azure-backup.md)에서 서비스에 대한 개요를 확인하세요. 시작하기 전에 먼저 Azure 백업 작업에 필요한 필수 조건 및 현재 VM 솔루션의 제한에 관한 기본 사항을 다루었는지 확인합니다.

PowerShell을 효과적으로 사용하기 위해 개체의 계층 구조와 시작하는 위치를 이해하는 데 필요합니다.

![복구 서비스 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## 설정 및 등록

시작하려면

1. [PowerShell 최신 버전 다운로드](https://github.com/Azure/azure-powershell/releases)(필요한 최소 버전은 1.0.0)

2. 다음 명령을 입력하여 사용할 수 있는 Azure 백업 PowerShell cmdlet을 찾습니다.

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


다음 작업은 PowerShell로 자동화할 수 있습니다.

- 복구 서비스 자격 증명 모음 만들기
- Azure VM 백업 또는 보호
- 백업 작업 트리거
- 백업 작업 모니터링
- Azure VM 복원

## 복구 서비스 자격 증명 모음 만들기

> [AZURE.TIP] 처음으로 Azure 백업을 사용하는 고객의 경우, 구독과 함께 사용할 Azure 복구 서비스 공급자를 등록해야 합니다. 이는 다음 명령을 실행하여 수행할 수 있습니다. Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

**New-AzureRmRecoveryServicesVault** cmdlet을 사용하여 복구 서비스 자격 증명 모음을 새로 만들 수 있습니다. 복구 서비스 자격 증명 모음은 ARM 리소스이므로 리소스 그룹 내에 배치해야 합니다. 승격된 Azure PowerShell 콘솔에서 다음 명령을 실행합니다.

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

구독 내 모든 복구 서비스 자격 증명 모음 목록을 보려면 **Get-AzureRmRecoveryServicesVault** cmdlet을 사용합니다.

### 저장소 중복 설정
복구 서비스 자격 증명 모음을 만들 때, 로컬 중복 저장소(LRS) 또는 지역 중복 저장소(GRS) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testVault에 대한 BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여줍니다.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] 많은 Azure 백업 cmdlet에는 복구 서비스 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 복구 서비스 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.


## Azure VM 백업

VM에 대한 보호를 활성화하기 전에, 자격 증명 모음 컨텍스트를 설정해야 합니다. 컨텍스트는 모든 후속 cmdlet에 적용됩니다.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### 보호 정책 만들기

새 자격 증명 모음을 만들 때 기본 정책으로 제공됩니다. 이 정책은 매일 오후 9시30분에 백업 작업을 트리거합니다. 백업 스냅숏은 30일간 유지됩니다. 기본 정책을 사용하여 VM을 신속하게 보호하고, 나중에 다른 세부 정보로 정책을 편집할 수 있습니다.

자격 증명 모음에서 사용할 수 있는 정책의 목록을 보려면 Get-AzureRmRecoveryServicesBackupProtectionPolicy cmdlet을 사용합니다.

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 Azure 백업에 얼마나 오래 유지할지를 정의합니다. 기본 보존 정책을 보려면 Get-AzureRmRecoveryServicesBackupRetentionPolicyObject를 사용합니다. 마찬가지로 Get-AzureRmRecoveryServicesBackupSchedulePolicyObject를 사용하여 기본 일정 정책을 볼 수 있습니다. 일정 및 보존 정책 개체는 New-AzureRmRecoveryServicesBackupProtectionPolicy cmdlet에 대해 입력으로 사용됩니다.

백업 보호 정책은 항목의 백업 수행 시점과 빈도를 정의합니다. New-AzureRmRecoveryServicesBackupProtectionPolicy cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다. 백업 정책은 Enable-AzureRmRecoveryServicesBackupProtection cmdlet에 대한 입력으로 사용됩니다.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### 보호 사용

보호 활성화에는 항목 및 정책이라는 두 가지 개체가 관련됩니다. 두 개체 모두 자격 증명 모음에 대한 보호를 활성화해야 합니다. 정책이 항목과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

ASM 기반 VM의 경우

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### 보호 정책 수정

정책을 수정하기 위해서는, BackupSchedulePolicyObject 또는 BackupRetentionPolicy 개체를 수정하고 Set-AzureRmRecoveryServicesBackupProtectionPolicy를 사용하여 정책을 수정합니다.

다음 예제는 보존 카운트를 365개로 변경합니다.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## 초기 백업 실행

백업 일정은 항목에 대한 초기 백업에 전체 백업을 트리거합니다. 후속 백업의, 백업은 증분 복사본입니다. 초기 백업을 강제로 특정 시간에 수행하거나 즉시 수행하려는 경우 Backup-AzureRmRecoveryServicesBackupItem cmdlet을 사용합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: PowerShell에서 StartTime 및 EndTime 필드의 표준 시간대는 UTC입니다. 하지만, 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

## 백업 작업 모니터링

Azure 백업의 장기 실행 작업 대부분은 하나의 작업으로 모델링됩니다. 이는 Azure 포털이 항상 열려 있지 않아도 진행률을 쉽게 추적할 수 있게 해줍니다.

진행 중인 작업의 최신 상태를 가져오려면 Get-AzureRMRecoveryservicesBackupJob cmdlet을 사용합니다.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료하기 위해 폴링하는 대신(불필요한 추가 코드) Wait-AzureRmRecoveryServicesBackupJob cmdlet을 사용하는 것이 더 간단합니다. 스크립트를 사용하면 작업이 완료되거나 지정된 시간 제한 값에 도달할 때까지 cmdlet이 실행을 일시 중지합니다.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Azure VM 복원

백업 데이터를 복원하려면, 백업 항목 및 지정 시간 데이터가 포함된 복구 지점을 식별해야 합니다. 이 정보는 자격 증명 모음에서 고객의 계정으로 데이터 복원을 시작하도록 Restore-AzureRMRecoveryServicesBackupItem cmdlet에 제공됩니다.

### VM 선택

올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택하려면 Get-AzureRmRecoveryServicesBackupContainer cmdlet을 사용하고 Get-AzureRmRecoveryServicesBackupItem cmdlet으로 파이프합니다.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### 복구 지점 선택

이제 Get-AzureRMRecoveryServicesBackupRecoveryPoint cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열하고 복원할 복구 지점을 선택할 수 있습니다. 일반적으로 사용자는 목록에서 가장 최근의 AppConsistent 지점을 선택합니다.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

$rp 변수는 선택한 백업 항목에 대한 복구 지점의 배열로, 시간의 역순으로 정렬되며 최신 복구 지점은 인덱스 0에 있습니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예를 들어 $rp[0]은 최신 복구 지점을 선택합니다.

### 디스크 복원

Azure 포털 및 Azure PowerShell을 통해 수행된 복원 작업 간의 주요 차이점이 있습니다. PowerShell을 사용하면 복구 지점에서 디스크 및 구성 정보를 복원할 때 복원 작업이 중지됩니다. 가상 컴퓨터를 만들지 않습니다.

> [AZURE.WARNING] Restore-AzureRMRecoveryServicesBackupItem은 VM을 만들지 않고, 지정된 저장소 계정에 디스크를 복원합니다. 이것은 Azure 포털에서 벌어지는 상황과 다릅니다.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

복원 작업이 완료되면 Get-AzureRmRecoveryServicesBackupJobDetails cmdlet을 사용하여 복원 작업의 세부 정보를 가져올 수 있습니다. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 포함됩니다.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## 복구 서비스 자격 증명 모음에 Windows Server 또는 DPM 등록

복구 서비스 자격 증명 모음을 만든 후에, 최신 에이전트 및 보관 자격 증명을 다운로드하여 편리한 위치(예: C:\\Downloads)에 저장합니다.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Windows Server 또는 DPM 서버에서, [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet을 실행하여 컴퓨터를 자격 증명 모음에 등록합니다.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->