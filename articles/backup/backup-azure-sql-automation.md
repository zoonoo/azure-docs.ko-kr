---
title: 'Azure Backup: 백업 및 Azure Backup 및 PowerShell을 사용 하 여 Azure Vm에서 SQL Database 복원'
description: 백업 하 고 Azure Backup 및 PowerShell을 사용 하 여 Azure Vm에서 SQL Database를 복원 합니다.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL,
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734232"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>백업 및 PowerShell 사용 하 여 Azure Vm에서 SQL Database 복원

이 문서에서는 Azure PowerShell를 사용 하 여 백업 하 고 사용 하 여 Azure VM 내에서 SQL DB를 복구 하는 방법 설명 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음입니다.

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * PowerShell을 설정 하 고 Azure Recovery Services 공급자를 등록 합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure VM 내에서 SQL DB에 대 한 백업을 구성 합니다.
> * 백업 작업을 실행 합니다.
> * 백업 된 SQL DB를 복원 합니다.
> * 백업을 모니터링 하 고 작업을 복원 합니다.

## <a name="before-you-start"></a>시작하기 전에

* [자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음에 대 한 합니다.
* 에 대 한 기능에 대해 알아보세요 [Azure Vm 내에서 SQL Db 백업](backup-azure-sql-database.md#before-you-start)합니다.
* Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 됩니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

검토 합니다 **Az.RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) Azure 라이브러리에서 참조 합니다.

### <a name="set-up-and-install"></a>설정 및 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음과 같이 PowerShell을 설정 합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전 1.5.0 됩니다.

2. 이 명령 사용 하 여 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup 및 Recovery Services 자격 증명 모음에 대 한 별칭 및 cmdlet을 검토 합니다. 표시 될 수 있습니다의 예는 다음과 같습니다. Cmdlet의 전체 목록은 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 사용 하 여 Azure 계정에 로그인 **Connect AzAccount**합니다.
5. 표시 되는 웹 페이지에서 계정 자격 증명을 입력 하는 것인지 묻는 메시지가 나타납니다.

    * 또는 매개 변수로 계정 자격 증명을 포함할 수 있습니다는 **Connect AzAccount** cmdlet을 사용 해야 **-자격 증명**합니다.
    * CSP 파트너 테 넌 트에 대 한 작업 인 경우 테 넌 트를 해당 tenantID 또는 테 넌 트 기본 도메인 이름을 사용 하 여 고객을 지정 합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

6. 계정에 여러 구독이 있을 수 있으므로 계정을 사용 하려는 구독을 연결 합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록 되었는지 확인 합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 확인 **RegistrationState** 변경 내용 **Registered**합니다. 실행 하지 않는 경우에 **레지스터 AzResourceProvider** cmdlet을 다시 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

1. 자격 증명 모음 리소스 그룹에 배치 됩니다. 기존 리소스 그룹에서 사용 하 여 새로 만들 필요가 합니다 [새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)합니다. 이 예제에서는 미국 서 부 지역에 새 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 사용 합니다 [새로 만들기-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) 자격 증명 모음을 만드는 cmdlet입니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 자격 증명 모음 저장소에 사용할 중복 유형을 지정 합니다.

    * [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
    * 다음 예제에서는 합니다 **-BackupStorageRedundancy** 에 대 한 옵션을[집합 AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd에 대 한 **testvault** 로  **GeoRedundant**합니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 비슷합니다. 연결 된 리소스 그룹 및 위치를 제공 됩니다.

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

* 많은 Azure Backup cmdlet는 자격 증명 모음 개체를 변수에 저장 하는 것이 유용 하므로 Recovery Services 자격 증명 모음 개체를 입력으로 필요 합니다.
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. 사용 하 여 설정 [집합 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)합니다. 컨텍스트를 설정한 후 모든 후속 cmdlet에 적용 됩니다.

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

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* 뷰를 사용 하 여 기본 백업 정책이 보존 [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)합니다.
* 뷰를 사용 하 여 기본 백업 정책이 일정 [Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)합니다.
* 사용할 합니다 [새로 만들기-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) 새 백업 정책을 만들려면 cmdlet. 일정 및 보존 정책 개체를 입력 합니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수 매개 변수로 새 정책의 (**NewSQLPolicy**). **NewSQLPolicy** 매일 "전체" 백업 사용, 180 일 동안 유지 및 2 시간 마다 로그 백업을 수행

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 비슷합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>백업을 사용 하도록 설정

### <a name="registering-the-sql-vm"></a>SQL VM 등록

Azure 파일 공유와 Azure VM 백업에 대 한 Backup 서비스는 Azure Resource Manager 리소스에 연결 하 고 관련 정보를 가져올 수 있습니다. SQL Azure VM 내에서 응용 프로그램 이므로 Backup 서비스는 응용 프로그램에 액세스 하 고 필요한 정보를 가져올 수 있는 권한이 필요 합니다. 이 작업을 수행 하려면 *'등록'* Recovery services 자격 증명을 사용 하 여 SQL 응용 프로그램을 포함 하는 Azure VM입니다. 자격 증명 모음을 사용 하 여 SQL VM을 등록 하면 해당 자격 증명 모음에만 SQL Db를 보호할 수 있습니다. 사용 하 여 [레지스터 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet은 VM을 등록 합니다.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

명령은이 리소스의 '백업 컨테이너'를 반환 하 고 상태를 '등록'

> [!NOTE]
> Force 매개 변수를 지정 하지 않으면 사용자는 텍스트를 사용 하 여 '할까요이 컨테이너에 대 한 보호 사용 안 함' 확인 하도록 요청 합니다. 이 텍스트를 무시 하 고 가정해 "Y"를 확인 하세요. 이것은 알려진된 문제 및 텍스트 및 force 매개 변수 요구 사항을 제거 하는 중

### <a name="fetching-sql-dbs"></a>SQL Db를 가져오는 중

등록이 완료 되 면 Backup 서비스를 사용할 수 있는 SQL 내의 모든 구성 요소 VM 나열할 수 됩니다. 모든 SQL 구성 요소를 보려면 아직이 자격 증명 모음 사용 하 여 백업할 [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

출력 항목 형식과 ServerName을 사용 하 여이 자격 증명이 모음에 등록 된 모든 SQL Vm에서 보호 되지 않는 모든 SQL 구성 요소를 표시 됩니다. 전달 하 여 특정 SQL VM에 추가로 필터링 할 수는 '-컨테이너 ' 고유한 SQL 항목에 도착 하는 'Name' 및 'ServerName' ItemType 함께 조합 플래그 매개 변수 또는 사용 합니다.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>백업 구성

필요한 SQL DB 및 사용 하 여 정책을 만들었으므로 백업 해야 하는 경우, 사용할 수 있습니다는 [사용 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 이 SQL DB에 대 한 백업을 구성 하는 cmdlet입니다.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

명령 백업 구성 완료 되 고 다음과 같은 출력이 반환 될 때까지 대기 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>새 SQL Db를 가져오는 중

컴퓨터가 등록 되 면 Backup 서비스는 다음 사용 가능한 Db의 세부 정보를 가져옵니다. 수동으로 새 '조회' 새로 추가 등 모든 보호 되지 않은 Db를 가져오려고 하는 데 백업 서비스를 시작 해야 사용자 SQL Db/s q L 인스턴스 등록 된 컴퓨터에 나중에 추가 하는 경우 다시 합니다. 사용 된 [Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) 새로 조회를 수행 하려면 SQL VM에서 PS cmdlet. 이 명령은 작업이 완료 될 때까지 대기 합니다. 나중에 사용 된 [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) 최신 보호 되지 않는 SQL 구성 요소 목록을 가져오려면 PS cmdlet

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

관련 보호 가능한 항목을 가져온 후에 설명 된 대로 백업을 사용 합니다 [구역 위에](#configuring-backup)합니다.
하나 하지 않으려는 경우 수동으로 새 Db를 검색, 수 선택할 autoprotection 설명 했 듯이 [아래](#enable-autoprotection)합니다.

## <a name="enable-autoprotection"></a>AutoProtection를 사용 하도록 설정

사용자는 나중에 추가 하는 모든 Db 특정 정책을 사용 하 여 자동으로 보호 되는 backup을 구성할 수 있습니다. Autoprotection 사용 하려면 [Enable AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

명령 하는 것 이므로 모든 향후 Db 작업은는 SQLInstance에서 수행 하는 백업 수준입니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Autoprotection 의도 부여 되 면 새로 가져올 컴퓨터에는 조회 Db 작업은 8 시간 마다 예약 된 백그라운드 작업으로 수행을 추가 합니다.

## <a name="restore-sql-dbs"></a>SQL Db를 복원 합니다.

Azure Backup 다음과 같이 Azure Vm에서 실행 되는 SQL Server 데이터베이스를 복원할 수 있습니다.

1. 트랜잭션 로그 백업을 사용 하 여 특정 날짜 또는 시간 (초)을 복원 합니다. 선택한 시간을 기준으로 복원 하는 데 필요한 로그 백업 체인 및 azure Backup은 자동으로 적절 한 전체 차등 백업 결정 합니다.
2. 특정 복구 지점으로 복원 하려면 특정을 전체 또는 차등 백업을 복원 합니다.

언급 한 필수 조건 확인 [여기](restore-sql-database-azure-vm.md#prerequisites) SQL Db를 복원 하기 전에 합니다.

먼저 가져올 관련 SQL DB를 사용 하 여 백업 합니다 [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>가져오기 관련 복원 시간

사용자는 백업 된 SQL DB 전체/차등 복사본에 복원할 수 위에서 설명한 대로 **또는** 로그-에-시간으로 합니다.

#### <a name="fetch-distinct-recovery-points"></a>서로 다른 복구 지점을 가져오는합니다

사용 하 여 [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) 백업 된 SQL DB에 대 한 고유 (전체/차등) 복구 지점을 가져올 수 있습니다.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

출력은 다음 예제와 유사

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

'RecoveryPointId' 필터 또는 배열 필터를 사용 하 여 관련 된 복구 지점을 가져옵니다.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>지정 시간 복구 지점 페치

사용자가 DB는 특정-특정 시점으로 복원 하려는 경우 사용 하 여 [Get AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet. Cmdlet에는 해당 SQL 백업 항목에 대해 끊어지지 않은 연속 로그 체인의 시작 시간과 종료 시간을 나타내는 날짜의 목록을 반환 합니다. 원하는 지점-에-시간이이 범위 이내 여야 합니다.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

출력은 다음 예제와 비슷하게 됩니다.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

위의 출력은 해당 사용자는 모든에서 시간 표시 시작 시간과 종료 시간 사이 복원할 수 있습니다 의미 합니다. 시간은 utc 이며 모든-특정 시점에서 위에 표시 된 범위 내에 있는 PS 생성 합니다.

> [!NOTE]
> 경우 로그에-시간 복원에 대 한 선택, 시작 지점 즉, 전체 백업 DB 복원 되는 사용자 지정 하지 않는 필요 합니다. Azure Backup 서비스는 주의 전체 복구 계획의 예는 전체 백업 항목 선택에 로그 백업을 적용 등입니다.

### <a name="determine-recovery-configuration"></a>복구 구성 확인

SQL DB 복원 시 다음 복원 시나리오를 사용할 수 있습니다.

1. 다른 복구 지점-OriginalWorkloadRestore에서에서 데이터를 사용 하 여 백업 된 SQL DB를 재정의합니다.
2. SQL DB와 동일한 SQL 인스턴스에서-AlternateWorkloadRestore 새 DB 복원
3. SQL DB AlternateWorkloadRestore 다른 SQL VM에서 다른 SQL 인스턴스에 새 DB로 복원

관련 복구 지점 가져오기 후 (고유 또는 시간에서 로그)를 사용 하 여 [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) 원하는 복구 계획에 따라 복구 구성 개체를 가져올 PS cmdlet.

#### <a name="original-workload-restore"></a>원래 작업 복원

복구 지점에서 데이터를 사용 하 여 백업 DB을 재정의 하려면 지정 오른쪽 플래그 및 관련 복구 지점 다음 예제와 같이 됩니다.

##### <a name="original-restore-with-distinct-recovery-point"></a>원래 고유한 복구 지점 복원

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>원래 로그에서 특정 시점 복원

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>대체 작업 복원

> [!IMPORTANT]
> SQL DB를 백업으로 복원할 수 있습니다 다른 SQLInstance만에 새 DB '등록'이 자격 증명이 모음에 Azure VM에서.

대상 SQLInstance 다른 Azure VM 내에 있는 경우, 위에서 설명한 대로 인지 확인 [이 자격 증명이 모음에 등록 된](#registering-the-sql-vm) 관련 SQLInstance 보호 가능한 항목으로 표시 됩니다.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

그런 다음 전달 하기만 오른쪽 플래그를 사용 하 여 대상 SQL 인스턴스를 관련 복구 지점 아래 표시 된 것 처럼 됩니다.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>대체 고유 복구 지점 복원

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>대체 로그-특정 시점 복원

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

최종 복구 지점 구성 개체에서 가져온 [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 복원에 대 한 모든 관련 정보가 있으며 아래와 같습니다.

````powershell
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
````

복원 된 DB 이름, OverwriteWLIfpresent, NoRecoveryMode, 및 targetPhysicalPath 필드를 편집할 수 있습니다. 아래와 같이 대상 파일 경로 대 한 자세한 세부 정보를 가져옵니다.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

아래와 같이 문자열 값으로 관련 PS 속성을 설정 합니다.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> 최종 복구 구성 개체에 필요 하 고 적절 한 값을 모두 복원 작업이 구성 개체에 따라 달라 집니다 되므로 있는지 확인 합니다.

### <a name="restore-with-relevant-configuration"></a>관련 구성을 사용 하 여 복원

관련 복구 구성 개체를 가져와 확인 후 사용 합니다 [복원 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet은 복원 프로세스를 시작 합니다.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

복원 작업 추적 하는 작업을 반환 합니다.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>SQL 백업 관리

### <a name="on-demand-backup"></a>주문형 백업

Backup이 DB에 대 한 활성화 된, 사용자는 주문형 백업을 사용 하 여 DB를 트리거할 수도 있습니다 [백업 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. 다음 예제에서는 압축 사용 시 SQL DB에 전체 백업을 트리거을 60 일 동안 보존 해야 하는 전체 백업

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

임시 백업 명령을 추적 하는 작업을 반환 합니다.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

출력 손실 되었는지 또는 관련 작업 ID를 가져오려는 [작업의 목록을 가져오려면](#track-azure-backup-jobs) Azure Backup에서 서비스 하 고 다음 하 고 해당 세부 정보를 추적 합니다.

### <a name="change-policy-for-backup-items"></a>백업 항목에 대 한 정책 변경

사용자 기존 정책을 수정 하거나 Policy2를 Policy1에서 백업 항목의 정책을 변경할 수 있습니다. 백업 항목에 대 한 정책으로 전환 하려면 단순히 관련 정책을 가져올 항목을 백업 하 고 사용 합니다 [사용 AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 매개 변수로 백업 항목을 사용 하 여 명령입니다.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

명령 백업 구성 완료 되 고 다음과 같은 출력이 반환 될 때까지 대기 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL Vm을 다시 등록 합니다.

> [!WARNING]
> 이 읽을 수 있는지 [문서](backup-sql-server-azure-troubleshoot.md#re-registration-failures) 오류 증상 및 재등록을 시도 하기 전에 원인을 이해 하려면

SQL VM의 재등록을 트리거하기 위해 관련 백업 컨테이너를 가져오고 등록 cmdlet에 전달 합니다.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

사용자는 보호를 중지 하려는, 하는 경우 사용할 수 있습니다는 [사용 안 함-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet. 이 예약된 된 백업을 중지 되지만까지 지 원하는 데이터를 영구적으로 유지 됩니다.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>백업 데이터 삭제

저장된 된 백업 데이터를 자격 증명 모음에서에서 완전히 제거 하기 위해 추가 '-RemoveRecoveryPoints' 플래그/교체 하는 [protection 명령 ' disable'](#retain-data)합니다.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>자동 보호 사용 안 함

사용자를 사용 하 여 비활성화할 수는 SQLInstance에서 autoprotection을 구성한 경우 합니다 [사용 안 함-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>SQL VM을 등록 취소

하는 경우 SQL server의 모든 Db [보호 되 고 더 백업 데이터가 더 이상을](#delete-backup-data), 사용자는이 자격 증명이 모음에서 SQL VM을 등록 취소할 수 없습니다. 그런 후에 사용자가 다른 자격 증명 모음에 Db를 보호할 수 있습니다. 사용 하 여 [등록 취소 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet SQL VM의 등록을 취소 합니다.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Azure 백업 작업을 추적

Azure Backup만 추적 하는 SQL 백업에 트리거되는 사용자 작업을 확인 하는 것이 반드시 합니다. 예약 된 백업 (로그 백업 포함) portal/powershell에서 표시 되지 않습니다. 그러나 경우 예약 된 작업이 실패 합니다는 [백업 경고](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 생성 되 고 포털에 표시 합니다. [Azure Monitor를 사용 하 여](backup-azure-monitoring-use-azuremonitor.md) 모든 예약 된 작업 및 기타 관련 정보를 추적 합니다.

사용자에 반환 된 JobID 사용 하 여 임시/사용자 트리거 작업을 추적할 수는 [출력](#on-demand-backup) 백업과 같은 비동기 작업입니다. 사용 하 여 [Get AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet 작업 및 세부 정보를 추적 합니다.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Azure Backup 서비스에서 임시 작업 및 해당 상태 목록을 가져오려면 [Get AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet. 다음 예제에서는 모든 진행 중인 SQL 작업을 반환합니다.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

진행 중인 작업을 취소 하려면 사용 합니다 [중지 AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹 관리

SQL Always On 가용성 그룹을 확인 하도록 [노드를 모두 등록](#registering-the-sql-vm) 가용성 그룹 (AG)의 합니다. 모든 노드에 대 한 등록이 완료 되 면 SQL 가용성 그룹 개체를 논리적으로 보호 가능한 항목 아래에 생성 됩니다. SQL AG에서 데이터베이스 'SQLDatabase'으로 나열 됩니다. 노드는 독립 실행형 인스턴스로 표시 되며 해당 항목 아래에서 기본 SQL 데이터베이스를 SQL 데이터베이스로 나열 됩니다.

예를 들어, 가정 SQL AG를 두 개 이상의 노드가: ' sql server 0' 및 ' sql server 1' 및 1 SQL AG DB입니다. 이러한 두 노드는 등록 되 면 사용자 [보호 가능한 항목을 나열](#fetching-sql-dbs), 다음 구성 요소 나열

1. SQL AG 개체-보호 가능한 항목으로 SQLAvailabilityGroup 형식
2. SQL AG를 DB-보호 가능한 항목 형식으로 SQLDatabase
3. SQLInstance로 0 sql server--보호 가능한 항목 입력
4. SQLInstance로 1 sql server--보호 가능한 항목 입력
5. SQLDatabase로 0 sql server--에서 기본 SQL Db (master, model, msdb) 보호 가능한 항목 입력
6. SQLDatabase로 1 sql server--기본 SQL Db (master, model, msdb) 보호 가능한 항목 입력

sql-서버-0, 1-server-sql도 나열 됩니다 "AzureVMAppContainer"으로 시기 [백업 컨테이너 나와](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)합니다.

방금 관련 SQL 데이터베이스를 가져올 [백업을 사용 하도록 설정](#configuring-backup) 하며 [임시 백업을](#on-demand-backup) 및 [PS cmdlet을 복원](#restore-sql-dbs) 동일 합니다.
