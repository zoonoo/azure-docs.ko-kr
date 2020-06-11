---
title: Azure Managed Disks의 서버 쪽 암호화 - PowerShell
description: Azure Storage는 미사용 데이터를 암호화한 후 저장소 클러스터에 보관하여 데이터를 보호합니다. Microsoft 관리형 키를 사용하여 사용자의 관리 디스크를 암호화하거나, 고객 관리형 키를 사용하여 사용자 고유의 키로 암호화를 관리할 수 있습니다.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 164ce87df77d81a7d36d4448f5d8da8287ed0a01
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656724"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure Managed Disks의 서버 쪽 암호화

Azure Managed Disks는 데이터를 클라우드에 보관할 때 기본적으로 자동으로 데이터를 암호화합니다. SSE(서버 쪽 암호화)는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다.

Azure Managed Disks의 데이터는 사용 가능한 가장 강력한 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 투명하게 암호화되며 FIPS 140-2 규격입니다. 암호화 모듈의 기본 Azure Managed Disks에 대한 자세한 정보는 [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)을 참조하세요.

암호화는 관리 디스크의 성능에 영향을 주지 않으며 암호화에 대한 추가 비용은 없습니다. 

> [!NOTE]
> 임시 디스크는 관리 디스크가 아니며 SSE로 암호화되지 않습니다. 임시 디스크에 대한 자세한 정보는 [관리 디스크 개요: 디스크 역할](managed-disks-overview.md#disk-roles)을 참조하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리형 키를 사용하여 관리 디스크를 암호화하거나 사용자 고유의 키를 사용하여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 관리 디스크의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 *고객 관리형 키*를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대한 각 옵션을 자세히 설명합니다.

## <a name="platform-managed-keys"></a>플랫폼 관리형 키

기본적으로 관리 디스크는 플랫폼 관리형 암호화 키를 사용합니다. 2017년 6월 10일부터 기존 관리 디스크에 기록되는 모든 새 관리 디스크, 스냅샷, 이미지 및 새 데이터는 플랫폼 관리형 키로 자동으로 미사용 암호화됩니다.

## <a name="customer-managed-keys"></a>고객 관리형 키

사용자 고유의 키를 사용하여 각 관리 디스크 수준에서 암호화를 관리할 수 있습니다. 고객 관리형 키를 사용하는 관리 디스크에 대한 서버 쪽 암호화는 Azure Key Vault와의 통합 환경을 제공합니다. [사용자의 RSA 키](../../key-vault/keys/hsm-protected-keys.md)를 Key Vault로 가져오거나 Azure Key Vault에서 새 RSA 키를 생성할 수 있습니다. 

Azure Managed Disks는 [봉투(Envelope) 암호화](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)를 사용하여 완전히 투명한 방식으로 암호화 및 암호 해독을 처리합니다. 키를 사용하여 보호되는 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 DEK(데이터 암호화 키)를 사용하여 데이터를 암호화합니다. 스토리지 서비스는 데이터 암호화 키를 생성하고 RSA 암호화를 사용하여 고객 관리형 키로 이를 암호화합니다. 봉투(Envelope) 암호화를 사용하면 VM에 영향을 주지 않고 규정 준수 정책에 따라 정기적으로 키를 회전(변경)할 수 있습니다. 키를 회전하면 스토리지 서비스가 새로운 고객 관리형 키를 사용하여 데이터 암호화 키를 다시 암호화합니다. 

DEK를 암호화 및 암호 해독하는 데 키를 사용하려면 Key Vault에서 관리 디스크에 대한 액세스 권한을 부여해야 합니다. 이를 통해 데이터 및 키를 완전히 제어할 수 있습니다. 언제든지 키를 사용하지 않도록 설정하거나 관리 디스크에 대한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 통해 암호화 키 사용을 감사하여 관리 디스크 또는 다른 신뢰할 수 있는 Azure 서비스만 키에 액세스하도록 할 수 있습니다.

프리미엄 SSD, 표준 SSD 및 표준 HDD의 경우: 키를 사용하지 않도록 설정하거나 삭제하면 해당 키를 사용하는 디스크가 있는 모든 VM이 자동으로 종료됩니다. 그 후에 키를 다시 사용하도록 설정하지 않거나 새 키를 할당하지 않으면 VM을 사용할 수 없습니다.

Ultra Disks의 경우 키를 사용하지 않도록 설정하거나 삭제해도 키를 사용하는 Ultra Disks가 있는 모든 VM이 자동으로 종료되지 않습니다. VM의 할당을 취소하고 다시 시작한 후에는 디스크에서 키 사용이 중지되고 VM은 다시 온라인 상태가 되지 않습니다. VM을 다시 온라인 상태로 전환하려면 새 키를 할당하거나 기존 키를 사용하도록 설정해야 합니다.

다음 다이어그램은 관리 디스크에서 고객 관리형 키를 사용하여 요청하기 위해 Azure Active Directory 및 Azure Key Vault를 사용하는 방법을 보여 줍니다.

![관리 디스크 및 고객 관리형 키 워크플로. 관리자가 Azure Key Vault를 만든 다음, 디스크 암호화 집합을 만들고, 디스크 암호화 집합을 설정합니다. 해당 집합은 VM에 연결되어 있으므로 디스크에서 Azure AD를 사용하여 인증할 수 있습니다.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


다음 목록에서는 다이어그램에 대해 보다 자세히 설명합니다.

1. Azure Key Vault 관리자가 Key Vault 리소스를 만듭니다.
1. Key Vault 관리자가 RSA 키를 Key Vault로 가져오거나 Key Vault에서 새 RSA 키를 생성합니다.
1. 관리자는 Azure Key Vault ID와 키 URL을 지정하는 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리 디스크에 대한 키 관리를 간소화하기 위해 도입된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 만들어지면 [시스템이 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)가 Azure AD(Active Directory)에서 생성되고 디스크 암호화 집합과 연결됩니다. 
1. 그 다음, Azure Key Vault 관리자가 Key Vault에서 작업을 수행할 수 있는 관리 ID 권한을 부여합니다.
1. VM 사용자는 디스크 암호화 집합과 연결하여 디스크를 만듭니다. 또한 VM 사용자는 디스크 암호화 집합과 연결하여 기존 리소스에 대해 고객 관리형 키와 함께 서버 쪽 암호화를 사용하도록 설정할 수 있습니다. 
1. 관리 디스크는 관리 ID를 사용하여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰는 경우 관리 디스크는 데이터 암호화 및 암호 해독을 수행하기 위해 데이터 암호화 키를 암호화(래핑)하고 해독(래핑 해제)하는 요청을 Azure Key Vault로 보냅니다. 

고객 관리형 키에 대한 액세스를 취소하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요. 액세스를 취소하면 Azure Storage에서 암호화 키에 액세스할 수 없으므로 스토리지 계정의 모든 데이터에 대한 액세스가 효과적으로 차단됩니다.

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 [모든 데이터를 복사](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)해야 합니다.
- 크기가 2080인 ["soft" 및 "hard" RSA 키](../../key-vault/keys/about-keys.md)만 지원되며 다른 키나 크기는 지원되지 않습니다.
- 서버 쪽 암호화 및 고객 관리형 키를 사용하여 암호화된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리형 키를 사용하여 암호화해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객 관리형 키로 암호화된 디스크에서 만든 스냅샷은 동일한 고객 관리형 키를 사용하여 암호화해야 합니다.
- 고객 관리형 키와 관련된 모든 리소스(Azure Key Vault, 디스크 암호화 집합, VM, 디스크 및 스냅샷)는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리형 키를 사용하여 암호화된 디스크, 스냅샷 및 이미지는 다른 구독으로 이동할 수 없습니다.
- Azure Portal을 사용하여 디스크 암호화 집합을 만들 경우 현재는 스냅샷을 사용할 수 없습니다.
- 또한 고객 관리형 키에 서버 쪽 암호화를 사용하여 암호화된 Managed Disks는 Azure Disk Encryption으로 암호화할 수 없고 그 반대의 경우도 마찬가지입니다.
- 공유 이미지 갤러리에서 고객 관리형 키를 사용하는 방법에 대한 자세한 정보는 [미리 보기: 이미지 암호화를 위해 고객 관리형 키 사용](../image-version-encryption.md)을 참조하세요.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 DiskEncryptionSet 설정

1. 최신 [Azure PowerShell 버전](/powershell/azure/install-az-ps)을 설치했으며 Connect-AzAccount를 사용하여 Azure 계정에 로그인했는지 확인합니다.

1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만드는 경우 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제를 사용하면 지정된 보존 기간(기본적으로 90일) 동안 Key Vault에 삭제된 키를 보관하게 됩니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이러한 설정은 실수로 삭제하여 데이터가 손실되지 않도록 보호합니다. 이러한 설정은 관리 디스크를 암호화하기 위해 Key Vault를 사용하는 경우 필수입니다.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    DiskEncryptionSet의 인스턴스를 만듭니다. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Key Vault에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여합니다.

        > [!NOTE]
        > Azure가 Azure Active Directory에서 DiskEncryptionSet의 ID를 만드는 데는 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다"와 같은 오류가 발생하면 몇 분 정도 기다린 후 다시 시도하세요.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용하여 VM을 만들고 고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화합니다.

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객 관리형 키와 함께 서버 쪽 암호화를 사용하여 암호화된 빈 디스크를 만들어 VM에 연결합니다.

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

#### <a name="encrypt-existing-managed-disks"></a>기존 관리 디스크 암호화 

다음 스크립트를 사용하여 기존 디스크를 암호화하려면 실행 중인 VM에 해당 디스크가 연결되어 있지 않아야 합니다.

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용하여 가상 머신 확장 집합을 만들고 고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화합니다.

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>DiskEncryptionSet의 키를 변경하여 DiskEncryptionSet를 참조하는 모든 리소스에 대한 키를 회전합니다.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>디스크의 서버 쪽 암호화 상태 찾기

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)은 Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능을 활용하여 게스트 VM 내에서 고객 관리 키를 사용하여 Managed Disks를 암호화합니다.  고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../../key-vault/general/overview.md)
- [고객 관리형 키를 사용하도록 설정된 디스크를 사용하여 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
