---
title: PowerShell을 통해 Azure 파일 백업
description: 이 문서에서는 Azure 백업 서비스 및 PowerShell을 사용하여 Azure 파일을 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273541"
---
# <a name="back-up-azure-files-with-powershell"></a>PowerShell을 통해 Azure 파일 백업

이 문서에서는 Azure PowerShell을 사용하여 Azure [백업](backup-overview.md) 복구 서비스 자격 증명 모음을 사용하여 Azure 파일 파일 공유를 백업하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정하고 Azure 복구 서비스 공급자를 등록합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure 파일 공유에 대한 백업을 구성합니다.
> * 백업 작업을 실행합니다.

## <a name="before-you-start"></a>시작하기 전에

* 복구 서비스 자격 증명 모음에 대해 [자세히 알아보세요.](backup-azure-recovery-services-vault-overview.md)
* [Azure 파일 공유를 백업하기](backup-afs.md)위한 미리 보기 기능에 대해 읽어보십시오.
* 복구 서비스에 대한 PowerShell 개체 계층 구조를 검토합니다.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az.RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) 참조를 검토합니다.

## <a name="set-up-and-install"></a>설정 및 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음과 같이 PowerShell을 설정합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.0.0입니다.

> [!WARNING]
> 미리 보기에 필요한 PS의 최소 버전은 'Az 1.0.0'이었습니다. GA에 대한 향후 변경으로 인해 필요한 최소 PS 버전은 'Az.RecoveryServices 2.6.0'입니다. 이 버전으로 모든 기존 PS 버전을 업그레이드하는 것이 매우 중요합니다. 그렇지 않으면 기존 스크립트가 GA 후에 중단됩니다. 다음 PS 명령으로 최소 버전 설치

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 이 명령을 통해 Azure 백업 PowerShell cmdlet 찾기:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure 백업, Azure 사이트 복구 및 복구 서비스 자격 증명 모음에 대한 별칭 및 cmdlet을 검토합니다. 다음은 표시되는 내용의 예입니다. 그것은 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다.
5. 표시되는 웹 페이지에서 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

    * 또는 **-Credential이**있는 **Connect-AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테넌트를 대신하여 작업하는 CSP 파트너인 경우 테넌트ID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

6. 계정에 여러 구독이 있을 수 있으므로 계정에 사용할 구독을 연결합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록되었는지 확인합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 **RegisterState가** **등록된**으로 변경되는지 확인합니다. 그렇지 않으면 **Register-AzResourceProvider** cmdlet을 다시 실행합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

