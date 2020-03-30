---
title: Azure 마이그레이션 서버 마이그레이션을 사용하여 서버 측 암호화(SSE) 및 고객 관리 키(CMK)를 사용하여 VMware 가상 컴퓨터를 Azure로 마이그레이션합니다.
description: Azure 마이그레이션 서버 마이그레이션을 사용하여 서버 측 암호화(SSE) 및 CMK(고객 관리 키)를 사용하여 VMware VM을 Azure로 마이그레이션하는 방법을 알아봅니다.
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269485"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>서버 측 암호화 및 고객 관리 키를 사용하여 VMware VM을 Azure VM으로 마이그레이션

이 문서에서는 Azure 마이그레이션 서버 마이그레이션(에이전트 없는 복제)을 사용하여 고객 관리 키(CMK)를 사용하여 서버 측 암호화(SSE)를 사용하여 암호화된 디스크를 사용하여 VMware VM을 Azure 가상 컴퓨터로 마이그레이션하는 방법을 설명합니다.

Azure 마이그레이션 서버 마이그레이션 포털 환경을 사용하면 [에이전트 없는 복제를 사용하여 VMware VM을 Azure로 마이그레이션할](tutorial-migrate-vmware.md) 수 있습니다. 포털 환경은 현재 Azure에서 복제된 디스크에 대해 CMK를 사용하여 SSE를 켜는 기능을 제공하지 않습니다. 복제된 디스크에 대해 CMK를 사용하여 SSE를 켜는 기능은 현재 REST API를 통해서만 사용할 수 있습니다. 이 문서에서는 VMware VM을 복제하고 AZURE에서 복제된 디스크를 CMK와 함께 사용하도록 Azure에서 복제된 디스크를 구성하는 [Azure 리소스 관리자 템플릿을](../azure-resource-manager/templates/overview.md) 만들고 배포하는 방법을 알아봅습니다.

이 문서의 예제에서는 [Azure PowerShell을](/powershell/azure/new-azureps-module-az) 사용하여 리소스 관리자 템플릿을 만들고 배포하는 데 필요한 작업을 수행합니다.

관리 디스크에 대한 CMK(고객 관리 키)를 사용한 서버 측 암호화(SSE)에 대해 [자세히 알아보십시오.](../virtual-machines/windows/disk-encryption.md)

## <a name="prerequisites"></a>사전 요구 사항

- 도구 요구 사항을 이해하기 위해 에이전트 없는 복제를 사용하여 VMware VM을 Azure로 마이그레이션하는 방법에 대한 [자습서를 검토합니다.](tutorial-migrate-vmware.md)
- [다음 지침에 따라](how-to-add-tool-first-time.md) Azure 마이그레이션 프로젝트를 만들고 **Azure 마이그레이션: 서버 마이그레이션** 도구를 프로젝트에 추가합니다.
- [이 지침에 따라](how-to-set-up-appliance-vmware.md) 온-프레미스 환경에서 VMware에 대한 Azure 마이그레이션 어플라이언스를 설정하고 완전한 검색을 수행합니다.

## <a name="prepare-for-replication"></a>복제 준비

VM 검색이 완료되면 서버 마이그레이션 타일의 검색된 서버 줄에 어플라이언스에서 검색된 VM웨어 VM 수가 표시됩니다.

VM 복제를 시작하려면 먼저 복제 인프라를 준비해야 합니다.

