---
title: 백업 및 PowerShell을 사용 하 여 Azure Backup을 사용 하 여 Azure Vm 복구
description: 백업 및 PowerShell을 사용 하 여 Azure Backup을 사용 하 여 Azure Vm을 복구 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 4df65819256e6a81a07927d463d130fbfdf9317a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255005"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>백업 및 PowerShell 사용 하 여 Azure Vm 복원

이 문서에서는 백업 및 Azure VM을 복원 하는 방법을 설명를 [Azure Backup](backup-overview.md) Recovery Services vault PowerShell cmdlet을 사용 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음을 만들고 자격 증명 모음 컨텍스트를 설정합니다.
> * 백업 정책 정의
> * 여러 가상 머신을 보호하기 위해 백업 정책 적용
> * 보호된 가상 머신에 대한 주문형 백업 작업 트리거. 가상 머신을 백업하거나 보호하려면 먼저 [필수 조건](backup-azure-arm-vms-prepare.md)을 완료하여 VM을 보호하기 위한 환경을 준비해야 합니다.

## <a name="before-you-start"></a>시작하기 전에

- [자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음에 대 한 합니다.
- [검토](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 백업에 대 한 아키텍처 [알아봅니다](backup-azure-vms-introduction.md) 백업 프로세스 및 [검토](backup-support-matrix-iaas.md) 지원, 제한 사항 및 필수 구성 요소입니다.
- Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 됩니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

검토 합니다 **Az.RecoveryServices** [cmdlet 참조](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) Azure 라이브러리에서 참조 합니다.

## <a name="set-up-and-register"></a>설정 및 등록

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

시작하려면

1. [PowerShell의 최신 버전 다운로드](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. 다음 명령을 입력하여 사용할 수 있는 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음의 별칭과 cmdlet이 나타납니다. 다음 이미지는 표시되는 예이며 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services 목록](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다. 이 cmdlet은 계정 자격 증명을 묻는 웹 페이지를 엽니다.

    * 또는 **Connect-AzAccount** cmdlet에서 **-Credential** 매개 변수를 사용하여 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예를 들면 다음과 같습니다. **Connect-AzAccount -Tenant "fabrikam.com"**

4. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. 에 Azure Backup을 처음 사용 하는 경우 사용 해야 합니다 **[레지스터 AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** 구독과 관련 하 여 Azure Recovery Service 공급자를 등록 하는 cmdlet입니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 다음 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인할 수 있습니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    명령 출력에서 **RegistrationState**는 **등록됨**으로 변경해야 합니다. 그렇지 않으면 실행 하는 경우는 **[등록 AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet을 다시 합니다.


## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다. Recovery Services 자격 증명 모음은 Backup 자격 증명 모음과 다릅니다.

1. Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용 하거나 사용 하 여 리소스 그룹을 만들 수 있습니다 합니다 **[새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) cmdlet을 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md) 또는 [GRS(지역 중복 저장소)](../storage/common/storage-redundancy-grs.md) 중에 사용할 저장소 중복 유형을 지정합니다. 다음 예제는 testvault에 대한 -BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음 예제와 유사하며, 연관된 ResourceGroupName 및 위치가 제공됩니다.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Azure VM 백업

Recovery Services 자격 증명 모음을 사용하여 가상 머신을 보호합니다. 보호를 적용하기 전에 자격 증명 모음 컨텍스트(자격 증명 모음에 보호된 데이터 형식)를 설정하고 보호 정책을 확인합니다. 보호 정책은 백업 작업을 실행하는 시간과 각 백업 스냅샷을 유지하는 기간에 대한 일정입니다.

### <a name="set-vault-context"></a>자격 증명 모음 컨텍스트 설정

VM에 대 한 보호를 설정 하기 전에 사용 하 여 [집합 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) 자격 증명 모음 컨텍스트를 설정 합니다. 자격 증명 모음 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다. 다음 예제에서는 자격 증명 모음, *testvault*에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>저장소 복제 설정을 수정합니다.

사용 하 여 [집합 AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) LRS/GRS를 자격 증명 모음의 저장소 복제 구성을 설정 하는 명령

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> 자격 증명 모음에 보호된 백업 항목이 없는 경우에만 스토리지 중복을 수정할 수 있습니다.

### <a name="create-a-protection-policy"></a>보호 정책 만들기

Recovery Services 자격 증명 모음을 만들면 기본 보호 및 보존 정책이 함께 제공됩니다. 기본 보호 정책은 매일 지정된 시간에 백업 작업을 트리거합니다. 기본 보존 정책은 매일 복구 지점을 30일 동안 유지합니다. 기본 정책을 사용하여 VM을 신속하게 보호하고, 나중에 다른 세부 정보로 정책을 편집할 수 있습니다.

사용 하 여 **[Get AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** 자격 증명 모음에서 사용할 수 있는 보호 정책을 보려면. 특정 정책을 받거나 워크로드 유형과 연결된 정책을 보려면 이 cmdlet을 사용할 수 있습니다. 다음 예제에서는 워크로드 유형, AzureVM에 대한 정책을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

다음 예제와 유사하게 출력됩니다.

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 보관 하는 기간을 삭제 하기 전에 정의 합니다.

- 기본 보존 정책을 보려면 [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)를 사용합니다.
- 마찬가지로 [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)를 사용하여 기본 일정 정책을 볼 수 있습니다.
- [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다.
- 일정 및 보존 정책 개체를 새로 만들기-AzRecoveryServicesBackupProtectionPolicy cmdlet에 대 한 입력으로 사용 됩니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 됩니다. 원하는 시작 시간에 시작 시간을 변경 하려면 다음 예제를 사용 합니다. 물론 원하는 시작 시간 UTC 여야 합니다. 아래 예제에서는 원하는 시작 시간은 매일 백업의 오전 01시 UTC를 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 예제에서는 이러한 변수를 사용하여 보호 정책, *NewPolicy*를 만들 때 매개 변수를 정의합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

다음 예제와 유사하게 출력됩니다.

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>보호 사용

보호 정책을 정의한 후에는 항목에 대한 정책을 계속 사용해야 합니다. 사용 하 여 [Enable AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 보호를 사용 하도록 설정 합니다. 보호를 사용하도록 설정하는 데에는 항목 및 정책이라는 두 가지 개체가 필요합니다. 정책이 자격 증명 모음과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

다음 예제에서는 NewPolicy 정책을 사용하여 V2VM 항목에 대해 보호를 활성화합니다. 예제는 VM이 암호화되었는지 여부와 암호화 유형에 따라 다릅니다.

**암호화되지 않은 Resource Manager VM**에 대한 보호를 사용하도록 설정하려면

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

암호화 된 Vm (BEK 및 KEK를 사용 하 여 암호화 됨)에 대 한 보호를 사용 하려면 key vault에서 키 및 비밀을 읽을 Azure Backup 서비스 권한을 부여 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

**암호화된 VM(BEK만 사용하여 암호화된)** 에 대한 보호를 사용하도록 설정하려면 Azure Backup 서비스에 주요 자격 증명 모음에서 암호를 읽을 수 있는 권한을 제공해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Azure Government 클라우드를 사용 하는 경우 다음 사용 하 여 값 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 ServicePrincipalName 매개 변수에서 [집합 AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet.
>

## <a name="monitoring-a-backup-job"></a>백업 작업 모니터링

Azure Portal을 사용하지 않고 백업 작업과 같은 장기 실행 작업을 모니터링할 수 있습니다. 진행 중인 작업의 상태를 가져오려면 합니다 [Get AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet. 이 cmdlet은 특정 자격 증명 모음에 대한 백업 작업을 가져오고 해당 자격 증명 모음은 자격 증명 모음 컨텍스트에 지정됩니다. 다음 예제에서는 배열과 같은 진행 중인 작업의 상태를 가져와 $joblist 변수에 상태를 저장합니다.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

다음 예제와 유사하게 출력됩니다.

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료 하기 위해 불필요 한 추가 코드는-폴링하는 대신 사용 합니다 [대기 AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet. 이 cmdlet은 작업이 완료되거나 지정된 시간 제한 값에 도달할 때까지 실행을 일시 중지합니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Azure VM 백업 관리

### <a name="modify-a-protection-policy"></a>보호 정책 수정

보호 정책을 수정 하려면 사용 하 여 [집합 AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 여 SchedulePolicy 또는 RetentionPolicy 개체를 수정 합니다.

#### <a name="modifying-scheduled-time"></a>예약 된 시간 수정

보호 정책을 만들 때 시작 시간을 기본적으로 할당 됩니다. 다음 예제에서는 보호 정책의 시작 시간을 수정 하는 방법을 보여 줍니다.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>보존 수정

다음 예제는 복구 지점 보존 기간을 365일로 변경합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>즉시 복원을 스냅숏 보존 구성

> [!NOTE]
> Az PS 버전 1.6.0부터 Powershell을 사용 하 여 정책 즉시 복원을 스냅숏 보존 기간을 업데이트할 수 있습니다 하나

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

기본값은 2가 됩니다, 그리고 사용자 1의 최소 및 최대 5 개를 사용 하 여 값을 설정할 수 있습니다. 매주 백업 정책에 대 한 기간은 5로 설정 되며 변경할 수 없습니다.

### <a name="trigger-a-backup"></a>백업 트리거

사용 하 여 [백업 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 백업 작업을 트리거할 수 있습니다. 초기 백업인 경우 전체 백업입니다. 후속 백업에서는 증분 복사를 수행합니다. 다음 예에서는 VM을 60 일 동안 보존할 백업 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

다음 예제와 유사하게 출력됩니다.

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell에서 표시되는 StartTime 및 EndTime 필드의 시간대는 UTC입니다. 하지만, 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

### <a name="change-policy-for-backup-items"></a>백업 항목에 대 한 정책 변경

사용자 기존 정책을 수정 하거나 Policy2를 Policy1에서 백업 항목의 정책을 변경할 수 있습니다. 백업 항목에 대 한 정책으로 전환 하려면 단순히 관련 정책을 가져올 항목을 백업 하 고 사용 합니다 [사용 AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 매개 변수로 백업 항목을 사용 하 여 명령입니다.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

명령 백업 구성 완료 되 고 다음과 같은 출력이 반환 될 때까지 대기 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

사용자는 보호를 중지 하려는, 하는 경우 사용할 수 있습니다는 [사용 안 함-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet. 이 예약된 된 백업을 중지 되지만까지 지 원하는 데이터를 영구적으로 유지 됩니다.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>백업 데이터 삭제

저장된 된 백업 데이터를 자격 증명 모음에서에서 완전히 제거 하기 위해 추가 '-RemoveRecoveryPoints' 플래그/교체 하는 [protection 명령 ' disable'](#retain-data)합니다.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure VM 복원

Azure Portal을 사용하여 VM을 복원하는 방식과 PowerShell을 사용하여 VM을 복원하는 방식 간에는 주요 차이점이 있습니다. PowerShell을 사용하면 복구 지점에서 디스크 및 구성 정보가 만들어질 때 복원 작업이 완료됩니다. 복원 작업 중에 가상 머신은 만들어지지 않습니다. 디스크에서 가상 머신을 만들려면 [저장된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 섹션을 참조하세요. 전체 VM을 복원하지 않고 Azure VM 백업에서 몇 개의 파일을 복원하거나 복구하려면 [파일 복구](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) 섹션을 참조하세요.

> [!Tip]
> 복원 작업 중에 가상 머신은 만들어지지 않습니다.
>
>

다음 그래픽에서는 RecoveryServicesVault에서 BackupRecoveryPoint까지로 내려가는 개체 계층 구조를 보여 줍니다.

![BackupContainer를 표시하는 Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

백업 데이터를 복원하려면 백업 항목 및 지정 시간 데이터가 포함된 복구 지점을 식별합니다. 사용 하 여 [복원 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 계정에 자격 증명 모음에서 데이터를 복원 합니다.

Azure VM을 복원하는 기본 단계는 다음과 같습니다.

* VM을 선택합니다.
* 복구 지점을 선택합니다.
* 디스크를 복원합니다.
* 저장된 디스크에서 VM을 만듭니다.

### <a name="select-the-vm"></a>VM 선택

올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택 하려면 사용 합니다 [Get AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet에 파이프 하는 [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택

[Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모를 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 과거 7일부터 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

다음 예제와 유사하게 출력됩니다.

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>디스크 복원

사용 된 [복원 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 백업 항목의 데이터 및 구성을 복구 지점으로 복원 하는 cmdlet입니다. 복구 지점을 식별하고 나면 이 지점을 **-RecoveryPoint** 매개 변수의 값으로 사용합니다. 이전 샘플에서 **$rp[0]** 이 사용할 복구 지점이었지만, 아래 샘플 코드에서는 **$rp[0]** 이 디스크를 복원하는 데 사용할 복구 지점이 됩니다.

디스크 및 구성 정보를 복원하려면

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Managed Disks 복원

> [!NOTE]
> 백업된 VM에 있는 관리 디스크를 관리 디스크로 복원할 수 있도록 Azure PowerShell RM 모듈 v 6.7.0부터 복원 기능이 도입되었습니다. 해당 기능을 도입했습니다.
>
>

추가 매개 변수를 **TargetResourceGroupName**을 제공하여 Managed Disks를 복원할 RG를 지정합니다. 

> [!NOTE]
> 관리 디스크를 복원할 때는 **TargetResourceGroupName** 매개 변수를 사용하는 것이 좋습니다. 그러면 성능이 크게 개선되기 때문입니다. 또한 Azure PowerShell Az 모듈 1.0부터는 관리 디스크를 복원하는 경우 이 매개 변수를 반드시 사용해야 합니다.
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** 파일은 저장소 계정으로 복원되고, Managed Disks는 지정한 대상 RG로 복원됩니다.

다음 예제와 유사하게 출력됩니다.

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

사용 합니다 [대기 AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 복원 작업이 완료 될 때까지 기다립니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

복원 작업이 완료 되 면 사용 된 [Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) 복원 작업의 세부 정보를 가져오려는 cmdlet. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 포함됩니다.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

디스크를 복원한 후 다음 섹션으로 이동하여 VM을 만듭니다.

## <a name="create-a-vm-from-restored-disks"></a>복원된 디스크에서 VM 만들기

디스크를 복원한 후 다음 단계를 사용하여 디스크에서 가상 머신을 만들고 구성합니다.

> [!NOTE]
> 복원된 디스크에서 암호화된 VM을 만들려면 Azure 역할에 **Microsoft.KeyVault/vaults/deploy/action** 작업을 수행할 권한이 있어야 합니다. 사용자의 역할에 이 사용 권한이 없는 경우 이 작업이 포함된 사용자 지정 역할을 만드세요. 자세한 내용은 [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.
>
>

> [!NOTE]
> 디스크를 복원하면 이제 새 VM을 만드는 데 직접 사용할 수 있는 배포 템플릿을 가져올 수 있습니다. 암호화/암호화되지 않은 관리/비관리 VM을 만들기 위한 더 이상의 다른 PS cmdlet은 없습니다.

결과 작업 세부 정보는 쿼리 및 배포 가능한 템플릿 URI를 제공합니다.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

[여기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)에 설명된 대로 템플릿을 배포하여 새 VM을 만들면 됩니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

다음 섹션에서는 "VMConfig" 파일을 사용하여 VM을 만드는 데 필요한 단계를 나열합니다.

> [!NOTE]
> 위에 설명된 배포 템플릿을 사용하여 VM을 만드는 것이 좋습니다. 이 섹션(포인트 1-6)은 곧 삭제될 예정입니다.

1. 복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Azure 저장소 컨텍스트를 설정하고 JSON 구성 파일을 복원합니다.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. JSON 구성 파일을 사용하여 VM 구성을 만듭니다.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. OS 디스크 및 데이터 디스크를 연결합니다. 이 단계에서는 다양한 관리 및 암호화된 VM 구성에 대한 예제를 제공합니다. VM 구성에 맞는 예제를 사용하세요.

   * **관리되지 않고 암호화되지 않은 VM** - 관리되지 않고 암호화되지 않은 VM에는 다음 샘플을 사용합니다.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Azure AD를 포함하며 BEK만 사용하여 암호화된 비관리 VM** - Azure AD를 포함하며 BEK만 사용하여 암호화된 비관리 VM의 경우에는 키 자격 증명 모음에 비밀을 복원해야 디스크를 연결할 수 있습니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md)을 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다. OS 디스크를 설정할 때 관련 OS 형식을 언급해야 합니다.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 비관리 VM** - Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 비관리 VM의 경우에는 디스크를 연결하기 전에 키 자격 증명 모음에 키와 비밀을 복원합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md)을 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 비관리 VM** - Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 비관리 VM의 경우 원본 **키 자격 증명 모음/비밀을 사용할 수 없으면** [Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 비밀을 복원합니다. 그런 후에 다음 스크립트를 실행하여 복원된 OS Blob에서 암호화 세부 정보를 설정합니다. 데이터 Blob의 경우에는 이 단계를 수행하지 않아도 됩니다. 복원된 키 자격 증명 모음에서 $dekurl을 가져올 수 있습니다.<br>

   아래 스크립트는 원본 키 자격 증명 모음/비밀을 사용할 수 없을 때만 실행하면 됩니다.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    OS Blob에서 암호화 세부 정보가 설정되고 **비밀이 사용 가능**해지면 아래에 나와 있는 스크립트를 사용하여 디스크를 연결합니다.<br>

    원본 키 자격 증명 모음/비밀이 이미 사용 가능한 상태이면 위의 스크립트를 실행할 필요가 없습니다.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 비관리 VM** - Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 비관리 VM의 경우 원본 **키 자격 증명 모음/키/비밀을 사용할 수 없으면** [Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 키와 비밀을 복원합니다. 그런 후에 다음 스크립트를 실행하여 복원된 OS Blob에서 암호화 세부 정보를 설정합니다. 데이터 Blob의 경우에는 이 단계를 수행하지 않아도 됩니다. 복원된 키 자격 증명 모음에서 $dekurl 및 $kekurl을 가져올 수 있습니다.

   아래 스크립트는 원본 키 자격 증명 모음/키/비밀을 사용할 수 없을 때만 실행하면 됩니다.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   OS Blob에서 암호화 세부 정보가 설정되고 **키/비밀이 사용 가능**해지면 아래에 나와 있는 스크립트를 사용하여 디스크를 연결합니다.

    원본 키 자격 증명 모음/키/비밀이 사용 가능한 상태이면 위의 스크립트를 실행할 필요가 없습니다.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **관리 및 암호화되지 않은 VM** - 암호하되지 않은 관리되는 VM의 경우 복원된 Managed Disks를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

   * **Azure AD를 포함하며 BEK만 사용하여 암호화된 관리형 VM** - Azure AD를 포함하며 BEK만 사용하여 암호화된 관리형 VM의 경우 복원된 관리형 디스크를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

   * **Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 관리형 VM** - Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 관리형 VM의 경우에는 복원된 관리 디스크를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

   * **Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 관리형 VM** - Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 관리형 VM의 경우 원본 **키 자격 증명 모음/비밀을 사용할 수 없으면** [Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 비밀을 복원합니다. 그런 후에 다음 스크립트를 실행하여 복원된 OS 디스크에서 암호화 세부 정보를 설정합니다. 데이터 디스크의 경우에는 이 단계를 수행하지 않아도 됩니다. 복원된 키 자격 증명 모음에서 $dekurl을 가져올 수 있습니다.

     아래 스크립트는 원본 키 자격 증명 모음/비밀을 사용할 수 없을 때만 실행하면 됩니다.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     OS 디스크에서 암호화 세부 정보가 설정되고 비밀이 사용 가능해진 후에 복원된 관리 디스크를 연결하려면 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

   * **Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 관리형 VM** - Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 관리형 VM의 경우 원본 **키 자격 증명 모음/키/비밀을 사용할 수 없으면** [Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 키 및 비밀을 복원합니다. 그런 후에 다음 스크립트를 실행하여 복원된 OS 디스크에서 암호화 세부 정보를 설정합니다. 데이터 디스크의 경우에는 이 단계를 수행하지 않아도 됩니다. 복원된 키 자격 증명 모음에서 $dekurl 및 $kekurl을 가져올 수 있습니다.

   아래 스크립트는 원본 키 자격 증명 모음/키/비밀을 사용할 수 없을 때만 실행하면 됩니다.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    OS 디스크에서 암호화 세부 정보가 설정되고 키/비밀이 사용 가능해진 후에 복원된 관리 디스크를 연결하려면 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

5. 네트워크 설정을 지정합니다.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. 가상 머신을 만듭니다.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. ADE 확장을 푸시합니다.

   * **Azure AD가 포함된 VM** - 다음 명령을 사용하여 데이터 디스크에 대해 암호화를 수동으로 사용하도록 설정합니다.  

     **BEK만 사용**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Azure AD가 포함되지 않은 VM** - 다음 명령을 사용하여 데이터 디스크에 대해 암호화를 수동으로 사용하도록 설정합니다.

     명령 실행 중에 AADClientID를 입력하라는 메시지가 표시되면 Azure PowerShell을 업데이트해야 합니다.

     **BEK만 사용**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM 백업에서 파일 복원

디스크를 복원하는 것 외에 Azure VM 백업에서 개별 파일을 복원할 수도 있습니다. 파일 복원 기능은 복구 지점의 모든 파일에 대한 액세스를 제공합니다. 일반 파일의 경우처럼 파일 탐색기를 통해 파일을 관리합니다.

Azure VM 백업에서 파일을 복원하는 기본 단계는 다음과 같습니다.

* VM 선택
* 복구 지점 선택
* 복구 지점의 디스크 탑재
* 필요한 파일 복사
* 디스크 분리

### <a name="select-the-vm"></a>VM 선택

올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택 하려면 사용 합니다 [Get AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet에 파이프 하는 [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택

[Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모를 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 과거 7일부터 선택한 백업 항목에 대한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

다음 예제와 유사하게 출력됩니다.

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>복구 지점의 디스크 탑재

사용 된 [Get AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) 복구 지점의 모든 디스크를 탑재 하는 스크립트를 가져오려는 cmdlet.

> [!NOTE]
> 디스크는 스크립트가 실행되는 컴퓨터에 iSCSI 연결 디스크로 탑재됩니다. 탑재가 즉시 발생하며, 요금이 부과되지 않습니다.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

다음 예제와 유사하게 출력됩니다.

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

파일을 복구할 컴퓨터에서 스크립트를 실행합니다. 스크립트를 실행하려면 제공된 암호를 입력해야 합니다. 디스크가 연결된 후에 Windows 파일 탐색기를 사용하여 새 볼륨 및 파일을 검색합니다. 자세한 내용은 Backup 관련 문서 [Azure Virtual Machine 백업에서 파일 복구](backup-azure-restore-files-from-vm.md)를 참조하세요.

### <a name="unmount-the-disks"></a>디스크 분리

사용 하 여 필요한 파일을 복사한 후 [사용 안 함-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) 디스크를 분리 하 합니다. 복구 지점의 파일에 대한 액세스가 제거되도록 디스크를 분리해야 합니다.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 Azure 리소스와 연결하려는 경우 [Windows Server용 Backup 배포 및 관리](backup-client-automation.md) PowerShell 문서를 참조하세요. DPM 백업을 관리하는 경우 [DPM에 대한 Backup 배포 및 관리](backup-dpm-automation.md) 문서를 참조하세요.
