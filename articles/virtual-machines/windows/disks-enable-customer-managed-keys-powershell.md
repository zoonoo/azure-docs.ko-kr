---
title: Azure PowerShell-SSE 관리 디스크를 사용 하 여 고객 관리 키를 사용 하도록 설정
description: Azure PowerShell를 사용 하 여 관리 디스크에서 고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.date: 03/02/2021
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 63a2e40236e03b9877f4fabac6d0489a87a41ede
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550589"
---
# <a name="azure-powershell---enable-customer-managed-keys-with-server-side-encryption---managed-disks"></a>Azure PowerShell-서버 쪽 암호화 관리 디스크를 사용 하 여 고객 관리 키를 사용 하도록 설정 합니다.

Azure 디스크 저장소를 사용 하면 관리 디스크에 대해 SSE (서버 쪽 암호화)를 사용 하는 경우 (선택 하는 경우) 사용자 고유의 키를 관리할 수 있습니다. 고객 관리 키 및 기타 관리 되는 디스크 암호화 유형으로 SSE에 대 한 개념 정보는 디스크 암호화 문서의 [고객 관리 키](../disk-encryption.md#customer-managed-keys) 섹션을 참조 하세요.

## <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 [모든 데이터를 복사](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)해야 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation"></a>자동 키 회전 없이 Azure Key Vault 및 Disk를 설정 합니다.

SSE에서 고객 관리 키를 사용 하려면 Azure Key Vault 및 DiskEncryptionSet 리소스를 설정 해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview"></a>자동 키 회전 (미리 보기)을 사용 하 여 Azure Key Vault 및 DiskEncryptionSet 설정

1. 최신 [Azure PowerShell 버전](/powershell/azure/install-az-ps)을 설치 했 고를 사용 하 여의 Azure 계정에 로그인 했는지 확인 `Connect-AzAccount` 합니다.
1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만들 때 보호 제거를 사용 하도록 설정 해야 합니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이 설정은 실수로 인 한 삭제로 인해 데이터가 손실 되는 것을 방지 하 고 관리 디스크를 암호화 하는 데 필수적입니다.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  API 버전을 사용 하 여 DiskEncryptionSet을 만들고 `2020-12-01` `rotationToLatestKeyVersionEnabled` Azure Resource Manager 템플릿 [CreateDiskEncryptionSetWithAutoKeyRotation.js](https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/AutoKeyRotation/CreateDiskEncryptionSetWithAutoKeyRotation.json) 를 통해 속성을 true로 설정 합니다.
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/AutoKeyRotation/CreateDiskEncryptionSetWithAutoKeyRotation.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $($keyVault.ResourceId) `
    -keyVaultKeyUrl $($key.Key.Kid) `
    -encryptionType "EncryptionAtRestWithCustomerKey" `
    -region $LocationName
    ```

1.  Key Vault에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여합니다.

    > [!NOTE]
    > Azure가 Azure Active Directory에서 DiskEncryptionSet의 ID를 만드는 데는 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다"와 같은 오류가 발생하면 몇 분 정도 기다린 후 다시 시도하세요.

    ```powershell
    $des=Get-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="examples"></a>예제

이러한 리소스를 만들고 구성 했으므로 이제 이러한 리소스를 사용 하 여 관리 디스크를 보호할 수 있습니다. 다음은 각각의 시나리오를 포함 하는 예제 스크립트 이며,이 스크립트는 관리 디스크를 보호 하는 데 사용할 수 있습니다.

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용하여 VM을 만들고 고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화합니다.

스크립트를 복사 하 고 모든 예제 값을 사용자 고유의 매개 변수로 바꾼 다음 실행 합니다.

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객 관리형 키와 함께 서버 쪽 암호화를 사용하여 암호화된 빈 디스크를 만들어 VM에 연결합니다.

스크립트를 복사 하 고 모든 예제 값을 사용자 고유의 매개 변수로 바꾼 다음 실행 합니다.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

### <a name="encrypt-existing-managed-disks"></a>기존 관리 디스크 암호화 

다음 스크립트를 사용하여 기존 디스크를 암호화하려면 실행 중인 VM에 해당 디스크가 연결되어 있지 않아야 합니다.

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용하여 가상 머신 확장 집합을 만들고 고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화합니다.

스크립트를 복사 하 고 모든 예제 값을 사용자 고유의 매개 변수로 바꾼 다음 실행 합니다.

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>DiskEncryptionSet의 키를 변경하여 DiskEncryptionSet를 참조하는 모든 리소스에 대한 키를 회전합니다.

스크립트를 복사 하 고 모든 예제 값을 사용자 고유의 매개 변수로 바꾼 다음 실행 합니다.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>디스크의 서버 쪽 암호화 상태 찾기

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [고객 관리형 키를 사용하도록 설정된 디스크를 사용하여 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)