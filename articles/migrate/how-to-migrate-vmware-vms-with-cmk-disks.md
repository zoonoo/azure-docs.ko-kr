---
title: Azure Migrate Server 마이그레이션을 사용 하 여 SSE (서버 쪽 암호화) 및 CMK (고객 관리 키)를 사용 하 여 VMware 가상 머신을 Azure로 마이그레이션
description: Azure Migrate Server 마이그레이션을 사용 하 여 SSE (서버 쪽 암호화) 및 CMK (고객 관리 키)를 사용 하 여 VMware Vm을 Azure로 마이그레이션하는 방법에 대해 알아봅니다.
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: 01f30305529e7f142be0ca6ddffa0f5a12a235bb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260015"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>서버 쪽 암호화 및 고객이 관리 하는 키를 사용 하 여 VMware Vm을 Azure Vm으로 마이그레이션

이 문서에서는 VMware Vm을 Azure virtual machines로 마이그레이션하는 방법에 대해 설명 합니다. 여기서는 SSE (서버 쪽 암호화)를 사용 하 여 CMK (고객 관리 키)와 Azure Migrate 서버 마이그레이션 (에이전트 없는 복제)을 사용 합니다.

Azure Migrate Server 마이그레이션 포털 환경을 사용 하 여 [에이전트 없는 복제로 VMware vm을 Azure로 마이그레이션할](tutorial-migrate-vmware.md) 수 있습니다. 현재 포털 환경에서는 Azure에서 복제 된 디스크에 대해 CMK로 SSE를 설정 하는 기능을 제공 하지 않습니다. 복제 된 디스크에 대해 CMK를 사용 하 여 SSE를 설정 하는 기능은 현재 REST API 통해서만 사용할 수 있습니다. 이 문서에서는 VMware VM을 복제 하 고 CMK와 함께 SSE를 사용 하도록 Azure에서 복제 된 디스크를 구성 하는 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 만들고 배포 하는 방법을 알아봅니다.

이 문서의 예에서는 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 를 사용 하 여 리소스 관리자 템플릿을 만들고 배포 하는 데 필요한 작업을 수행 합니다.

관리 디스크에 대 한 CMK (고객 관리 키)를 사용 하는 SSE (서버 쪽 암호화)에 [대해 자세히 알아보세요](../virtual-machines/windows/disk-encryption.md) .

## <a name="prerequisites"></a>필수 구성 요소

- 도구 요구 사항을 이해 하려면 에이전트 없는 복제를 사용 하 여 VMware Vm을 Azure로 마이그레이션하는 방법에 대 한 [자습서를 검토 합니다](tutorial-migrate-vmware.md) .
- [다음 지침에 따라](how-to-add-tool-first-time.md) Azure Migrate 프로젝트를 만들고 **Azure Migrate: 서버 마이그레이션** 도구를 프로젝트에 추가 합니다.
- [다음 지침에 따라](how-to-set-up-appliance-vmware.md) 온-프레미스 환경에서 VMware에 대 한 Azure Migrate 어플라이언스를 설정 하 고 검색을 완료 합니다.

## <a name="prepare-for-replication"></a>복제 준비

VM 검색이 완료 되 면 서버 마이그레이션 타일의 검색 된 서버 줄에 어플라이언스에서 검색 한 VMware Vm 수가 표시 됩니다.

Vm 복제를 시작 하려면 복제 인프라를 준비 해야 합니다.

1. 대상 지역에 Service Bus 인스턴스를 만듭니다. Service Bus은 온-프레미스 Azure Migrate 어플라이언스에서 서버 마이그레이션 서비스와 통신 하 여 복제 및 마이그레이션을 조정 하는 데 사용 됩니다.
2. 복제에서 작업 로그를 전송 하기 위한 저장소 계정을 만듭니다.
3. Azure Migrate 어플라이언스에서 복제 데이터를 업로드 하는 저장소 계정을 만듭니다.
4. 3 단계와 4 단계에서 만든 저장소 계정에서 blob 액세스에 대 한 공유 액세스 서명 토큰을 관리 하도록 Key Vault를 만들고 Key Vault를 구성 합니다.
5. 1 단계에서 만든 service bus에 대 한 공유 액세스 서명 토큰을 생성 하 고 이전 단계에서 만든 Key Vault에서 토큰에 대 한 암호를 만듭니다.
6. 온-프레미스 Azure Migrate 어플라이언스 (어플라이언스 AAD 앱 사용) 및 서버 마이그레이션 서비스에 Key Vault 액세스를 제공 하는 Key Vault 액세스 정책을 만듭니다.
7. 복제 정책을 만들고 이전 단계에서 만든 복제 인프라의 세부 정보를 사용 하 여 서버 마이그레이션 서비스를 구성 합니다.

