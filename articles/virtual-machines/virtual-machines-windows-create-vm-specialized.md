---
title: "Windows VM 복사본 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Windows를 실행 중인 전문화된 Azure VM의 복사본을 만드는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a779f084e0ad6de71ad3e2de86a2fb85738b8fe6


---
# <a name="create-a-vm-from-a-specialized-vhd"></a>전문화된 VHD에서 VM 만들기
Powershell을 사용하여 OS 디스크인 특수한 VHD를 연결하여 새 VM을 만듭니다. 특수한 VHD는 사용자 계정, 응용 프로그램 및 원본 VM의 다른 상태 데이터를 유지 관리합니다. 

일반화된 VHD에서 VM을 만들려는 경우 [일반화된 VHD 이미지에서 VM 만들기](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="create-the-subnet-and-vnet"></a>서브넷 및 VNet 만들기
[가상 네트워크](../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 이 예제에서는 **myResourceGroup** 리소스 그룹에 **mySubNet**으로 명명된 서브넷을 만들고 **10.0.0.0/24**에 대한 서브넷 주소 접두사를 설정합니다.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. VNet을 만듭니다. 이 예제에서는 가상 네트워크 이름을 **myVnetName**으로, 위치를 **미국 서부**로, 가상 네트워크의 주소 접두사를 **10.0.0.0/16**으로 설정합니다. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>공용 IP 주소 및 NIC 만들기
가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP를 만듭니다. 이 예제에서는 공용 IP 주소 이름을 **myIP**로 설정합니다.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예제에서는 NIC 이름을 **myNicName**으로 설정합니다.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 새 VM의 VHD가 기존의 특수한 VM에서 생성되었기 때문에 VM이 만들어진 후에 RDP를 사용하여 로그온할 사용 권한을 갖고 있던 원본 가상 컴퓨터에서 기존 계정을 사용할 수 있습니다.

이 예제에서는 NSG 이름을 **myNsg**로 설정하고 RDP 규칙 이름을 **myRdpRule**로 설정합니다.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

끝점 및 NSG 규칙에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="create-the-vm-configuration"></a>VM 구성 만들기
복사한 VHD를 OS VHD로 연결하도록 VM 구성을 설정합니다.

```powershell
# Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept 
# in a storage account named "myStorageAccount" in a container named "myContainer".
$osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"

# Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

# Add the NIC
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the 
# term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this 
# Windows-based VHD should be attached to the VM as the OS disk.
$osDiskName = $vmName + "osDisk"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


데이터 디스크를 VM에 연결해야 하는 경우 다음을 추가해야 합니다. 

```powershell
# Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit 
# Number (Lun).
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

데이터 및 운영 체제 디스크 URL은 다음과 유사하게 나타납니다. `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` 포털에서 대상 저장소 컨테이너로 이동하고 운영 체제 또는 복사된 데이터 VHD를 클릭한 다음 URL의 내용을 복사하여 이 내용을 찾을 수 있습니다.

## <a name="create-the-vm"></a>VM 만들기
방금 만든 구성을 사용하여 VM을 만듭니다.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
새로 만든 VM은 [Azure Portal](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>다음 단계
새 가상 컴퓨터에 로그인하려면 [포털](https://portal.azure.com)에서 VM으로 이동한 다음 **연결**을 클릭하고 원격 데스크톱 RDP 파일을 엽니다. 원본 가상 컴퓨터의 계정 자격 증명을 사용하여 새 가상 컴퓨터에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.




<!--HONumber=Nov16_HO3-->