1. 대상 지역에서 서비스 버스 인스턴스를 만듭니다. 서비스 버스는 온-프레미스 Azure Migrate 어플라이언스에서 복제 및 마이그레이션을 조정하기 위해 서버 마이그레이션 서비스와 통신하는 데 사용됩니다.
2. 복제에서 작업 로그를 전송하기 위한 저장소 계정을 만듭니다.
3. Azure 마이그레이션 어플라이언스가 복제 데이터를 업로드하는 저장소 계정을 만듭니다.
4. 키 볼트를 만들고 Key Vault를 구성하여 3단계및 4단계에서 생성된 저장소 계정의 Blob 액세스를 위해 공유 액세스 서명 토큰을 관리합니다.
5. 1단계에서 만든 서비스 버스에 대한 공유 액세스 서명 토큰을 생성하고 이전 단계에서 만든 Key Vault에서 토큰에 대한 비밀을 만듭니다.
6. 키 볼트 액세스 정책을 만들어 온-프레미스 Azure 마이그레이션 어플라이언스(어플라이언스 AAD 앱 사용) 및 키 자격 증명 모음에 대한 서버 마이그레이션 서비스 액세스 권한을 부여합니다.
7. 복제 정책을 만들고 이전 단계에서 만든 복제 인프라에 대한 세부 정보로 서버 마이그레이션 서비스를 구성합니다.

복제 인프라는 마이그레이션을 위해 대상 Azure 리전과 VM이 마이그레이션중인 대상 Azure 구독에서 만들어야 합니다.

서버 마이그레이션 포털 환경은 프로젝트에서 처음으로 VM을 복제할 때 자동으로 이 작업을 수행하여 복제 인프라의 준비를 단순화합니다. 이 문서에서는 포털 환경을 사용하여 하나 이상의 VM을 이미 복제했으며 복제 인프라가 이미 생성되어 있다고 가정합니다. 기존 복제 인프라의 세부 정보를 검색하는 방법과 이러한 세부 정보를 CMK로 복제를 설정하는 데 사용할 리소스 관리자 템플릿의 입력으로 사용하는 방법을 살펴보겠습니다.

### <a name="identifying-replication-infrastructure-components"></a>복제 인프라 구성 요소 식별

1. Azure 포털에서 리소스 그룹 페이지를 이동하여 Azure 마이그레이션 프로젝트가 만들어진 리소스 그룹을 선택합니다.
2. 왼쪽 메뉴에서 **배포를** 선택하고 *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* 문자열로 시작하는 배포 이름을 검색합니다. 이 프로젝트에서 VM에 대한 복제를 설정하기 위해 포털 환경에서 만든 리소스 관리자 템플릿 목록이 표시됩니다. 이러한 템플릿 을 다운로드하고 이를 기본으로 CMK를 사용하여 복제할 템플릿을 준비합니다.
3. 템플릿을 다운로드하려면 이전 단계에서 문자열 패턴과 일치하는 배포를 선택 >하려면 왼쪽 메뉴에서 **템플릿을** 선택> 위쪽 메뉴에서 **다운로드** 를 클릭합니다. template.json 파일을 로컬로 저장합니다. 마지막 단계에서 이 템플릿 파일을 편집합니다.

## <a name="create-a-disk-encryption-set"></a>디스크 암호화 세트 만들기

디스크 암호화 세트 개체는 관리되는 디스크를 SSE에 사용할 CMK가 포함된 키 볼트에 매핑합니다. CMK를 사용하여 VM을 복제하려면 디스크 암호화 집합을 만들고 복제 작업에 대한 입력으로 전달합니다.