복제 인프라는 마이그레이션에 대 한 대상 Azure 지역 및 Vm을 마이그레이션하는 대상 Azure 구독에서 만들어야 합니다.

서버 마이그레이션 포털 환경에서는 프로젝트에서 처음으로 VM을 복제할 때 자동으로이 작업을 수행 하 여 복제 인프라의 준비를 간소화 합니다. 이 문서에서는 이미 포털 환경을 사용 하 여 하나 이상의 Vm을 복제 했 고 복제 인프라가 이미 만들어져 있다고 가정 합니다. 기존 복제 인프라의 세부 정보를 검색 하는 방법과 이러한 세부 정보를 CMK를 사용 하 여 복제를 설정 하는 데 사용 되는 리소스 관리자 템플릿에 대 한 입력으로 사용 하는 방법을 살펴보겠습니다.

### <a name="identifying-replication-infrastructure-components"></a>복제 인프라 구성 요소 식별

1. Azure Portal에서 리소스 그룹 페이지로 이동 하 여 Azure Migrate 프로젝트를 만든 리소스 그룹을 선택 합니다.
2. 왼쪽 메뉴에서 **배포** 를 선택 하 고 *"MigrateV2. VMwareV2EnableMigrate"* 문자열로 시작 하는 배포 이름을 검색 합니다. 이 프로젝트의 Vm에 대 한 복제를 설정 하기 위해 포털 환경에서 만든 리소스 관리자 템플릿 목록이 표시 됩니다. 이러한 템플릿 중 하나를 다운로드 하 고이를 기본으로 사용 하 여 CMK를 사용 하 여 복제 하기 위한 템플릿을 준비 합니다.
3. 템플릿을 다운로드 하려면 이전 단계에서 문자열 패턴과 일치 하는 배포를 선택 하 고, 왼쪽 메뉴에서 **템플릿** > 선택 하 > 상단 메뉴에서 **다운로드** 를 클릭 합니다. template.js파일을 로컬로 저장 합니다. 마지막 단계에서이 템플릿 파일을 편집 합니다.

## <a name="create-a-disk-encryption-set"></a>디스크 암호화 집합 만들기

디스크 암호화 집합 개체는 SSE에 사용할 CMK가 포함 된 Key Vault에 Managed Disks 매핑됩니다. CMK를 사용 하 여 Vm을 복제 하려면 디스크 암호화 집합을 만들고이를 복제 작업에 대 한 입력으로 전달 합니다.

Azure PowerShell를 사용 하 여 디스크 암호화 집합을 만들려면 [여기](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) 의 예제를 따르세요. Vm을 마이그레이션하는 대상 구독 및 마이그레이션의 대상 Azure 지역에서 디스크 암호화 집합이 만들어졌는지 확인 합니다.

디스크 암호화 집합은 고객이 관리 하는 키를 사용 하 여 관리 디스크를 암호화 하도록 구성 하거나, 고객 관리 키와 플랫폼 키를 모두 사용 하 여 이중 암호화 하도록 구성할 수 있습니다. 미사용에서 이중 암호화 옵션을 사용 하려면 [여기](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)에 설명 된 대로 디스크 암호화 집합을 구성 합니다.

아래에 표시 된 예제에서 디스크 암호화 집합은 고객이 관리 하는 키를 사용 하도록 구성 됩니다.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>마이그레이션할 VMware VM의 세부 정보를 가져옵니다.

이 단계에서는 Azure PowerShell 사용 하 여 마이그레이션해야 하는 VM의 세부 정보를 가져옵니다. 이러한 세부 정보는 복제를 위해 리소스 관리자 템플릿을 생성 하는 데 사용 됩니다. 특히 다음 두 가지 속성을 사용할 수 있습니다.

- 검색 된 Vm에 대 한 컴퓨터 리소스 ID입니다.
- VM 및 해당 디스크 식별자에 대 한 디스크 목록입니다.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

