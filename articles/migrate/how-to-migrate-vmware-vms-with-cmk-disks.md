---
title: Azure Migrate Server Migration을 사용하여 SSE(서버 쪽 암호화) 및 CMK(고객 관리형 키)가 포함된 VMware 가상 머신을 Azure로 마이그레이션
description: Azure Migrate Server Migration을 사용하여 SSE(서버 쪽 암호화) 및 CMK(고객 관리형 키)가 포함된 VMware VM을 Azure로 마이그레이션하는 방법을 알아보기
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751090"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>서버 쪽 암호화 및 고객 관리형 키를 사용하여 VMware VM을 Azure VM으로 마이그레이션

이 문서에서는 Azure Migrate Server Migration(에이전트 없는 복제)을 사용하여 CMK(고객 관리형 키)와 함께 SSE(서버 쪽 암호화)를 사용해 VMware VM을 암호화된 디스크가 포함된 Azure 가상 머신으로 마이그레이션하는 방법을 설명합니다.

Azure Migrate Server Migration 포털 환경을 사용하여 [에이전트 없는 복제로 VMware vm을 Azure로 마이그레이션할](tutorial-migrate-vmware.md) 수 있습니다. 현재 포털 환경에서는 Azure에서 복제된 디스크에 대해 CMK로 SSE를 설정하는 기능을 제공하지 않습니다. 복제된 디스크에 대해 CMK를 사용하여 SSE를 설정하는 기능은 현재 REST API를 통해서만 사용할 수 있습니다. 이 문서에서는 VMware VM을 복제하고 CMK로 SSE를 사용하도록 Azure에서 복제된 디스크를 구성하는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 만들고 배포하는 방법을 알아봅니다.

이 문서의 예에서는 [Azure PowerShell](/powershell/azure/new-azureps-module-az)을 사용하여 Resource Manager 템플릿을 만들고 배포하는 데 필요한 작업을 수행합니다.

관리 디스크에 대한 CMK(고객 관리 키)를 사용하는 SSE(서버 쪽 암호화)에 대해 [자세히 알아봅니다](../virtual-machines/disk-encryption.md).

## <a name="prerequisites"></a>필수 구성 요소

- 도구 요구 사항을 이해하려면 에이전트 없는 복제를 사용하여 VMware VM을 Azure로 마이그레이션하는 방법에 대한 [자습서를 검토합니다](tutorial-migrate-vmware.md) .
- [다음 지침에 따라](./create-manage-projects.md) Azure Migrate 프로젝트를 만들고 **Azure Migrate: Server Migration** 도구를 이 프로젝트에 추가합니다.
- [다음 지침에 따라](how-to-set-up-appliance-vmware.md) 온-프레미스 환경에서 VMware에 Azure Migrate 어플라이언스를 설정하고 검색을 완료합니다.

## <a name="prepare-for-replication"></a>복제 준비

VM 검색이 완료되면 Server Migration 타일의 검색된 서버 줄에 어플라이언스에서 검색한 VMware VM 수가 표시됩니다.

VM 복제를 시작하려면 복제 인프라를 준비해야 합니다.

1. 대상 지역에 Service Bus 인스턴스를 만듭니다. Service Bus는 온-프레미스 Azure Migrate 어플라이언스에서 Server Migration 서비스와 통신하여 복제 및 마이그레이션을 조정하는 데 사용됩니다.
2. 복제에서 작업 로그를 전송하기 위한 스토리지 계정을 만듭니다.
3. Azure Migrate 어플라이언스에서 복제 데이터를 업로드하는 스토리지 계정을 만듭니다.
4. Key Vault를 만들고, 3단계와 4단계에서 만든 스토리지 계정에서 Blob 액세스에 대해 공유 액세스 서명 토큰을 관리 하도록 Key Vault를 구성합니다.
5. 1단계에서 만든 Service Bus에 대한 공유 액세스 서명 토큰을 생성하고, 이전 단계에서 만든 Key Vault에서 토큰에 대한 암호를 만듭니다.
6. Key Vault 액세스 정책을 만들어서 온-프레미스 Azure Migrate 어플라이언스(어플라이언스 AAD 앱 사용) 및 Server Migration Service에 Key Vault에 대한 액세스를 제공합니다.
7. 복제 정책을 만들고, 이전 단계에서 만든 복제 인프라의 세부 정보를 사용하여 Server Migration 서비스를 구성합니다.

