---
title: Azure VM 백업의 SQL DB는 PowerShell을 통해 복원할 & 있습니다.
description: Azure 백업 및 PowerShell을 사용하여 Azure VM에서 SQL 데이터베이스를 백업하고 복원합니다.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131823"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>PowerShell을 사용하여 Azure VM에서 SQL 데이터베이스를 백업하고 복원합니다.

이 문서에서는 Azure PowerShell을 사용하여 Azure [백업](backup-overview.md) 복구 서비스 자격 증명 모음을 사용하여 Azure VM 내에서 SQL DB를 백업하고 복구하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정하고 Azure 복구 서비스 공급자를 등록합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure VM 내에서 SQL DB에 대한 백업을 구성합니다.
> * 백업 작업을 실행합니다.
> * 백업된 SQL DB를 복원합니다.
> * 백업 을 모니터링하고 작업을 복원합니다.

## <a name="before-you-start"></a>시작하기 전에

* 복구 서비스 자격 증명 모음에 대해 [자세히 알아보세요.](backup-azure-recovery-services-vault-overview.md)
* [Azure VM 내에서 SQL DB를 백업하기](backup-azure-sql-database.md#before-you-start)위한 기능 기능에 대해 읽어보십시오.
* 복구 서비스에 대한 PowerShell 개체 계층 구조를 검토합니다.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az.RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) 참조를 검토합니다.

### <a name="set-up-and-install"></a>설정 및 설치

다음과 같이 PowerShell을 설정합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.5.0입니다.