VM이 검색 된 Azure Migrate 어플라이언스에 해당 하는 SiteId 문자열 값을 복사 합니다. 위에 표시 된 예제에서 SiteId는 *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* 입니다.

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

마이그레이션할 컴퓨터의 ResourceId, name 및 disk uuid 값을 복사 합니다.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>복제용 리소스 관리자 템플릿 만들기

- 선택한 편집기의 **복제 인프라 구성 요소 확인** 단계에서 다운로드 한 리소스 관리자 템플릿 파일을 엽니다.
- "ReplicationFabrics/ *replicationProtectionContainers/replicationMigrationItems"* 형식의 리소스를 제외 하 고 템플릿에서 모든 리소스 정의를 제거 합니다.
- 위의 형식에 대 한 리소스 정의가 여러 개 있는 경우 하나를 제외 하 고 모두 제거 합니다. 리소스 정의에서 **dependsOn** 속성 정의를 제거 합니다.
- 이 단계가 끝나면 아래 예제와 같이 파일을 만들고 동일한 속성 집합을 가집니다.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- 리소스 정의에서 **name** 속성을 편집 합니다. Name 속성에서 last "/" 뒤에 오는 문자열을 $machine 값으로 바꿉니다 *. *이전 단계의 이름입니다.
- **ProviderSpecificDetails vmwareMachineId** 속성 값을 $machine 값으로 변경 합니다. * ResourceId*(이전 단계에서).
- **Targetresourcegroupid**, **targetnetworkid**, **targetSubnetName** 의 값을 대상 리소스 그룹 ID, 대상 가상 네트워크 리소스 id 및 대상 서브넷 이름으로 각각 설정 합니다.
- 이 VM에 대 한 Azure 하이브리드 혜택를 적용 하려면 **licenseType** 의 값을 "windowsserver"로 설정 합니다. 이 VM이 Azure 하이브리드 혜택에 적합 하지 않은 경우 **licenseType** 의 값을 NoLicenseType로 설정 합니다.
- **TargetVmName** 속성 값을 마이그레이션된 VM에 대 한 원하는 Azure 가상 머신 이름으로 변경 합니다.
- 필요에 따라 **targetVmName** 속성 아래에 **targetvmsize** 라는 속성을 추가 합니다. **Targetvmsize** 속성의 값을 마이그레이션된 VM에 대해 원하는 Azure virtual machine 크기로 설정 합니다.
- **Diskstoinclude** 속성은 단일 온-프레미스 디스크를 나타내는 각 목록 항목을 사용 하 여 복제에 대 한 디스크 입력의 목록입니다. 온-프레미스 VM의 디스크 수 만큼 많은 목록 항목을 만듭니다. 목록 항목의 **디스크가** 속성을 이전 단계에서 확인 한 디스크의 uuid로 바꿉니다. VM의 OS 디스크에 대해 **isOSDisk** 값을 "true"로 설정 하 고 다른 모든 디스크에 대해 "false"로 설정 합니다. **Logstorageaccountid** 및 **logStorageAccountSasSecretName** 속성을 변경 하지 않고 그대로 둡니다. **Disktype** 값을 디스크에 사용할 Azure 관리 디스크 유형 (*Standard_LRS, Premium_LRS, StandardSSD_LRS*)으로 설정 합니다. CMK를 사용 하 여 암호화 해야 하는 디스크의 경우 **Diskencryptionsetid** 라는 속성을 추가 하 고이 값을 생성 된 디스크 암호화 집합의 리소스 ID ($des로 설정 합니다 **. Id**) *디스크 암호화 설정* 단계
- 편집한 템플릿 파일을 저장 합니다. 위의 예제에서 편집 된 템플릿 파일은 다음과 같습니다.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>복제 설정

이제 편집 된 리소스 관리자 템플릿을 프로젝트 리소스 그룹에 배포 하 여 VM에 대 한 복제를 설정할 수 있습니다. [Azure Resource Manager 템플릿 및 Azure PowerShell를 사용 하 여 리소스를 배포](../azure-resource-manager/templates/deploy-powershell.md) 하는 방법을 알아봅니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>다음 단계

포털 환경을 통해 복제 상태를 [모니터링](tutorial-migrate-vmware.md#track-and-monitor) 하 고 테스트 마이그레이션 및 마이그레이션을 수행 합니다.
