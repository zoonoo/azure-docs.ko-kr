---
title: PowerShell을 사용 하 여 Azure 파일 공유 백업
description: 이 문서에서는 Azure Backup 서비스와 PowerShell을 사용 하 여 Azure Files 파일 공유를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 53187152802908e94ee4a8a231d3b7874cf42422
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199348"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>PowerShell을 사용 하 여 Azure 파일 공유 백업

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 통해 Azure Files 파일 공유를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정 하 고 Recovery Services 공급자를 등록 합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure 파일 공유에 대 한 백업을 구성 합니다.
> * 백업 작업을 실행 합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md) .
* Azure 라이브러리에서 Az. RecoveryServices [cmdlet 참조](/powershell/module/az.recoveryservices) 참조를 검토 합니다.
* Recovery Services에 대 한 다음 PowerShell 개체 계층 구조를 검토 합니다.

  ![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>PowerShell 설정

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 다음과 같이 설정 합니다.

1. [최신 버전의 Azure PowerShell을 다운로드](/powershell/azure/install-az-ps)합니다.

    > [!NOTE]
    > Azure 파일 공유를 백업 하는 데 필요한 최소 PowerShell 버전은 Az. RecoveryServices 2.6.0입니다. 최신 버전을 사용 하거나 최소한 최소 버전을 사용 하면 기존 스크립트 문제를 방지할 수 있습니다. 다음 PowerShell 명령을 사용 하 여 최소 버전을 설치 합니다.
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 다음 명령을 사용 하 여 Azure Backup에 대 한 PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음에 대 한 별칭 및 cmdlet을 검토 합니다. 다음은 표시 될 수 있는 항목의 예입니다. Cmdlet의 전체 목록은 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다.
5. 표시 되는 웹 페이지에 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

    또는 **-Credential**을 사용 하 여 **AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
   
    테 넌 트를 대신 하 여 작업 중인 CSP 파트너인 경우 고객을 테 넌 트로 지정 합니다. 테 넌 트 ID 또는 테 넌 트 주 도메인 이름을 사용 합니다. 예를 들면 **AzAccount-Tenant "fabrikam.com"** 입니다.

6. 계정에 여러 구독이 있을 수 있으므로 사용 하려는 구독을 계정과 연결 합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup를 처음으로 사용 하는 경우 **AzResourceProvider** cmdlet을 사용 하 여 Azure Recovery Services 공급자를 구독에 등록 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록 되었는지 확인 합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 **Registrationstate** 가 **등록**됨으로 변경 되었는지 확인 합니다. 그렇지 않은 경우 **AzResourceProvider** cmdlet을 다시 실행 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 리소스 관리자 리소스 이므로 리소스 그룹에 저장 해야 합니다. 기존 리소스 그룹을 사용 하거나 **AzResourceGroup** cmdlet을 사용 하 여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 해당 그룹의 이름과 위치를 지정 합니다.

Recovery Services 자격 증명 모음을 만들려면 다음 단계를 따르세요.

1. 기존 리소스 그룹이 없는 경우 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) cmdlet을 사용 하 여 새 리소스 그룹을 만듭니다. 이 예제에서는 미국 서 부 지역에 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet을 사용 하 여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용한 자격 증명 모음과 동일한 위치를 지정 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 자격 증명 모음 저장소에 사용할 중복성 유형을 지정 합니다. [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
   
   다음 예에서는 **testvault 된** 의 [AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmdlet에 대 한 **-BackupStorageRedundancy** 옵션을 **GeoRedundant**로 설정 합니다.

   ```powershell
   $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
   Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
   ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용 합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사 합니다. 출력은 연결 된 리소스 그룹 및 위치를 제공 합니다.

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

많은 Azure Backup cmdlet에는 입력으로 Recovery Services 자격 증명 모음 개체가 필요 하므로 자격 증명 모음 개체를 변수에 저장 하는 것이 편리 합니다.

자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)를 사용 하 여 설정 합니다. 컨텍스트를 설정한 후에는 모든 후속 cmdlet에 적용 됩니다.

다음 예에서는 **testvault 된**에 대 한 자격 증명 모음 컨텍스트를 설정 합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 가져옵니다.

Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 사용 중단 계획 합니다. 대신 자격 증명 모음 ID를 저장 하거나 인출 하 고 관련 명령에 전달할 수 있습니다. 자격 증명 모음 컨텍스트를 설정 하지 않았거나 특정 자격 증명 모음에 대해 실행할 명령을 지정 하려는 경우 다음과 같이 자격 증명 모음 ID를 `-vaultID` 모든 관련 명령에 전달 합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정 및 백업 복구 지점이 유지 되는 기간을 지정 합니다.

백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다. 매일, 매주, 매월 또는 매년 보존을 사용 하 여 백업을 구성할 수 있습니다.

다음은 백업 정책에 대 한 몇 가지 cmdlet입니다.

* [AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)를 사용 하 여 기본 백업 정책 보존을 확인 합니다.
* [AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)를 사용 하 여 기본 백업 정책 일정을 확인 합니다.
* [AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)를 사용 하 여 새 백업 정책을 만듭니다. 일정 및 보존 정책 개체를 입력으로 입력 합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 되어 있습니다. 다음 예를 사용 하 여 시작 시간을 원하는 시작 시간으로 변경할 수 있습니다. 원하는 시작 시간은 UTC (협정 세계시)로 지정 해야 합니다. 이 예제에서는 원하는 시작 시간이 매일 백업의 경우 01:00 AM UTC 라고 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30 분의 배수로 시작 시간을 제공 해야 합니다. 앞의 예제에서는 "01:00:00" 또는 "02:30:00"만 사용할 수 있습니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수를 새 정책에 대 한 매개 변수로 사용 합니다 (**Newafspolicy**). **NewAFSPolicy**는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

다음과 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>백업 사용

백업 정책을 정의한 후에 정책을 사용 하 여 Azure 파일 공유에 대 한 보호를 사용 하도록 설정할 수 있습니다.

### <a name="retrieve-a-backup-policy"></a>백업 정책 검색

[AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)를 사용 하 여 관련 정책 개체를 가져옵니다. 이 cmdlet을 사용 하 여 작업 유형과 연결 된 정책을 보거나 특정 정책을 가져올 수 있습니다.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>작업 유형에 대 한 정책 검색

다음 예에서는 **Azurefiles**작업 유형에 대 한 정책을 검색 합니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

다음과 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 **Backuptime** 시간 필드의 표준 시간대는 UTC입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

#### <a name="retrieve-a-specific-policy"></a>특정 정책 검색

다음 정책은 **dailyafs**이라는 백업 정책을 검색 합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>보호를 사용 하도록 설정 하 고 정책을 적용 합니다.

[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)를 사용 하 여 보호를 사용 하도록 설정 합니다. 정책이 자격 증명 모음과 연결 된 후 백업은 정책 일정에 따라 트리거됩니다.

다음 예제에서는 정책 **dailyafs**를 사용 하 여 저장소 계정 **Testazurefileshare**에서 Azure 파일 공유 **testazurefileshare** 공유에 대 한 보호를 사용 하도록 설정 합니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

명령은 구성 보호 작업이 완료 될 때까지 대기 하 고 다음 예제와 비슷한 출력을 제공 합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice-backup-item-identification"></a>중요 알림: 백업 항목 id

이 섹션에서는 일반 공급을 준비 하기 위해 Azure 파일 공유 백업의 중요 한 변경 사항에 대해 간략하게 설명 합니다.

Azure 파일 공유에 대 한 백업을 사용 하도록 설정 하는 동안 사용자는 고객에 게 엔터티 이름으로 파일 공유 이름을 제공 하 고 백업 항목을 만듭니다. 백업 항목의 이름은 Azure Backup 서비스에서 만드는 고유 식별자입니다. 일반적으로 식별자는 사용자에 게 친숙 한 이름입니다. 하지만 파일 공유를 삭제할 수 있고 같은 이름으로 다른 파일 공유를 만들 수 있는 일시 삭제 시나리오를 처리 하려면 이제 Azure 파일 공유의 고유 id가 ID입니다. 

각 항목의 고유 ID를 확인 하려면 **Backupmanagementtype** 및 **WorkloadType** 에 대 한 관련 필터를 사용 하 여 **AzRecoveryServicesBackupItem** 명령을 실행 하 여 모든 관련 항목을 가져옵니다. 그런 다음 반환 된 PowerShell 개체/응답에서 이름 필드를 확인 합니다. 

항목을 나열 하 고 응답의 이름 필드에서 고유한 이름을 검색 하는 것이 좋습니다. *Name* 매개 변수를 사용 하 여 항목을 필터링 하려면이 값을 사용 합니다. 그렇지 않으면 *FriendlyName* 매개 변수를 사용 하 여 해당 ID를 가진 항목을 검색 합니다.

> [!IMPORTANT]
> Azure 파일 공유 백업에 대 한 PowerShell이 최소 버전 (Az. RecoveryServices 2.6.0)으로 업그레이드 되었는지 확인 합니다. 이 버전을 사용 하면 **AzRecoveryServicesBackupItem** 명령에 *FriendlyName* 필터를 사용할 수 있습니다. 
>
> Azure 파일 공유의 이름을 *FriendlyName* 매개 변수에 전달 합니다. 파일 공유의 이름을 *name* 매개 변수에 전달 하는 경우이 버전은 경고를 throw 하 여 이름을 *FriendlyName* 매개 변수에 전달 합니다. 
>
> 최소 버전을 설치 하지 않으면 기존 스크립트에 오류가 발생할 수 있습니다. 다음 명령을 사용 하 여 PowerShell의 최소 버전을 설치 합니다.
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) 를 사용 하 여 보호 된 Azure 파일 공유에 대 한 주문형 백업을 실행 합니다.

1. [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)를 사용 하 여 백업 데이터를 보관 하는 자격 증명 모음의 컨테이너에서 저장소 계정을 검색 합니다.
2. 백업 작업을 시작 하려면 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)를 사용 하 여 Azure 파일 공유에 대 한 정보를 가져옵니다.
3. [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)를 사용 하 여 요청 시 백업을 실행 합니다.

다음과 같이 주문형 백업을 실행 합니다.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

이 명령은 다음 예제와 같이 추적할 ID가 있는 작업을 반환 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

백업을 수행 하는 동안 Azure 파일 공유 스냅숏이 사용 됩니다. 일반적으로이 작업은 명령에서이 출력을 반환 하는 시간을 기준으로 완료 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure Files를 백업 하는](backup-afs.md)방법에 대해 알아봅니다.
- Azure Automation runbook을 사용 하 여 백업을 예약 하려면 [GitHub의 샘플 스크립트](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) 를 참조 하세요.