복제 인프라는 마이그레이션에 대한 대상 Azure 지역 및 VM을 마이그레이션하는 대상 Azure 구독에 만들어야 합니다.

Server Migration 포털 환경에서는 프로젝트에서 처음으로 VM을 복제할 때 자동으로 이 작업을 수행하여 복제 인프라의 준비를 간소화합니다. 이 문서에서는 포털 환경을 사용하여 하나 이상의 VM을 이미 복제했고 복제 인프라가 만들어져 있다고 가정합니다. 기존 복제 인프라의 세부 정보를 검색하는 방법, 그리고 이러한 세부 정보를 CMK를 사용하여 복제를 설정하는 데 사용되는 Resource Manager 템플릿에 대한 입력으로 사용하는 방법을 살펴보겠습니다.

### <a name="identifying-replication-infrastructure-components"></a>복제 인프라 구성 요소 식별

1. Azure Portal에서 리소스 그룹 페이지로 이동하여 Azure Migrate 프로젝트를 만든 리소스 그룹을 선택합니다.
2. 왼쪽 메뉴에서 **배포** 를 선택하고, *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* 문자열로 시작하는 배포 이름을 검색합니다. 이 프로젝트의 VM에 복제를 설정하기 위해 포털 환경에서 만든 Resource Manager 템플릿 목록이 표시됩니다. 이러한 템플릿 중 하나를 다운로드하고, 이를 기본으로 삼아서 CMK를 사용하는 복제를 위한 템플릿을 준비합니다.
3. 템플릿을 다운로드하려면, 이전 단계에서 문자열 패턴과 일치하는 배포를 선택하고, 왼쪽 메뉴에서 **템플릿** 을 선택하며, 상단 메뉴에서 **다운로드** 를 클릭합니다. template.json 파일을 로컬로 저장합니다. 마지막 단계에서 이 템플릿 파일을 편집합니다.

## <a name="create-a-disk-encryption-set"></a>디스크 암호화 집합을 만들기

디스크 암호화 집합 개체는 SSE에 사용할 CMK가 포함된 Key Vault에 Managed Disks를 매핑됩니다. CMK로 VM을 복제하려면, 디스크 암호화 집합을 만들고, 이를 복제 작업에 입력으로 전달합니다.

Azure PowerShell를 사용하여 디스크 암호화 집합을 만들려면 [여기](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md)의 예제를 따르세요. VM을 마이그레이션하는 대상 구독에서, 그리고 마이그레이션의 대상 Azure 지역에서 디스크 암호화 집합이 만들어졌는지 확인합니다.

디스크 암호화 집합은 고객 관리형 키를 사용하여 관리 디스크를 암호화 하도록 구성하거나, 고객 관리 키와 플랫폼 키를 모두 사용하여 이중 암호화 하도록 구성할 수 있습니다. 미사용 이중 암호화 옵션을 사용하려면, [여기](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)에 설명된 대로 디스크 암호화 집합을 구성합니다.

아래의 예제에서 디스크 암호화 집합은 고객 관리형 키를 사용하도록 구성됩니다.

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

이 단계에서는 Azure PowerShell 사용하여 마이그레이션해야 하는 VM의 세부 정보를 가져옵니다. 이러한 세부 정보는 복제용 Resource Manager 템플릿을 생성하는 데 사용됩니다. 특히 다음 두 가지 관심 있는 속성을 사용할 수 있습니다.

- 검색된 VM의 컴퓨터 리소스 ID입니다.
- VM 및 해당 디스크 식별자에 대한 디스크 목록입니다.

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

