---
title: Azure PowerShell를 사용 하 여 Azure Managed Disks 백업
description: Azure PowerShell를 사용 하 여 Azure Managed Disks를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630494"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure Managed Disks 백업

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure 관리 디스크](../virtual-machines/managed-disks-overview.md) 를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 백업 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure 디스크의 백업 구성

- 주문형 백업 작업 실행

Azure 디스크 백업 지역 가용성, 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

## <a name="create-a-backup-vault"></a>백업 자격 증명 모음 만들기

백업 자격 증명 모음은 Azure Database for PostgreSQL 서버 및 Azure 디스크와 같이 Azure Backup에서 지 원하는 다양 한 최신 작업에 대 한 백업 데이터를 저장 하는 Azure의 저장소 엔터티입니다. 백업 자격 증명 모음을 사용 하면 관리 오버 헤드를 최소화 하면서 백업 데이터를 쉽게 구성할 수 있습니다. 백업 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며, 백업 데이터를 보호 하는 데 도움이 되는 향상 된 기능을 제공 합니다.

백업 자격 증명 모음을 만들기 전에 자격 증명 모음 내에서 데이터의 저장소 중복성을 선택 합니다. 그런 다음 해당 저장소 중복성 및 위치를 사용 하 여 백업 자격 증명 모음을 만듭니다. 이 문서에서는 리소스 그룹 "testBkpVaultRG" 아래에 있는 "westus" 지역에서 백업 자격 증명 모음 "TestBkpVault"을 만듭니다. [AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 백업 자격 증명 모음을 만듭니다. [백업 자격 증명 모음을 만드는](./backup-vault-overview.md#create-a-backup-vault)방법에 대해 자세히 알아보세요.

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

자격 증명 모음을 만든 후에는 Azure 디스크를 보호 하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

Azure 디스크 백업에 대 한 백업 정책의 내부 구성 요소를 이해 하려면 [AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true)명령을 사용 하 여 정책 템플릿을 검색 합니다. 이 명령은 지정 된 데이터 원본 유형에 대 한 기본 정책 템플릿을 반환 합니다. 이 정책 템플릿을 사용 하 여 새 정책을 만듭니다.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

정책 템플릿은 백업 트리거를 결정 하는 트리거와 백업 삭제/복사/이동 시기를 결정 하는 수명 주기로 구성 됩니다. Azure disk backup에서 trigger의 기본값은 4 시간 마다 (PT4H) 예약 된 매시간 트리거이 고 각 백업이 7 일 동안 유지 되는 경우입니다.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure Disk Backup은 하루에 여러 백업을 제공 합니다. 더 자주 백업 해야 하는 경우 4, 6, 8 또는 12 시간 간격으로 백업을 수행할 수 있는 기능을 사용 하 여 **매시간** 백업 빈도를 선택 합니다. 선택한 **시간** 간격에 따라 백업이 예약 됩니다. 예를 들어 **4 시간 마다** 를 선택 하는 경우 백업이 하루에 동일 하 게 분산 되도록 4 시간 마다 매일 백업이 수행 됩니다. 하루에 한 번 백업이 충분 한 경우 **매일** 백업 빈도를 선택 합니다. 매일 백업 빈도에서 백업을 수행할 시간을 지정할 수 있습니다. 시간은 백업 시작 시간을 나타내고 백업은 백업이 완료 되지 않는다는 점에 유의 해야 합니다. 백업 작업을 완료 하는 데 필요한 시간은 디스크 크기 및 연속 백업 간의 변동 비율을 비롯 한 다양 한 요인에 따라 달라 집니다. 그러나 Azure Disk backup은 프로덕션 응용 프로그램 성능에 영향을 주지 않는 [증분 스냅숏을](../virtual-machines/disks-incremental-snapshots.md)사용 하는 에이전트 없는 백업입니다.

   >[!NOTE]
   > 선택한 자격 증명 모음에 전역 중복 설정이 있을 수 있지만 현재 Azure Disk Backup은 스냅숏 데이터 저장소만 지원 합니다. 모든 백업은 구독의 리소스 그룹에 저장 되며 백업 자격 증명 모음 저장소로 복사 되지 않습니다.

정책 만들기에 대 한 자세한 내용은 [azure disk backup 정책](backup-managed-disks.md#create-backup-policy) 문서를 참조 하세요.

시간별 빈도 또는 보존 기간을 편집 하려면 [AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) 및/또는 [AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) 명령을 사용 합니다. 정책 개체에 원하는 값이 모두 있으면 [AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)을 사용 하 여 정책 개체에서 새 정책 만들기를 계속 진행 합니다.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책을 만든 후에는 사용자가 Azure 디스크를 보호 하기 위해 고려해 야 하는 3 가지 중요 한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련 된 주요 엔터티

#### <a name="disk-to-be-protected"></a>보호할 디스크

보호할 디스크의 ARM ID를 가져옵니다. 이는 디스크의 식별자 역할을 합니다. 다른 구독의 리소스 그룹 "diskrg" 아래에 "PSTestDisk" 라는 디스크의 예제를 사용 합니다.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>스냅숏 리소스 그룹

디스크 스냅숏은 구독 내의 리소스 그룹에 저장 됩니다. 지침으로, Azure Backup 서비스에서 사용할 스냅숏 데이터 저장소로 전용 리소스 그룹을 만드는 것이 좋습니다. 전용 리소스 그룹을 사용 하면 리소스 그룹에 대 한 액세스 권한을 제한할 수 있으므로 안전 하 고 쉽게 백업 데이터를 관리할 수 있습니다. 디스크 스냅숏을 저장 하려는 리소스 그룹의 ARM ID를 적어둡니다.

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

백업 자격 증명 모음은 디스크에 대 한 사용 권한과 스냅숏 리소스 그룹을 사용 하 여 스냅숏을 트리거하고 해당 수명 주기를 관리할 수 있어야 합니다. 자격 증명 모음의 시스템 할당 관리 id는 이러한 권한을 할당 하는 데 사용 됩니다. [AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 recovery services 자격 증명 모음에 대 한 시스템 할당 관리 id를 사용 하도록 설정 합니다.

### <a name="assign-permissions"></a>권한 할당

사용자는 자격 증명 모음 (자격 증명 모음 MSI로 표시) 및 관련 디스크 및/또는 디스크 RG를 통해 RBAC를 통해 몇 가지 권한을 할당 해야 합니다. 이러한 단계는 포털 또는 PowerShell을 통해 수행할 수 있습니다. 관련 된 모든 권한은 [이 섹션](backup-managed-disks.md#configure-backup)의 1, 2, 3 요소에 자세히 설명 되어 있습니다.

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정 하면 백업 구성은 2 단계로 수행 됩니다. 먼저 [AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 관련 자격 증명 모음, 정책, 디스크 및 스냅숏 리소스 그룹을 사용 하 여 관련 요청을 준비 합니다. 그런 다음 [AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 디스크를 보호 하기 위한 요청을 제출 합니다.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

사용자가 AzDataProtectionBackupInstance를 사용 하 여 백업을 트리거하는 관련 백업 인스턴스를 [가져옵니다](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

백업을 트리거하는 동안 보존 규칙을 지정할 수 있습니다. 정책에서 보존 규칙을 보려면 정책 개체에서 보존 규칙을 탐색 합니다. 아래 예제에서는 이름이 ' 기본값 ' 인 규칙이 표시 되 고 주문형 백업에 대해 해당 규칙을 사용 합니다.

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

[AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 요청 시 백업을 트리거합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>작업 추적

[AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 작업을 추적 합니다. 모든 작업을 나열 하 고 특정 작업 세부 정보를 가져올 수 있습니다.

Az. ResourceGraph를 사용 하 여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 백업 자격 증명 모음에서 수행할 수 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell를 사용 하 여 Azure Managed Disks 복원](restore-managed-disks-ps.md)
