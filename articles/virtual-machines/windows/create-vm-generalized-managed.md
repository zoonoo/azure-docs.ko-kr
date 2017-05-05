---
title: "Azure에서 관리 VM 이미지로 VM 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Azure PowerShell을 사용하여 일반화된 관리 VM 이미지로 Windows 가상 컴퓨터를 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/7/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 157f8db0f0f7ac751b03b39e504bf0a5ec25effc
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-from-a-generalized-managed-vm-image"></a>일반화된 관리 VM 이미지로 VM 만들기

Azure에서 관리 VM 이미지로 여러 VM을 만들 수 있습니다. 관리 VM 이미지는 OS 및 데이터 디스크를 비롯하여 VM을 만드는 데 필요한 정보를 포함하고 있습니다. OS 디스크와 데이터 디스크를 포함하여 이미지를 구성하는 VHD는 관리 디스크로 저장됩니다. 

일반화된 VM에는 [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 제거된 모든 개인 계정 정보가 포함되어 있습니다. 온-프레미스 VM에서 Sysprep을 실행한 후 [VHD를 Azure에 업로드](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하거나 기존 Azure VM에서 Sysprep을 실행한 후 [VM 이미지를 캡처](capture-image-resource.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하여 일반화된 VM을 만들 수 있습니다.



## <a name="prerequisites"></a>필수 조건

새 VM을 만들려면 [만들어 놓은 관리 VM 이미지가 있어야 합니다](capture-image-resource.md). 

AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.



## <a name="collect-information-about-the-image"></a>이미지에 대한 정보 수집

먼저 이미지에 대한 기본 정보를 수집하고 이미지의 변수를 만들어야 합니다. 이 예제에서는 **미국 중서부** 위치의 **myResourceGroup** 리소스 그룹에 있는 **myImage**라는 관리되는 VM 이미지를 사용합니다. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 vNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 이 예제에서는 주소 접두사로 **10.0.0.0/24**를 사용하는 **mySubnet**이라는 서브넷을 만듭니다.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 가상 네트워크 만들기 이 예제에서는 주소 접두사로 **10.0.0.0/16**을 사용하는 **myVnet**이라는 가상 네트워크를 만듭니다.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>공용 IP 주소 및 네트워크 인터페이스 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

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

RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 NSG(네트워크 보안 규칙)가 필요합니다. 

이 예에서는 포트 3389를 통한 RDP 트래픽을 허용하는 **myRdpRule**이라는 규칙을 포함하는 **myNsg**로 명명된 NSG를 만듭니다. NSG에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
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

## <a name="get-the-credentials-for-the-vm"></a>VM에 대한 자격 증명 가져오기

다음 cmdlet을 사용하면 VM에 원격으로 액세스하기 위해 로컬 관리자 계정으로 사용할 새 사용자 이름 및 암호를 입력할 수 있는 창이 열립니다. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>VM 이름, 컴퓨터 이름 및 VM 크기에 대한 변수 설정

1. VM 이름 및 컴퓨터 이름에 대한 변수를 만듭니다. 이 예제에서는 VM 이름을 **myVM**으로 설정하고 컴퓨터 이름을 **myComputer**로 설정합니다.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 가상 컴퓨터의 크기를 설정합니다. 이 예제에서는 **Standard_DS1_v2** 크기의 VM을 만듭니다. 자세한 내용은 [VM 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) 설명서를 참조하세요.

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. VM 구성에 VM의 이름과 크기를 추가합니다.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>새 VM에 대한 원본 이미지로 VM 이미지 설정

관리되는 VM 이미지의 ID를 사용하여 원본 이미지를 설정합니다.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>OS 구성을 설정하고 NIC를 추가합니다.

저장소 유형(PremiumLRS 또는 StandardLRS) 및 OS 디스크의 크기를 입력합니다. 이 예제에서는 계정 유형을 **PremiumLRS**로, 디스크 크기를 **128GB**로, 디스크 캐싱을 **ReadWrite**로 설정합니다.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>VM 만들기

이전에 작성하여 **$vm** 변수에 저장한 구성을 사용하여 새 VM을 만듭니다.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>다음 단계
Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.