2. 이 명령을 통해 Azure 백업 PowerShell cmdlet 찾기:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure 백업 및 복구 서비스 자격 증명 모음에 대한 별칭 및 cmdlet을 검토합니다. 다음은 표시되는 내용의 예입니다. 그것은 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다.
5. 표시되는 웹 페이지에서 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

    * 또는 **-Credential이**있는 **Connect-AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테넌트에서 작업하는 CSP 파트너인 경우 테넌트ID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

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

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

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
    * 다음 예제는 **-BackupStorageRedundancy** 설정에 대 한[설정-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 지 거 대 한 **설정에** 대 한 속성 cmd **지오레디던트로**설정 합니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사합니다. 연결된 리소스 그룹 및 위치가 제공됩니다.

```output
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

Azure PowerShell 지침에 따라 볼트 컨텍스트 설정을 더 이상 사용되지 않습니다. 대신 다음과 같이 vault ID를 저장하거나 가져와 관련 명령으로 전달할 수 있습니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정과 백업 복구 지점을 유지해야 하는 기간등을 지정합니다.

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* [Get-AzRecoveryServices백업보존정책개체를](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)사용하여 기본 백업 정책 보존을 봅니다.
* [Get-AzRecoveryServicesBackupPolicyPolicyObject를](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)사용하여 기본 백업 정책 일정을 봅니다.
* [새 AzRecoveryServices보호정책](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet을 사용하여 새 백업 정책을 만듭니다. 일정 및 보존 정책 개체를 입력합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의되어 있습니다. 다음 예제를 사용하여 시작 시간을 원하는 시작 시간으로 변경합니다. 원하는 시작 시간도 UTC에 있어야 합니다. 아래 예제에서는 원하는 시작 시간이 일일 백업의 경우 01:00 AM UTC라고 가정합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 시작 시간은 30분 배수로만 제공해야합니다. 위의 예에서는 "01:00:00" 또는 "02:30:00"일 수 있습니다. 시작 시간은 "01:15:00"이 될 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 이러한 변수를 새**정책(NewSQLPolicy)의**매개 변수로 사용합니다. **NewSQLPolicy는** 매일 "전체"백업을 소요, 180 일 동안 유지하고 2 시간마다 로그 백업소요

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 유사합니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>백업 사용

### <a name="registering-the-sql-vm"></a>SQL VM 등록

Azure VM 백업 및 Azure File 공유의 경우 백업 서비스는 이러한 Azure 리소스 관리자 리소스에 연결하고 관련 세부 정보를 가져올 수 있습니다. SQL은 Azure VM 내의 응용 프로그램이므로 Backup 서비스에는 응용 프로그램에 액세스하고 필요한 세부 정보를 가져올 수 있는 권한이 필요합니다. 이렇게 하려면 복구 서비스 자격 증명 모음을 사용 하 여 SQL 응용 프로그램을 포함 하는 Azure VM을 *'등록'* 해야 합니다. 볼트에 SQL VM을 등록하면 해당 볼트에만 SQL B를 보호할 수 있습니다. [레지스터-AzRecoveryServices백업컨테이너](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet을 사용하여 VM을 등록합니다.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

명령은 이 리소스의 '백업 컨테이너'를 반환하고 상태는 '등록'됩니다.

> [!NOTE]
> force 매개 변수가 지정되지 않으면 '이 컨테이너에 대한 보호를 비활성화하시겠습니까'라는 텍스트로 확인하라는 메시지가 표시됩니다. 확인하려면 이 텍스트를 무시하고 "Y"라고 말하십시오. 이것은 알려진 문제이며 힘 매개 변수에 대한 텍스트 및 요구 사항을 제거하기 위해 노력하고 있습니다.

### <a name="fetching-sql-dbs"></a>SQL DB 가져오기

등록이 완료되면 Backup 서비스는 VM 내에서 사용 가능한 모든 SQL 구성 요소를 나열할 수 있습니다. 이 볼트에 백업할 모든 SQL 구성 요소를 보려면 [Get-AzRecoveryServices보호 가능 항목](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet을 사용합니다.

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

출력에는 항목 유형 및 ServerName을 사용하여 이 자격 증명 모음에 등록된 모든 SQL VM에서 보호되지 않은 모든 SQL 구성 요소가 표시됩니다. '-컨테이너' 매개 변수를 전달하여 특정 SQL VM을 추가로 필터링하거나 ItemType 플래그와 함께 '이름' 및 'ServerName'의 조합을 사용하여 고유한 SQL 항목에 도달할 수 있습니다.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>백업 구성

필요한 SQL DB와 백업해야 하는 정책이 있으므로 [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet을 사용하여 이 SQL DB에 대한 백업을 구성할 수 있습니다.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

명령은 구성 백업이 완료될 때까지 대기하고 다음 출력을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>새 SQL DB 가져오기

컴퓨터가 등록되면 백업 서비스는 사용 가능한 DB의 세부 정보를 가져옵니다. 사용자가 나중에 등록된 컴퓨터에 SQL DB/SQL 인스턴스를 추가하는 경우 백업 서비스를 수동으로 트리거하여 보호되지 않은 모든 DB(새로 추가된 DB 포함)를 다시 얻으려면 새 '조회'를 수행해야 합니다. SQL VM에서 [초기화-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet을 사용하여 새로운 조회를 수행합니다. 명령은 작업이 완료될 때까지 기다립니다. 나중에 [Get-AzRecoveryServices보호 가능항목](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet을 사용하여 보호되지 않은 최신 SQL 구성 요소 목록을 가져옵니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

관련 보호 가능한 항목을 가져오면 [위의 섹션에서](#configuring-backup)지시한 대로 백업을 사용하도록 설정합니다.
새 DB를 수동으로 감지하지 않으려면 [아래에](#enable-autoprotection)설명된 대로 자동 보호를 선택할 수 있습니다.

## <a name="enable-autoprotection"></a>자동 보호 사용

사용자는 나중에 추가된 모든 DB가 특정 정책으로 자동으로 보호되도록 백업을 구성할 수 있습니다. 자동 보호를 사용하려면 [사용-AzRecovery서비스백업자동보호](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet을 사용하십시오.

명령은 이후의 모든 DB를 백업하는 것이므로 작업은 SQLInstance 수준에서 수행됩니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

자동 보호 의도가 주어지면 새로 추가된 DB를 가져오기 위해 컴퓨터에 대한 조회는 8시간마다 예약된 백그라운드 작업으로 수행됩니다.

## <a name="restore-sql-dbs"></a>SQL B 복원

Azure Backup은 다음과 같이 Azure VM에서 실행 중인 SQL Server 데이터베이스를 복원할 수 있습니다.

* 트랜잭션 로그 백업을 사용하여 특정 날짜 또는 시간(두 번째)으로 복원합니다. Azure Backup은 선택한 시간에 따라 복원하는 데 필요한 적절한 전체 차동 백업 및 로그 백업 체인을 자동으로 결정합니다.
* 특정 전체 또는 차등 백업을 복원하여 특정 복구 지점으로 복원합니다.

SQL DB를 복원하기 전에 [여기에](restore-sql-database-azure-vm.md#prerequisites) 언급된 필수 구성 조건을 확인하십시오.

먼저 [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet을 사용하여 관련 백업 된 SQL DB를 가져옵니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>관련 복원 시간 가져오기

위에서 설명한 대로 사용자는 백업된 SQL DB를 전체/차동 **복사본또는** 로그 시점 까지 복원할 수 있습니다.

#### <a name="fetch-distinct-recovery-points"></a>고유한 복구 지점 가져오기

[Get-AzRecoveryServicesBackupRecoveryPoint를](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) 사용하여 백업된 SQL DB에 대해 고유한(전체/차등) 복구 지점을 가져옵니다.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

출력은 다음 예제와 유사합니다.

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

'RecoveryPointId' 필터 또는 배열 필터를 사용하여 관련 복구 지점을 가져옵니다.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>시점 복구 지점 가져오기

사용자가 DB를 특정 시점에 복원하려는 경우 [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet을 사용합니다. cmdlet은 해당 SQL 백업 항목에 대한 끊어지지 않은 연속 로그 체인의 시작 및 종료 시간을 나타내는 날짜 목록을 반환합니다. 원하는 시점이 이 범위 내에 있어야 합니다.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

출력은 다음 예제와 유사합니다.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

위의 출력은 사용자가 표시된 시작 시간과 종료 시간 사이의 모든 시점에 복원할 수 있음을 의미합니다. 시간은 UTC에 있습니다. 위에 표시된 범위 내에 있는 PS에서 모든 시점-인-씬을 구성합니다.

> [!NOTE]
> 복원을 위해 로그 시점 인시(point-in-in-time)를 선택한 경우 사용자는 DB가 복원되는 전체 백업과 같은 시작 지점을 지정할 필요가 없습니다. Azure Backup 서비스는 전체 복구 계획, 즉 선택할 전체 백업, 적용할 로그 백업 등을 처리합니다.

### <a name="determine-recovery-configuration"></a>복구 구성 결정

SQL DB 복원의 경우 다음 복원 시나리오가 지원됩니다.

* 백업된 SQL DB에 다른 복구 지점의 데이터 재정의 - OriginalWorkloadRestore
* 동일한 SQL 인스턴스에서 SQL DB를 새 DB로 복원 - AlternateWorkloadRestore
* 다른 SQL VM에서 다른 SQL 인스턴스에서 SQL DB를 새 DB로 복원 - alternateWorkloadRestore
* SQL DB를 .bak 파일 -RestoreAsFiles로 복원

관련 복구 지점(고유 또는 로그 시점 인)을 가져온 후 [Get-AzRecoveryServicesBackupBackupRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet을 사용하여 원하는 복구 계획에 따라 복구 구성 개체를 가져옵니다.

#### <a name="original-workload-restore"></a>원래 워크로드 복원

백업된 DB를 복구 지점의 데이터로 재정의하려면 다음 예제와 같이 올바른 플래그와 관련 복구 지점을 지정하기만 하면 됩니다.

##### <a name="original-restore-with-distinct-recovery-point"></a>고유한 복구 지점을 가진 원래 복원

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>로그 시점으로 원본 복원

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>대체 워크로드 복원

> [!IMPORTANT]
> 백업된 SQL DB는 이 자격 증명 모음에 '등록된' Azure VM에서 다른 SQLInstance에만 새 DB로 복원할 수 있습니다.

위에서 설명한 대로 대상 SQLInstance가 다른 Azure VM 내에 있는 경우 [이 자격 증명 모음에 등록되어](#registering-the-sql-vm) 있고 관련 SQLInstance가 보호 가능한 항목으로 표시되는지 확인합니다.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

그런 다음 아래와 같이 올바른 플래그로 관련 복구 지점인 대상 SQL 인스턴스를 전달합니다.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>고유한 복구 지점으로 대체 복원

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>로그 시점으로 대체 복원

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스 대신 .bak 파일로 복원하려면 **파일로 복원** 옵션을 선택합니다. 백업된 SQL DB는 이 자격 증명 모음에 등록된 모든 대상 VM으로 복원할 수 있습니다.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>고유한 복구 지점을 가진 파일로 복원

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>최신 전체에서 로그 시점-인-타임 파일로 복원

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>지정된 전체에서 로그 시점-인-시(시간)으로 파일로 복원

복원에 사용해야 하는 특정 전체를 제공하려면 다음 명령을 사용합니다.

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

[Get-AzRecoveryServicesBackupRestoreConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet에서 얻은 최종 복구 지점 구성 개체에는 복원을 위한 모든 관련 정보가 있으며 아래와 같습니다.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

복원된 DB 이름, 덮어쓰기WLIfpresent, NoRecoveryMode 및 targetPhysicalPath 필드를 편집할 수 있습니다. 아래와 같이 대상 파일 경로에 대한 자세한 내용을 확인합니다.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

아래와 같이 관련 PS 속성을 문자열 값으로 설정합니다.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> 복원 작업은 구성 개체를 기반으로 하므로 최종 복구 구성 개체에 필요한 모든 적절 한 값이 있는지 확인 합니다.

### <a name="restore-with-relevant-configuration"></a>관련 구성으로 복원

관련 복구 Config 개체를 가져오고 확인하면 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet을 사용하여 복원 프로세스를 시작합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

복원 작업은 추적할 작업을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL 백업 관리

### <a name="on-demand-backup"></a>온디맨드 백업

DB에 대한 백업이 활성화되면 사용자는 [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet을 사용하여 DB에 대한 온디맨드 백업을 트리거할 수도 있습니다. 다음 예제는 압축을 사용하도록 설정한 SQL DB에 대한 전체 백업을 트리거하고 전체 백업을 60일 동안 유지해야 합니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

주문형 백업 명령은 추적할 작업을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

출력이 손실되어 있거나 관련 작업 ID를 얻으려면 Azure Backup 서비스에서 [작업 목록을 얻은](#track-azure-backup-jobs) 다음 해당 작업 및 세부 정보를 추적합니다.

### <a name="change-policy-for-backup-items"></a>백업 항목에 대한 정책 변경

사용자는 기존 정책을 수정하거나 백업된 항목의 정책을 Policy1에서 Policy2로 변경할 수 있습니다. 백업된 항목에 대한 정책을 전환하려면 관련 정책을 가져오고 항목을 백업하고 백업 항목과 함께 [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 명령을 매개 변수로 사용합니다.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

명령은 구성 백업이 완료될 때까지 대기하고 다음 출력을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL VM 다시 등록

> [!WARNING]
> 재등록을 시도하기 전에 이 [문서를](backup-sql-server-azure-troubleshoot.md#re-registration-failures) 읽고 실패 증상과 원인을 이해해야 합니다.

SQL VM의 재등록을 트리거하려면 관련 백업 컨테이너를 가져와 레지스터 cmdlet에 전달합니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

사용자가 보호를 중지하려는 경우 [비활성화-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet을 사용할 수 있습니다. 이렇게 하면 예약된 백업이 중지되지만 지금까지 백업된 데이터는 영원히 유지됩니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>백업 데이터 삭제

볼트에 저장된 백업 데이터를 완전히 제거하려면 '-RemoveRecoveryPoints' 플래그/스위치를 ['사용 안 함' 보호 명령으로](#retain-data)추가하기만 하면 됩니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>자동 보호 사용 안 함

SQLInstance에서 자동 보호가 구성된 경우 사용자는 [비활성화-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet을 사용하여 비활성화할 수 있습니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL VM 등록 취소

SQL 서버의 모든 [DB가 더 이상 보호되지 않고 백업 데이터가 없는](#delete-backup-data)경우 사용자는 이 자격 증명 모음에서 SQL VM을 등록 취소할 수 있습니다. 그런 다음에야 사용자는 다른 볼트에 대한 DB를 보호할 수 있습니다. [등록 취소-AzRecoveryServices백업컨테이너](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet을 사용하여 SQL VM을 등록 취소합니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure 백업 작업 추적

Azure Backup은 SQL 백업에서 사용자가 트리거한 작업만 추적한다는 점에 유의해야 합니다. 예약된 백업(로그 백업 포함)은 포털/전원 셸에 표시되지 않습니다. 그러나 예약된 작업이 실패하면 [백업 경고가](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 생성되어 포털에 표시됩니다. [Azure 모니터를 사용하여](backup-azure-monitoring-use-azuremonitor.md) 예약된 모든 작업 및 기타 관련 정보를 추적합니다.

사용자는 백업과 같은 비동기 작업의 [출력에서](#on-demand-backup) 반환되는 JobID를 통해 온디맨드/사용자 트리거 작업을 추적할 수 있습니다. [Get-AzRecovery서비스백업작업PS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) cmdlet을 사용하여 작업 및 세부 정보를 추적합니다.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Azure 백업 서비스에서 주문형 작업 및 해당 상태 목록을 얻으려면 [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet을 사용합니다. 다음 예제에서는 진행 중인 모든 SQL 작업을 반환합니다.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

진행 중인 작업을 취소하려면 [중지-AzRecovery서비스백업작업](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet을 사용합니다.

## <a name="managing-sql-always-on-availability-groups"></a>SQL 항상 가용성 그룹 관리

SQL Always On 가용성 그룹의 경우 AG(가용성 그룹)의 [모든 노드를 등록해야](#registering-the-sql-vm) 합니다. 모든 노드에 대한 등록이 완료되면 SQL 가용성 그룹 개체는 보호 가능한 항목 아래에 논리적으로 만들어집니다. SQL AG 아래의 데이터베이스는 'SQLDatabase'로 나열됩니다. 노드는 독립 실행형 인스턴스로 표시되고 노드 아래에 있는 기본 SQL 데이터베이스도 SQL 데이터베이스로 나열됩니다.

예를 들어 SQL AG에 'SQL-server-0' 및 'SQL-server-1' 및 1 SQL AG DB의 두 개의 노드가 있다고 가정해 보겠습니다. 이러한 노드가 모두 등록되면 사용자가 [보호 가능한 항목을 나열하면](#fetching-sql-dbs)다음 구성 요소가 나열됩니다.

* SQL AG 개체 - SQLAvailabilityGroup으로 보호 가능한 항목 유형
* SQL AG DB - SQLDatabase로 보호 가능한 항목 유형
* SQL-server-0 - SQLInstance로 보호 가능한 항목 유형
* SQL-server-1 - SQLInstance로 보호 가능한 항목 유형
* SQL-server-0에서 기본 SQL DB(마스터, 모델, msdb) - SQLDatabase로 보호 가능한 항목 유형
* SQL-server-1에서 기본 SQL DB(마스터, 모델, msdb) - SQLDatabase로 보호 가능한 항목 유형

sql-server-0, sql-server-1은 [백업 컨테이너가 나열될](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)때 "AzureVMAppContainer"로도 나열됩니다.

관련 SQL 데이터베이스를 가져 와서 [백업을 활성화하고](#configuring-backup) [주문형 백업](#on-demand-backup) 및 [복원 PS cmdlet은](#restore-sql-dbs) 동일합니다.
