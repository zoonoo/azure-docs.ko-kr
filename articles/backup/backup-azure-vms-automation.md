---
title: PowerShell을 사용 하 여 Azure Vm 백업 및 복구
description: PowerShell과 함께 Azure Backup를 사용 하 여 Azure Vm을 백업 및 복구 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 733a06a84aa170f1361ea74d126ec9752586fce2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363852"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>PowerShell을 사용 하 여 Azure Vm 백업 및 복원

이 문서에서는 PowerShell cmdlet을 사용 하 여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음에서 Azure VM을 백업 및 복원 하는 방법을 설명 합니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * Recovery Services 자격 증명 모음을 만들고 자격 증명 모음 컨텍스트를 설정 합니다.
> * 백업 정책 정의
> * 여러 가상 컴퓨터를 보호 하는 백업 정책 적용
> * 보호 된 가상 컴퓨터에 대 한 주문형 백업 작업 트리거 가상 컴퓨터를 백업 (또는 보호) 하려면 먼저 Vm을 보호할 수 있도록 환경을 준비 하는 [필수 구성 요소](backup-azure-arm-vms-prepare.md) 를 완료 해야 합니다.

## <a name="before-you-start"></a>시작 하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md) .
* Azure VM 백업에 대 한 아키텍처를 [검토](backup-architecture.md#architecture-built-in-azure-vm-backup) 하 고, 백업 프로세스 [에 대해 알아보고](backup-azure-vms-introduction.md) , 지원, 제한 사항 및 필수 구성 요소를 [검토](backup-support-matrix-iaas.md) 합니다.
* Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az. RecoveryServices** [cmdlet 참조](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) 참조를 검토 합니다.

## <a name="set-up-and-register"></a>설정 및 등록

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

시작 하려면 다음을 수행 합니다.

1. [최신 버전의 PowerShell 다운로드](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. 다음 명령을 입력 하 여 사용할 수 있는 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음에 대 한 별칭 및 cmdlet이 표시 됩니다. 다음 이미지는 표시 되는 항목의 예입니다. Cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services 목록](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. **AzAccount**를 사용 하 여 Azure 계정에 로그인 합니다. 이 cmdlet은 계정 자격 증명을 입력 하 라는 메시지를 표시 하는 웹 페이지를 표시 합니다.

    * 또는 **-Credential** 매개 변수를 사용 하 여 **AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테 넌 트를 대신 하 여 작업 중인 CSP 파트너인 경우 tenantID 또는 테 넌 트 주 도메인 이름을 사용 하 여 고객을 테 넌 트로 지정 합니다. 예: **AzAccount-Tenant "fabrikam.com"**

4. 계정에 여러 구독이 있을 수 있으므로 사용 하려는 구독을 계정과 연결 합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup를 처음으로 사용 하는 경우 **[AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet을 사용 하 여 Azure 복구 서비스 공급자를 구독에 등록 해야 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 다음 명령을 사용 하 여 공급자가 성공적으로 등록 되었는지 확인할 수 있습니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    명령 출력에서 **Registrationstate** 가 **등록**됨으로 변경 되어야 합니다. 그렇지 않은 경우 **[AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** cmdlet을 다시 실행 하면 됩니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내 합니다. Recovery Services 자격 증명 모음은 백업 자격 증명 모음과 다릅니다.

1. Recovery Services 자격 증명 모음은 리소스 관리자 리소스 이므로 리소스 그룹 내에 저장 해야 합니다. 기존 리소스 그룹을 사용 하거나 **[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet을 사용 하 여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정 합니다.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) cmdlet을 사용 하 여 Recovery Services 자격 증명 모음을 만듭니다. 리소스 그룹에 사용 된 자격 증명 모음에 대해 동일한 위치를 지정 해야 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 사용할 저장소 중복성 유형을 지정 합니다. [LRS (로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md) 또는 [GRS (지역 중복 저장소](../storage/common/storage-redundancy-grs.md))를 사용할 수 있습니다. 다음 예에서는 testvault 된에 대 한-BackupStorageRedundancy 옵션을 GeoRedundant로 설정 하는 방법을 보여 줍니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 입력으로 Recovery Services 자격 증명 모음 개체가 필요 합니다. 이러한 이유로 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장 하는 것이 편리 합니다.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>구독에서 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용 합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음 예제와 유사 합니다. 여기에는 연결 된 ResourceGroupName와 위치가 제공 됩니다.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Azure Vm 백업

Recovery Services 자격 증명 모음을 사용 하 여 가상 컴퓨터를 보호 합니다. 보호를 적용 하기 전에 자격 증명 모음 컨텍스트 (자격 증명 모음에서 보호 되는 데이터 유형)를 설정 하 고 보호 정책을 확인 합니다. 보호 정책은 백업 작업이 실행 되는 일정 및 각 백업 스냅숏의 보존 기간입니다.

### <a name="set-vault-context"></a>자격 증명 모음 컨텍스트 설정

VM에 대 한 보호를 사용 하도록 설정 하기 전에 [AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) 를 사용 하 여 자격 증명 모음 컨텍스트를 설정 합니다. 자격 증명 모음 컨텍스트가 설정 되 면 모든 후속 cmdlet에 적용 됩니다. 다음 예에서는 자격 증명 모음에 대 한 자격 증명 모음 컨텍스트 *testvault 된*를 설정 합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 가져옵니다.

Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 사용 중단 계획 합니다. 대신 자격 증명 모음 ID를 저장 하거나 인출 하 고 관련 명령에 전달할 수 있습니다. 따라서 자격 증명 모음 컨텍스트를 설정 하지 않았거나 특정 자격 증명 모음에 대해 실행할 명령을 지정 하려는 경우 다음과 같이 자격 증명 모음 ID를 모든 관련 명령에 "-vaultID"으로 전달 합니다.

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

디스크나

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>저장소 복제 설정 수정

[AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) 명령을 사용 하 여 자격 증명 모음에 대 한 저장소 복제 구성을 LRS/GRS로 설정 합니다.

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> 저장소 중복성은 자격 증명 모음으로 보호 되는 백업 항목이 없는 경우에만 수정할 수 있습니다.

### <a name="create-a-protection-policy"></a>보호 정책 만들기

Recovery Services 자격 증명 모음을 만들 때 기본 보호 및 보존 정책이 제공 됩니다. 기본 보호 정책은 매일 지정 된 시간에 백업 작업을 트리거합니다. 기본 보존 정책은 매일 복구 지점을 30 일 동안 유지 합니다. 기본 정책을 사용 하 여 VM을 신속 하 게 보호 하 고 나중에 다른 세부 정보를 사용 하 여 정책을 편집할 수 있습니다.

**[AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** 를 사용 하 여 자격 증명 모음에서 사용할 수 있는 보호 정책을 볼 수 있습니다. 이 cmdlet을 사용 하 여 특정 정책을 가져오거나 작업 유형과 연결 된 정책을 볼 수 있습니다. 다음 예에서는 작업 유형인 Add-azurevm에 대 한 정책을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

출력은 다음 예제와 유사 합니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell에서 BackupTime 시간 필드의 표준 시간대는 UTC입니다. 그러나 Azure Portal에 백업 시간이 표시 되 면 시간은 현지 표준 시간대로 조정 됩니다.
>
>

백업 보호 정책은 하나 이상의 보존 정책과 연결 됩니다. 보존 정책은 복구 지점을 삭제 하기 전까지 유지 되는 기간을 정의 합니다.

* [AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) 를 사용 하 여 기본 보존 정책을 볼 수 있습니다.
* 마찬가지로 [AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 를 사용 하 여 기본 일정 정책을 가져올 수 있습니다.
* [AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet은 백업 정책 정보를 보유 하는 PowerShell 개체를 만듭니다.
* 일정 및 보존 정책 개체는 AzRecoveryServicesBackupProtectionPolicy cmdlet에 대 한 입력으로 사용 됩니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 되어 있습니다. 다음 예를 사용 하 여 시작 시간을 원하는 시작 시간으로 변경할 수 있습니다. 원하는 시작 시간은 UTC로도 지정 해야 합니다. 아래 예제에서는 원하는 시작 시간이 매일 백업의 경우 01:00 AM UTC 라고 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30 분의 배수로 시작 시간을 제공 해야 합니다. 위의 예제에서 "01:00:00" 또는 "02:30:00"만 가능 합니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예에서는 일정 정책과 보존 정책을 변수에 저장 합니다. 이 예제에서는 이러한 변수를 사용 하 여 보호 정책 *Newpolicy*를 만들 때 매개 변수를 정의 합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

출력은 다음 예제와 유사 합니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>보호 사용

보호 정책을 정의한 후에도 항목에 대해 정책을 사용 하도록 설정 해야 합니다. [AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 를 사용 하 여 보호를 사용 하도록 설정 합니다. 보호를 사용 하도록 설정 하려면 항목 및 정책의 두 개체가 필요 합니다. 정책이 자격 증명 모음과 연결 되 면 정책 일정에 정의 된 시간에 백업 워크플로가 트리거됩니다.

> [!IMPORTANT]
> PS를 사용 하 여 한 번에 여러 Vm에 대 한 백업을 사용 하도록 설정 하는 동안 단일 정책에 연결 된 Vm이 100 이상 인지 확인 합니다. 권장 되는 [모범 사례](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy)입니다. 현재 PS 클라이언트는 100 개가 넘는 Vm이 있는 경우 명시적으로 차단 하지 않지만, 나중에 확인이 추가 될 예정입니다.

다음 예에서는 정책, NewPolicy를 사용 하 여 V2VM 항목에 대 한 보호를 사용 하도록 설정 합니다. 이 예제는 VM의 암호화 여부 및 암호화 유형에 따라 달라 집니다.

**암호화 되지 않은 리소스 관리자 vm**에 대 한 보호를 사용 하도록 설정 하려면:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

암호화 된 Vm (BEK 및 KEK를 사용 하 여 암호화 됨)에 대 한 보호를 사용 하도록 설정 하려면 키 자격 증명 모음에서 키 및 암호를 읽을 수 있는 권한을 Azure Backup 서비스에 부여 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

**암호화 된 vm (BEK만 사용 하 여 암호화 됨)** 에 대 한 보호를 사용 하도록 설정 하려면 키 자격 증명 모음에서 비밀을 읽을 수 있는 Azure Backup 서비스 권한을 제공 해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Azure Government 클라우드를 사용 하는 경우 [AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Cmdlet의 ServicePrincipalName 매개 변수에 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 값을 사용 합니다.
>

## <a name="monitoring-a-backup-job"></a>백업 작업 모니터링

Azure Portal 사용 하지 않고 백업 작업과 같은 장기 실행 작업을 모니터링할 수 있습니다. 진행 중인 작업의 상태를 가져오려면 [AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet을 사용 합니다. 이 cmdlet은 특정 자격 증명 모음에 대 한 백업 작업을 가져오고 해당 자격 증명 모음은 자격 증명 모음 컨텍스트에 지정 됩니다. 다음 예에서는 진행 중인 작업의 상태를 배열로 가져오고 $joblist 변수에 상태를 저장 합니다.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

출력은 다음 예제와 유사 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료 하기 위해 폴링하는 대신 불필요 한 추가 코드 [AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 합니다. 이 cmdlet은 작업이 완료 되거나 지정 된 시간 제한 값에 도달할 때까지 실행을 일시 중지 합니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Azure VM 백업 관리

### <a name="modify-a-protection-policy"></a>보호 정책 수정

보호 정책을 수정 하려면 [AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 를 사용 하 여 SchedulePolicy 또는 보존 정책 개체를 수정 합니다.

#### <a name="modifying-scheduled-time"></a>예약 된 시간 수정

보호 정책을 만들 때 기본적으로 시작 시간이 할당 됩니다. 다음 예에서는 보호 정책의 시작 시간을 수정 하는 방법을 보여 줍니다.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>보존 수정

다음 예에서는 복구 지점 보존 기간을 365 일로 변경 합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>즉시 복원 스냅숏 보존 구성

> [!NOTE]
> Az PS version 1.6.0 부터는 Powershell을 사용 하 여 정책에서 즉시 복원 스냅숏 보존 기간을 업데이트할 수 있습니다.

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

기본값은 2이 고, 사용자는 최소값 1 및 최대값 5를 사용 하 여 값을 설정할 수 있습니다. 주간 백업 정책의 경우 기간은 5로 설정 되며 변경할 수 없습니다.

### <a name="trigger-a-backup"></a>백업 트리거

백업 [-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 를 사용 하 여 백업 작업을 트리거합니다. 초기 백업 인 경우 전체 백업입니다. 후속 백업에서는 증분 복사를 수행 합니다. 다음 예제에서는 60 일 동안 보존 되는 VM 백업을 수행 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

출력은 다음 예제와 유사 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell에서 StartTime 및 EndTime 필드의 표준 시간대는 UTC입니다. 그러나 Azure Portal에서 시간이 표시 되 면 시간은 현지 표준 시간대로 조정 됩니다.
>
>

### <a name="change-policy-for-backup-items"></a>백업 항목에 대 한 정책 변경

사용자는 기존 정책을 수정 하거나 백업 된 항목의 정책을 Policy1에서 Policy2로 변경할 수 있습니다. 백업 항목에 대 한 정책을 전환 하려면 관련 정책을 페치하고 항목을 백업 하 고 백업 항목과 함께 [AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 명령을 매개 변수로 사용 합니다.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

명령은 구성 백업이 완료 될 때까지 대기 하 고 다음 출력을 반환 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

사용자가 보호를 중지 하려는 경우 [AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet을 사용할 수 있습니다. 이렇게 하면 예약 된 백업이 중지 되지만 지금까지 백업 된 데이터는 영구적으로 유지 됩니다.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>백업 데이터 삭제

자격 증명 모음에 저장 된 백업 데이터를 완전히 제거 하려면 '-RemoveRecoveryPoints ' 플래그/스위치를 [' 사용 안 함 ' 보호 명령](#retain-data)에 추가 합니다.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure VM 복원

Azure Portal를 사용 하 여 VM을 복원 하는 것과 PowerShell을 사용 하 여 VM을 복원 하는 것 간에는 중요 한 차이점이 있습니다. PowerShell을 사용 하면 복구 지점에서 디스크 및 구성 정보를 만든 후 복원 작업이 완료 됩니다. 복원 작업에서 가상 컴퓨터를 만들지 않습니다. 디스크에서 가상 컴퓨터를 만들려면 [복원 된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)섹션을 참조 하세요. 전체 VM을 복원 하지 않고 Azure VM 백업에서 몇 개의 파일을 복원 하거나 복구 하려는 경우 [파일 복구 섹션](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)을 참조 하세요.

> [!Tip]
> 복원 작업은 가상 컴퓨터를 만들지 않습니다.
>
>

다음 그림은 RecoveryServicesVault에서 BackupRecoveryPoint에 대 한 개체 계층을 보여 줍니다.

![BackupContainer를 표시 하 Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

백업 데이터를 복원 하려면 백업 항목 및 지정 시간 데이터가 포함 된 복구 지점을 식별 합니다. [AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 를 사용 하 여 자격 증명 모음에서 계정으로 데이터를 복원 합니다.

Azure VM을 복원 하는 기본 단계는 다음과 같습니다.

* VM을 선택 합니다.
* 복구 지점을 선택 합니다.
* 디스크를 복원 합니다.
* 저장 된 디스크에서 VM을 만듭니다.

### <a name="select-the-vm"></a>VM 선택

올바른 백업 항목을 식별 하는 PowerShell 개체를 가져오려면 자격 증명 모음의 컨테이너에서 시작 하 여 개체 계층 구조에서 작업을 수행 합니다. VM을 나타내는 컨테이너를 선택 하려면 [AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet을 사용 하 고 [AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet으로 파이프 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택

[AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용 하 여 백업 항목에 대 한 모든 복구 지점의 목록을 표시 합니다. 그런 다음 복원할 복구 지점을 선택 합니다. 사용할 복구 지점을 확신할 수 없는 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택 하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 지난 7 일간의 선택 된 백업 항목에 대 한 복구 지점의 배열입니다. 배열은 인덱스 0의 최신 복구 지점을 사용 하 여 역순으로 정렬 됩니다. 표준 PowerShell 배열 인덱싱을 사용 하 여 복구 지점을 선택 합니다. 이 예에서 [0]은 최신 복구 지점을 선택 $rp.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

출력은 다음 예제와 유사 합니다.

```output
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

[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet을 사용 하 여 백업 항목의 데이터 및 구성을 복구 지점으로 복원 합니다. 복구 지점을 식별 한 후에는 **-RecoveryPoint** 매개 변수의 값으로 사용 합니다. 위의 샘플에서 **$rp [0]** 은 사용할 복구 지점입니다. 다음 샘플 코드에서 **$rp [0]** 은 디스크를 복원 하는 데 사용할 복구 지점입니다.

디스크 및 구성 정보를 복원 하려면:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Managed disks 복원

> [!NOTE]
> 지원 되는 VM에 관리 디스크를 포함 하는 경우이를 관리 디스크로 복원 하려면 Azure PowerShell RM 모듈 v 6.7.0의 기능을 도입 했습니다. 부터
>
>

**TargetResourceGroupName** 추가 매개 변수를 제공 하 여 관리 디스크를 복원할 RG를 지정 합니다.

> [!NOTE]
> 관리 디스크를 복원 하는 데 **TargetResourceGroupName** 매개 변수를 사용 하는 것이 좋습니다 .이는 상당한 성능 향상이 발생 하기 때문입니다. 또한 Azure Powershell Az module 1.0부터이 매개 변수는 관리 디스크를 사용 하는 복원의 경우 필수입니다.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**Vmconfig** 파일은 저장소 계정에 복원 되 고 관리 디스크는 지정 된 대상 RG로 복원 됩니다.

출력은 다음 예제와 유사 합니다.

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 하 여 복원 작업이 완료 될 때까지 기다립니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

복원 작업이 완료 되 면 [AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 하 여 복원 작업의 세부 정보를 가져옵니다. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 있습니다.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

디스크를 복원한 후 다음 섹션으로 이동 하 여 VM을 만듭니다.

## <a name="replace-disks-in-azure-vm"></a>Azure VM에서 디스크 교체

디스크 및 구성 정보를 교체 하려면 다음 단계를 수행 합니다.

* 1 단계: [디스크 복원](backup-azure-vms-automation.md#restore-the-disks)
* 2 단계: [PowerShell을 사용 하 여 데이터 디스크 분리](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* 3 단계: [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>복원 된 디스크에서 VM 만들기

디스크를 복원한 후 다음 단계를 사용 하 여 디스크에서 가상 컴퓨터를 만들고 구성 합니다.

> [!NOTE]
>
> 1. AzureAz module 3.0.0 이상이 필요 합니다. <br>
> 2. 복원 된 디스크에서 암호화 된 Vm을 만들려면 Azure 역할에 다음 작업을 수행할 수 있는 권한이 있어야 합니다 **. KeyVault/자격 증명 모음/배포/작업** 역할에이 권한이 없는 경우이 작업을 사용 하 여 사용자 지정 역할을 만듭니다. 자세한 내용은 [AZURE RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요. <br>
> 3. 디스크를 복원한 후에는 이제 새 VM을 만드는 데 직접 사용할 수 있는 배포 템플릿을 가져올 수 있습니다. 암호화/암호화 되지 않은 관리/관리 되지 않는 Vm을 만드는 다른 PS cmdlet이 없습니다.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>배포 템플릿을 사용 하 여 VM 만들기

결과 작업 세부 정보는 쿼리 및 배포할 수 있는 템플릿 URI를 제공 합니다.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

템플릿은 고객의 저장소 계정 및 지정 된 컨테이너에 있으므로 직접 액세스할 수 없습니다. 이 템플릿에 액세스 하려면 전체 URL (임시 SAS 토큰과 함께)이 필요 합니다.

1. 먼저 템플릿 Bloburi에서 템플릿 이름을 추출 합니다. 형식은 아래에 설명 되어 있습니다. Powershell에서 split 작업을 사용 하 여이 URL에서 최종 템플릿 이름을 추출할 수 있습니다.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. 그런 다음 [여기](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment)에 설명 된 대로 전체 URL을 생성할 수 있습니다.

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. 템플릿을 배포 하 여 [여기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)에 설명 된 대로 새 VM을 만듭니다.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>구성 파일을 사용 하 여 VM 만들기

다음 섹션에서는 "VMConfig" 파일을 사용 하 여 VM을 만드는 데 필요한 단계를 나열 합니다.

> [!NOTE]
> 위에 설명 된 배포 템플릿을 사용 하 여 VM을 만드는 것이 좋습니다. 이 섹션 (요점 1-6)은 곧 사용 되지 않을 예정입니다.

1. 복원 된 디스크 속성에서 작업 세부 정보를 쿼리 합니다.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Azure 저장소 컨텍스트를 설정 하 고 JSON 구성 파일을 복원 합니다.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. JSON 구성 파일을 사용 하 여 VM 구성을 만듭니다.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. OS 디스크 및 데이터 디스크를 연결 합니다. 이 단계에서는 다양 한 관리 및 암호화 VM 구성에 대 한 예제를 제공 합니다. VM 구성에 적합 한 예제를 사용 합니다.

* **관리** 되지 않거나 암호화 되지 않은 vm-관리 되지 않으며 암호화 되지 않은 vm에 대해 다음 샘플을 사용 합니다.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **AZURE ad를 사용 하는 관리 되지 않는 및 암호화 된 vm (bek만 해당)** -azure ad를 사용 하 여 관리 되지 않는 암호화 된 VM (bek만 사용 하 여 암호화 됨)의 경우, 디스크를 연결 하려면 먼저 키 자격 증명 모음에 암호를 복원 해야 합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화 된 가상 컴퓨터 복원](backup-azure-restore-key-secret.md)을 참조 하세요. 다음 샘플에서는 암호화 된 Vm의 OS 및 데이터 디스크를 연결 하는 방법을 보여 줍니다. OS 디스크를 설정 하는 경우 관련 OS 유형을 언급 해야 합니다.

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

* **AZURE ad (BEK 및 KEK)를 사용 하는 관리 되지 않는 vm 및 암호화 된 vm** -azure AD (BEK 및 KEK를 사용 하 여 암호화 됨)를 사용 하 여 관리 되지 않는 암호화 된 vm의 경우 디스크를 연결 하기 전에 키 및 비밀을 키 자격 증명 모음에 복원 합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화 된 가상 머신 복원](backup-azure-restore-key-secret.md)을 참조 하세요. 다음 샘플에서는 암호화 된 Vm의 OS 및 데이터 디스크를 연결 하는 방법을 보여 줍니다.

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

* **AZURE ad가 없는 관리 되지 않고 암호화 된 vm (bek만 해당)** -azure ad가 없는 관리 되지 않는 암호화 된 VM (bek만 사용 하 여 암호화 됨)의 경우, 원본 키 **자격 증명/암호를 사용할 수 없는** 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 key vault에 비밀을 복원 합니다. 그런 후 다음 스크립트를 실행 하 여 복원 된 OS blob에 대 한 암호화 세부 정보를 설정 합니다 (데이터 blob에는이 단계가 필요 하지 않음). 복원 된 keyVault에서 $dekurl를 가져올 수 있습니다.

아래 스크립트는 원본 키 자격 증명 모음/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

암호를 **사용할 수** 있고 암호화 세부 정보가 OS Blob에도 설정 된 후에는 아래에 지정 된 스크립트를 사용 하 여 디스크를 연결 합니다.

원본 키 자격 증명 모음/비밀을 이미 사용할 수 있는 경우 위의 스크립트를 실행할 필요가 없습니다.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **AZURE ad가 없는 관리 되지 않는 암호화 된 vm (bek 및 KEK)** -azure ad가 없는 관리 되지 않는 암호화 된 VM (bek & KEK를 사용 하 여 암호화 됨)의 경우 원본 키 **자격 증명 모음/키/암호를 사용할 수** 없는 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 키 및 비밀을 key vault에 복원 그런 후 다음 스크립트를 실행 하 여 복원 된 OS blob에 대 한 암호화 세부 정보를 설정 합니다 (데이터 blob에는이 단계가 필요 하지 않음). 복원 된 keyVault에서 $dekurl 및 $kekurl를 가져올 수 있습니다.

아래 스크립트는 원본 키 자격 증명 모음/키/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

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

**키/암호를 사용할 수** 있고 암호화 세부 정보가 OS Blob에 설정 된 후 아래에 지정 된 스크립트를 사용 하 여 디스크를 연결 합니다.

원본 키 자격 증명 모음/키/암호를 사용할 수 있는 경우 위의 스크립트를 실행할 필요가 없습니다.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* 관리 되 **고 암호화 되지 않은 vm** -관리 되는 암호화 되지 않은 vm의 경우 복원 된 관리 디스크를 연결 합니다. 자세한 내용은 [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조 하세요.

* **AZURE ad를 사용 하는 관리 및 암호화 된 vm (bek만 해당)** -azure ad로 관리 되는 암호화 된 VM (bek만 사용 하 여 암호화 됨)에 대해 복원 된 관리 디스크를 연결 자세한 내용은 [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조 하세요.

* **AZURE ad (BEK 및 KEK)로 관리 및 암호화 된 vm** -azure ad를 사용 하 여 관리 되는 암호화 된 VM (BEK 및 KEK를 사용 하 여 암호화 됨)에 대해 복원 된 관리 디스크를 연결 자세한 내용은 [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조 하세요.

* **AZURE ad가 없는 관리 되 고 암호화 된 vm (bek만 해당)** -azure ad가 없는 관리 되 고 암호화 된 VM (bek만 사용 하 여 암호화 됨)의 경우, 원본 키 **자격 증명 모음/암호를 사용할 수 없는** 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 key vault에 비밀을 복원 합니다. 그런 후 다음 스크립트를 실행 하 여 복원 된 OS 디스크에서 암호화 세부 정보를 설정 합니다 (데이터 디스크에는이 단계가 필요 하지 않음). 복원 된 keyVault에서 $dekurl를 가져올 수 있습니다.

아래 스크립트는 원본 키 자격 증명 모음/암호를 사용할 수 없는 경우에만 실행 해야 합니다.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

암호를 사용할 수 있고 암호화 세부 정보가 OS 디스크에 설정 된 후 복원 된 관리 디스크를 연결 하려면 [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조 하세요.

* **AZURE ad가 없는 관리 되 고 암호화 된 vm (bek 및 KEK)** -azure ad가 없는 관리 되 고 암호화 된 VM (bek & KEK를 사용 하 여 암호화 됨)의 경우 원본 키 **자격 증명 모음/키/암호를 사용할 수** 없는 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 키 및 비밀을 key vault에 복원 그런 후 다음 스크립트를 실행 하 여 복원 된 OS 디스크에서 암호화 세부 정보를 설정 합니다 (데이터 디스크에는이 단계가 필요 하지 않음). 복원 된 keyVault에서 $dekurl 및 $kekurl를 가져올 수 있습니다.

아래 스크립트는 원본 키 자격 증명 모음/키/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

키/암호를 사용할 수 있고 암호화 세부 정보가 OS 디스크에 설정 된 후 복원 된 관리 디스크를 연결 하려면 [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조 하세요.

5. 네트워크 설정을 설정 합니다.

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

6. 가상 컴퓨터를 만듭니다.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. 푸시 ADE 확장.
   ADE 확장이 푸시되 지 않으면 데이터 디스크가 암호화 되지 않은 것으로 표시 되므로 아래 단계를 실행 하는 것이 필수입니다.

   * **AZURE AD를 사용 하는 VM의 경우** -다음 명령을 사용 하 여 데이터 디스크에 대 한 암호화를 수동으로 사용 하도록 설정 합니다.  

     **BEK만**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **AZURE AD를 사용 하지 않는 VM의 경우** -다음 명령을 사용 하 여 데이터 디스크에 대 한 암호화를 수동으로 사용 하도록 설정 합니다.

     명령 실행 중에 AADClientID를 요청 하는 경우에는 Azure PowerShell를 업데이트 해야 합니다.

     **BEK만**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> 암호화 된 VM 복원 디스크 프로세스의 일부로 만든 JASON 파일을 수동으로 삭제 해야 합니다.

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM 백업에서 파일 복원

디스크를 복원 하는 것 외에도 Azure VM 백업에서 개별 파일을 복원할 수 있습니다. 파일 복원 기능은 복구 지점의 모든 파일에 대 한 액세스를 제공 합니다. 일반 파일과 같이 파일 탐색기를 통해 파일을 관리 합니다.

Azure VM 백업에서 파일을 복원 하는 기본 단계는 다음과 같습니다.

* VM 선택
* 복구 지점 선택
* 복구 지점의 디스크 탑재
* 필요한 파일 복사
* 디스크 분리

### <a name="select-the-vm"></a>VM 선택

올바른 백업 항목을 식별 하는 PowerShell 개체를 가져오려면 자격 증명 모음의 컨테이너에서 시작 하 여 개체 계층 구조에서 작업을 수행 합니다. VM을 나타내는 컨테이너를 선택 하려면 [AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet을 사용 하 고 [AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet으로 파이프 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>복구 지점 선택

[AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용 하 여 백업 항목에 대 한 모든 복구 지점의 목록을 표시 합니다. 그런 다음 복원할 복구 지점을 선택 합니다. 사용할 복구 지점을 확신할 수 없는 경우 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택 하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 지난 7 일간의 선택 된 백업 항목에 대 한 복구 지점의 배열입니다. 배열은 인덱스 0의 최신 복구 지점을 사용 하 여 역순으로 정렬 됩니다. 표준 PowerShell 배열 인덱싱을 사용 하 여 복구 지점을 선택 합니다. 이 예에서 [0]은 최신 복구 지점을 선택 $rp.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

출력은 다음 예제와 유사 합니다.

```output
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

[AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) cmdlet을 사용 하 여 복구 지점의 모든 디스크를 탑재 하는 스크립트를 가져옵니다.

> [!NOTE]
> 디스크는 스크립트가 실행 되는 컴퓨터에 iSCSI 연결 디스크로 탑재 됩니다. 탑재는 즉시 발생 하며 요금이 발생 하지 않습니다.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

출력은 다음 예제와 유사 합니다.

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

파일을 복구 하려는 컴퓨터에서 스크립트를 실행 합니다. 스크립트를 실행 하려면 제공 된 암호를 입력 해야 합니다. 디스크가 연결 되 면 Windows 파일 탐색기를 사용 하 여 새 볼륨 및 파일을 검색 합니다. 자세한 내용은 Backup 문서 [Azure virtual machine 백업에서 파일 복구](backup-azure-restore-files-from-vm.md)를 참조 하세요.

### <a name="unmount-the-disks"></a>디스크 분리

필요한 파일이 복사 된 후 [AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) 를 사용 하 여 디스크를 분리 합니다. 복구 지점의 파일에 대 한 액세스가 제거 되도록 디스크를 분리 해야 합니다.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용 하 여 Azure 리소스에 참여 하려면 PowerShell 문서 [Windows Server에 대 한 백업 배포 및 관리](backup-client-automation.md)를 참조 하세요. DPM 백업을 관리 하는 경우 [dpm에 대 한 백업 배포 및 관리](backup-dpm-automation.md)문서를 참조 하세요.