Azure PowerShell을 사용하여 디스크 암호화 집합을 만들려면 [여기의](../virtual-machines/windows/disk-encryption.md#powershell) 예제를 따르십시오. VM이 마이그레이션중인 대상 구독과 마이그레이션을 위한 대상 Azure 리전에서 디스크 암호화 집합이 만들어지도록 합니다.

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

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>마이그레이션할 VMware VM에 대한 세부 정보 가져오기

이 단계에서는 Azure PowerShell을 사용하여 마이그레이션해야 하는 VM에 대한 세부 정보를 가져옵니다. 이러한 세부 정보는 복제를 위해 리소스 관리자 템플릿을 생성하는 데 사용됩니다. 특히 관심 있는 두 가지 속성은 다음과 같습니다.

- 검색된 VM에 대한 컴퓨터 리소스 ID입니다.
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

VM을 통해 검색되는 Azure 마이그레이션 어플라이언스에 해당하는 SiteId 문자열의 값을 복사합니다. 위에 표시된 예에서 SiteId는 *"/구독/509099b2-9d2c-4636-b43e-bd5cafb6be69/리소스 그룹/ContosoVMwareCMK/공급자/Microsoft.OffAzure/VMwareSites/VMwareapplianca8basite"*

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

## <a name="create-resource-manager-template-for-replication"></a>복제를 위한 리소스 관리자 템플릿 만들기

- 선택한 편집기에서 **복제 식별 인프라 구성 요소** 단계에서 다운로드한 리소스 관리자 템플릿 파일을 엽니다.
- *"Microsoft.RecoveryServices/볼트/복제패브릭/복제보호컨테이너/복제항목"을* 제외한 모든 리소스 정의를 템플릿에서 제거합니다.
- 위의 형식에 대한 리소스 정의가 여러 개인 경우 하나를 제외한 모든 리소스 정의를 제거합니다. 리소스 정의에서 **속성** 정의를 제거합니다.
- 이 단계의 끝에는 아래 예제와 같고 동일한 속성 집합을 가진 파일이 있어야 합니다.

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

- 리소스 정의에서 **이름** 속성을 편집합니다. name 속성의 마지막 "/" 다음에 오는 문자열을 $machine 값으로 *바꿉니다. (이전*단계의)를 참조하십시오.
- 속성의 값을 **변경합니다.공급자특정 세부 사항.vmwareMachineId** $machine *값으로 속성입니다. ResourceId*(이전 단계의).
- **targetResourceGroupId,** **targetNetworkId,** **targetSubnetName** 대상 리소스 그룹 ID에 대 한 값을 설정, 대상 가상 네트워크 리소스 ID를 대상으로, 그리고 대상 서브넷 이름 각각.
- 이 VM에 Azure 하이브리드 혜택을 적용하려면 **licenseType** 값을 "WindowsServer"로 설정합니다. 이 VM이 Azure 하이브리드 혜택에 적합하지 않은 경우 **licenseType** 값을 NoLicenseType으로 설정합니다.
- **targetVmName** 속성의 값을 마이그레이션된 VM에 대 한 원하는 Azure 가상 컴퓨터 이름으로 변경 합니다.
- 선택적으로 **targetVmSize라는** 속성을 **targetVmName** 속성 아래에 추가합니다. **targetVmSize** 속성의 값을 마이그레이션된 VM에 대해 원하는 Azure 가상 컴퓨터 크기로 설정합니다.
- **disksToInclude** 속성은 온-프레미스 디스크를 나타내는 각 목록 항목과 복제를 위한 디스크 입력 목록입니다. 온-프레미스 VM의 디스크 수만큼 목록 항목을 만듭니다. 목록 항목의 **diskId** 속성을 이전 단계에서 식별된 디스크의 uuid로 바꿉습니다. **isOSDisk** 값을 VM의 OS 디스크에 대해 "true"로 설정하고 다른 모든 디스크에 대해 "false"로 설정합니다. **로그저장소계정Id** 및 **로그저장소계정보안이름** 속성은 변경되지 않습니다. 디스크에 사용할 Azure 관리 디스크*유형(Standard_LRS, Premium_LRS, StandardSSD_LRS)으로* **diskType** 값을 설정합니다. CMK로 암호화해야 하는 디스크의 경우 **diskEncryptionSetId라는** 속성을 추가하고 생성된 디스크 암호화 집합의 리소스 ID에 값을 설정합니다($des.** Id)** *디스크 암호화 세트 만들기* 단계
- 편집된 템플릿 파일을 저장합니다. 위의 예제에서 편집된 템플릿 파일은 다음과 같습니다.

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

이제 편집된 리소스 관리자 템플릿을 프로젝트 리소스 그룹에 배포하여 VM에 대한 복제를 설정할 수 있습니다. [Azure 리소스 관리자 템플릿 및 Azure PowerShell을 사용하여 리소스를 배포하는](../azure-resource-manager/templates/deploy-powershell.md) 방법에 대해 알아봅니다.

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

포털 환경을 통해 복제 상태를 [모니터링하고](tutorial-migrate-vmware.md#track-and-monitor) 테스트 마이그레이션 및 마이그레이션을 수행합니다.