1. 볼트는 리소스 그룹에 배치됩니다. 기존 리소스 그룹이 없는 경우 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)을 사용하여 새 리소스 그룹을 만듭니다. 이 예제에서는 미국 서부 지역에 새 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. 새 [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet을 사용하여 볼트를 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 볼트 저장소에 사용할 중복 유형을 지정합니다.

   * [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
   * 다음 예제는 **-BackupStorageRedundancy** 설정에 대 한[설정-AzRecoveryServicesBackup속성](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 에 대 한 **테스트 볼트에** 대 한 **cmd 지오Redundant로**설정 합니다.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사합니다. 관련 리소스 그룹 및 위치가 제공됩니다.

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

볼트 개체를 변수에 저장하고 볼트 컨텍스트를 설정합니다.

* 대부분의 Azure 백업 cmdlet에는 복구 서비스 볼트 개체가 입력으로 필요하므로 볼트 개체를 변수에 저장하는 것이 편리합니다.
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [설정-AzRecovery서비스볼트컨텍스트로](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)설정합니다. 컨텍스트를 설정한 후 모든 후속 cmdlet에 적용됩니다.

다음 예제에서는 **testvault**에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>볼트 ID 가져오기

Azure PowerShell 지침에 따라 볼트 컨텍스트 설정을 더 이상 사용되지 않습니다. 대신 볼트 ID를 저장하거나 가져와 관련 명령으로 전달할 수 있습니다. 따라서 볼트 컨텍스트를 설정하지 않았거나 특정 볼트에 대해 실행할 명령을 지정하려면 다음과 같이 볼트 ID를 "-vaultID"로 모든 관련 명령에 전달합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정과 백업 복구 지점을 유지해야 하는 기간등을 지정합니다.

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* [Get-AzRecoveryServices백업보존정책개체를](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)사용하여 기본 백업 정책 보존을 봅니다.
* [Get-AzRecoveryServicesBackupPolicyPolicyObject를](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)사용하여 기본 백업 정책 일정을 봅니다.
* [새 AzRecoveryServices보호정책](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet을 사용하여 새 백업 정책을 만듭니다. 일정 및 보존 정책 개체를 입력합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의되어 있습니다. 다음 예제를 사용하여 시작 시간을 원하는 시작 시간으로 변경합니다. 원하는 시작 시간도 UTC에 있어야 합니다. 아래 예제에서는 원하는 시작 시간이 일일 백업의 경우 01:00 AM UTC라고 가정합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 시작 시간은 30분 배수로만 제공해야합니다. 위의 예에서는 "01:00:00" 또는 "02:30:00"일 수 있습니다. 시작 시간은 "01:15:00"이 될 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 이러한 변수를 새**정책(NewAFSPolicy)의**매개 변수로 사용합니다. **NewAFSPolicy**는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 유사합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>백업 사용

백업 정책을 정의한 후 정책을 사용하여 Azure 파일 공유에 대한 보호를 활성화할 수 있습니다.

### <a name="retrieve-a-backup-policy"></a>백업 정책 검색

[Get-AzRecoveryServices백업보호정책을](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)사용하통해 관련 정책 개체를 가져옵니다. 이 cmdlet을 사용하여 특정 정책을 얻거나 워크로드 유형과 연결된 정책을 볼 수 있습니다.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>워크로드 유형에 대한 정책 검색

다음 예제는 워크로드 유형 AzureFiles 에 대한 정책을 **검색합니다.**

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

출력은 다음과 유사합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 **BackupTime** 필드의 표준 시간대는UTC(협정 세계시)입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

### <a name="retrieve-a-specific-policy"></a>특정 정책 검색

다음 정책은 **dailyafs**라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>백업 활성화 및 정책 적용

[사용-AzRecovery서비스백업](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)보호 를 사용하여 보호를 활성화합니다. 정책이 볼트와 연결되면 정책 일정에 따라 백업이 트리거됩니다.

다음 예제에서는 **Azure**파일 공유 **testAzureFileShare** 저장소 계정 **testStorageAcct에**대 한 보호를 사용할 수 있습니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

명령은 보호 구성 작업을 마칠 때까지 대기하며 다음과 같은 출력을 제공합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>중요 공지 - AFS 백업을 위한 백업 항목 식별

이 섹션에서는 GA에 대비하여 AFS 백업의 중요한 변경 에 대해 간략하게 설명합니다.

AFS에 대한 백업을 사용하도록 설정하는 동안 사용자는 엔터티 이름과 백업 항목이 생성될 때 고객 친화적인 파일 공유 이름을 제공합니다. 백업 항목의 '이름'은 Azure Backup 서비스에서 만든 고유 식별자입니다. 일반적으로 식별자는 사용자 친화적인 이름을 포함합니다. 그러나 파일 공유를 삭제하고 동일한 이름으로 다른 파일 공유를 만들 수 있는 소프트 삭제의 중요한 시나리오를 처리하기 위해 Azure 파일 공유의 고유 ID는 이제 고객 친화적인 이름 대신 ID가 됩니다. 각 항목의 고유한 ID/이름을 알기 위해 backupManagementType 및 WorkloadType에 대한 관련 필터를 사용하여 ```Get-AzRecoveryServicesBackupItem``` 명령을 실행하여 모든 관련 항목을 가져옵니다. 항상 항목을 나열한 다음 응답의 'name' 필드에서 고유한 이름을 검색하는 것이 좋습니다. 이 값을 사용하여 'Name' 매개 변수로 항목을 필터링합니다. 그렇지 않으면 FriendlyName 매개 변수를 사용하여 고객 친화적인 이름/식별자로 항목을 검색합니다.

> [!WARNING]
> PS 버전이 AFS 백업의 경우 'Az.RecoveryServices 2.6.0'의 최소 버전으로 업그레이드되었는지 확인합니다. 이 버전에서는 명령에 'friendlyName' 필터를 ```Get-AzRecoveryServicesBackupItem``` 사용할 수 있습니다. Azure 파일 공유 이름을 friendlyName 매개 변수에 전달합니다. Azure 파일 공유 이름을 'Name' 매개 변수로 전달하는 경우 이 버전은 이 친숙한 이름을 친숙한 이름 매개 변수로 전달하는 경고를 throw합니다. 이 최소 버전을 설치하지 않으면 기존 스크립트가 실패할 수 있습니다. 다음 명령으로 최소 버전의 PS를 설치합니다.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

[Backup-AzRecoveryServicesBackupItem을](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) 사용하여 보호된 Azure 파일 공유에 대한 온디맨드 백업을 실행합니다.

1. [Get-AzRecoveryServicesBackupContainer를](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)사용하여 백업 데이터를 보유하는 볼트의 컨테이너에서 저장소 계정을 검색합니다.
2. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)을 사용하여 Azure 파일 공유에 대한 정보를 얻습니다.
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)을 사용하여 주문형 백업을 실행합니다.

다음과 같이 주문형 백업을 실행합니다.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

백업을 수행하는 동안 Azure 파일 공유 스냅샷이 사용되기 때문에 일반적으로 명령에 이 출력이 반환될 때까지 작업이 완료됩니다.

### <a name="using-on-demand-backups-to-extend-retention"></a>온디맨드 백업을 사용하여 보존 기간 연장

주문형 백업을 사용하여 10년 동안 스냅샷을 유지할 수 있습니다. 스케줄러를 사용하여 선택한 보존을 사용하여 주문형 PowerShell 스크립트를 실행하고 매주, 매월 또는 매년 정기적으로 스냅샷을 만들 수 있습니다. 일반 스냅숏을 사용하는 동안 Azure 백업을 사용하는 [온디맨드 백업의 제한 사항은](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) 참조하십시오.

샘플 스크립트를 찾고 있는 경우 Azure Automation 실행책을 사용하여 GitHub()<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>의 샘플 스크립트를 참조하여 주기적으로 백업을 예약하고 최대 10년까지 유지할 수 있습니다.

> [!WARNING]
> PS 버전이 자동화 런북의 AFS 백업을 위해 'Az.RecoveryServices 2.6.0'의 최소 버전으로 업그레이드되었는지 확인합니다. 이전 'AzureRM' 모듈을 'Az' 모듈로 교체해야 합니다. 이 버전에서는 명령에 'friendlyName' 필터를 ```Get-AzRecoveryServicesBackupItem``` 사용할 수 있습니다. azure 파일 공유 이름을 friendlyName 매개 변수에 전달합니다. Azure 파일 공유 이름을 'Name' 매개 변수로 전달하는 경우 이 버전은 이 친숙한 이름을 친숙한 이름 매개 변수로 전달하는 경고를 throw합니다.

## <a name="next-steps"></a>다음 단계

Azure 포털에서 Azure 파일 백업에 [대해 자세히 알아보세요.](backup-afs.md)