VM이 검색되는 Azure Migrate 어플라이언스에 해당하는 SiteId 문자열 값을 복사합니다. 위에 예제에서 SiteId는 *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* 입니다.

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

마이그레이션할 컴퓨터의 ResourceId, 이름 및 디스크 uuid 값을 복사합니다.
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

## <a name="create-resource-manager-template-for-replication"></a>복제용 Resource Manager 템플릿 만들기

- 선택한 편집기의 **복제 인프라 구성 요소 확인** 단계에서 다운로드한 Resource Manager 템플릿 파일을 엽니다.
- *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"* 형식의 리소스를 제외하고 템플릿에서 모든 리소스 정의를 제거합니다.
- 위의 형식에 대한 리소스 정의가 여러 개 있는 경우 하나를 제외한 모든 리소스를 제거합니다. 리소스 정의에서 **dependsOn** 속성 정의를 제거합니다.
- 이 단계가 끝나면 아래 예제처럼 보이고 속성 집합이 동일한 파일이 있어야 합니다.

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

- 리소스 정의에서 **name** 속성을 편집합니다. name 속성의 마지막 "/" 뒤에 있는 문자열을 *$machine.Name* 값(이전 단계에서)으로 대체합니다.
- **properties.providerSpecificDetails.vmwareMachineId** 속성 값을 *$machine.ResourceId*(이전 단계에서) 값으로 변경합니다.
- **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName의** 값을 각각 대상 리소스 그룹 ID, 대상 가상 네트워크 리소스 ID 및 대상 서브넷 이름으로 설정합니다.
- 이 VM에 Azure 하이브리드 혜택를 적용하려면 **라이선스 유형** 의 값을 "windowsServer"로 설정합니다. 이 VM이 Azure 하이브리드 혜택에 적합하지 않은 경우, **라이선스 유형** 의 값을 NoLicenseType으로 설정합니다.
- **TargetVmName** 속성 값을 마이그레이션된 VM에 대해 원하는 Azure 가상 머신 이름으로 변경합니다.
- 필요에 따라 **targetVmName** 속성 아래의 **targetVmSize** 라는 속성을 추가합니다. **targetVmSize** 속성 값을 마이그레이션된 VM에 대해 원하는 Azure 가상 머신 크기로 설정합니다.
- **disksToInclude** 속성은 각 목록 항목이 하나의 온-프레미스 디스크를 나타내는 복제의 디스크 입력 목록입니다. 온-프레미스 VM의 디스크 수 만큼의 목록 항목을 만듭니다. 목록 항목의 **diskId** 속성을 이전 단계에서 확인한 디스크의 uuid로 바꿉니다. VM의 OS 디스크에 대해 **isOSDisk** 값을 "true"로 설정하고, 다른 모든 디스크에 대해 "false"로 설정합니다. **logStorageAccountId** 및 **logStorageAccountSasSecretName** 속성은 변경하지 않고 그대로 둡니다. **diskType** 값을 디스크에 사용할 Azure 관리형 디스크 유형(*Standard_LRS, Premium_LRS, StandardSSD_LRS*)으로 설정합니다. CMK를 사용하여 암호화해야 하는 디스크의 경우, **diskEncryptionSetId** 라는 속성을 추가하고, 이 값을 *디스크 암호화 집합 만들기* 단계에서 생성된 디스크 암호화 집합의 리소스 ID ( **$des.Id**)에 설정합니다.
- 편집한 텍스트 템플릿 파일을 저장합니다. 위의 예제에서 편집된 템플릿 파일은 다음과 같이 보입니다.

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

이제 편집된 Resource Manager 템플릿을 프로젝트 리소스 그룹에 배포하여 VM에 복제를 설정할 수 있습니다. [Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 리소스를 배포](../azure-resource-manager/templates/deploy-powershell.md)하는 방법을 알아봅니다.

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

포털 환경을 통해 [복제 상태를 모니터링하고](tutorial-migrate-vmware.md#track-and-monitor), 테스트 마이그레이션 및 마이그레이션을 수행합니다.