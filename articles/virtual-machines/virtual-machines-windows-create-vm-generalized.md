---
title: "일반화된 VHD에서 VM 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Azure PowerShell을 사용하여 일반화된 VHD 이미지에서 Windows 가상 컴퓨터를 만드는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: cb7f3a1bf44a18141294ab03677f7e733177c1b8


---
# <a name="create-a-vm-from-a-generalized-vhd-image"></a>일반화된 VHD 이미지에서 VM 만들기
일반화된 VHD 이미지에는 [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 제거된 모든 개인 계정 정보가 포함되어 있습니다. 온-프레미스 VM에서 Sysprep을 실행한 후 [VHD를 Azure에 업로드](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하거나 기존 Azure VM에서 Sysprep을 실행한 후 [VHD를 복사](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하여 일반화된 VHD를 만들 수 있습니다.

전문화된 VHD에서 VM을 만들려는 경우 [전문화된 VHD에서 VM 만들기](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

일반화된 VHD에서 VM을 만드는 가장 빠른 방법은 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)을 사용하는 것입니다. 

## <a name="prerequisites"></a>필수 조건
Hyper-V를 사용하여 만든 것처럼 온-프레미스 VM에서 업로드한 VHD를 사용하려는 경우 [Azure에 업로드할 Windows VHD 준비](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 지침을 따라야 합니다. 

이 방법을 사용하여 VM을 만들려면 먼저 업로드된 VHD와 기존 Azure VM VHD를 일반화해야 합니다. 자세한 내용은 [Sysprep을 사용하여 Windows 가상 컴퓨터 일반화](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 

## <a name="set-the-uri-of-the-vhd"></a>VHD의 URI 설정
VHD에 대한 URI는 형식: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. 이 예에서는 **myVHD**로 명명된 VHD가 **mycontainer** 컨테이너의 **mystorageaccount** 저장소 계정에 있습니다.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[가상 네트워크](../virtual-network/virtual-networks-overview.md)의 vNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 다음 예제에서는 **10.0.0.0/24** 주소 접두사가 있는 **myResourceGroup** 리소스 그룹에 **mySubnet**으로 명명된 서브넷을 만듭니다.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 가상 네트워크 만들기 다음 예제에서는 **10.0.0.0/16** 주소 접두사가 있는 **미국 서부** 위치에 **myVnet**으로 명명된 가상 네트워크를 만듭니다.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>공용 IP 주소 및 네트워크 인터페이스 만들기
가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP 주소 만들기. 이 예에서는 **myPip**라는 공용 IP 주소를 만듭니다. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예에서는 **myNic**라는 NIC를 만듭니다. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 

이 예에서는 포트 3389를 통한 RDP 트래픽을 허용하는 **myRdpRule**이라는 규칙을 포함하는 **myNsg**로 명명된 NSG를 만듭니다. NSG에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>가상 네트워크에 대한 변수 만들기
완료된 가상 네트워크에 대한 변수를 만듭니다. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>VM 만들기
다음 PowerShell 스크립트는 가상 컴퓨터 구성을 설정하고 업로드된 VM 이미지를 새 설치에 대한 소스로 사용하는 방법을 보여 줍니다.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>다음 단계
Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.




<!--HONumber=Nov16_HO3-->


