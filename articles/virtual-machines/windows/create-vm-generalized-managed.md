---
title: "Azure에서 관리되는 이미지에서 VM 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 Azure PowerShell 또는 Azure Portal을 사용하여 일반화된 관리되는 이미지로 Windows 가상 머신을 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>관리되는 이미지에서 VM 만들기

Azure PowerShell 또는 Azure Portal을 사용하여 관리되는 VM 이미지로 여러 VM을 만들 수 있습니다. 관리 VM 이미지는 OS 및 데이터 디스크를 비롯하여 VM을 만드는 데 필요한 정보를 포함하고 있습니다. OS 디스크와 데이터 디스크를 포함하여 이미지를 구성하는 VHD는 관리 디스크로 저장됩니다. 

새 VM을 만들려면 [만들어 놓은 관리 VM 이미지가 있어야 합니다](capture-image-resource.md). 

## <a name="use-the-portal"></a>포털 사용

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. **형식**별로 리소스를 정렬하여 간편하게 이미지를 찾을 수 있습니다.
3. 목록에서 사용할 이미지를 선택합니다. 이미지 **개요** 페이지가 열립니다.
4. 메뉴에서 **+ VM 만들기**를 클릭합니다.
5. 가상 컴퓨터 정보를 입력합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 컴퓨터에 로그인하는 데 사용됩니다 완료되면 **확인**을 클릭합니다. 기존 리소스 그룹에서 새 VM을 만들거나 **새로 만들기**를 선택하여 VM을 저장할 새 리소스 그룹을 만들 수 있습니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 
7. **설정**에서 필요한 항목을 변경하고 **확인**을 클릭합니다. 
8. 요약 페이지에서 이미지 이름이 **개인 이미지** 아래 나열되어야 합니다. **확인**을 클릭하여 가상 머신 배포를 시작합니다.


## <a name="use-powershell"></a>PowerShell 사용

### <a name="prerequisites"></a>필수 조건

최신 버전의 AzureRM.Compute 및 AzureRM.Network PowerShell 모듈을 사용하고 있는지 확인합니다. 관리자 권한으로 PowerShell 프롬프트를 열고 다음 명령을 실행하여 설치합니다.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
자세한 내용은 [Azure PowerShell 버전 관리](/powershell/azure/overview)를 참조하세요.



### <a name="collect-information-about-the-image"></a>이미지에 대한 정보 수집

먼저 이미지에 대한 기본 정보를 수집하고 이미지의 변수를 만들어야 합니다. 이 예제에서는 **미국 중서부** 위치의 **myResourceGroup** 리소스 그룹에 있는 **myImage**라는 관리되는 VM 이미지를 사용합니다. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 vNet 및 서브넷을 만듭니다.

서브넷을 만듭니다. 이 예제에서는 주소 접두사로 **10.0.0.0/24**를 사용하는 **mySubnet**이라는 서브넷을 만듭니다.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

가상 네트워크 만들기 이 예제에서는 주소 접두사로 **10.0.0.0/16**을 사용하는 **myVnet**이라는 가상 네트워크를 만듭니다.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>공용 IP 주소 및 네트워크 인터페이스 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

공용 IP 주소 만들기. 이 예에서는 **myPip**라는 공용 IP 주소를 만듭니다. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
NIC 만들기. 이 예에서는 **myNic**라는 NIC를 만듭니다. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기

RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 NSG(네트워크 보안 규칙)가 필요합니다. 

이 예에서는 포트 3389를 통한 RDP 트래픽을 허용하는 **myRdpRule**이라는 규칙을 포함하는 **myNsg**로 명명된 NSG를 만듭니다. NSG에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>가상 네트워크에 대한 변수 만들기

완료된 가상 네트워크에 대한 변수를 만듭니다. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>VM에 대한 자격 증명 가져오기

다음 cmdlet을 사용하면 VM에 원격으로 액세스하기 위해 로컬 관리자 계정으로 사용할 새 사용자 이름 및 암호를 입력할 수 있는 창이 열립니다. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>VM 이름, 컴퓨터 이름 및 VM 크기에 대한 변수 설정

VM 이름 및 컴퓨터 이름에 대한 변수를 만듭니다. 이 예제에서는 VM 이름을 **myVM**으로 설정하고 컴퓨터 이름을 **myComputer**로 설정합니다.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

가상 컴퓨터의 크기를 설정합니다. 이 예제에서는 **Standard_DS1_v2** 크기의 VM을 만듭니다. 자세한 내용은 [VM 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) 설명서를 참조하세요.

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

VM 구성에 VM의 이름과 크기를 추가합니다.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>새 VM에 대한 원본 이미지로 VM 이미지 설정

관리되는 VM 이미지의 ID를 사용하여 원본 이미지를 설정합니다.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>OS 구성을 설정하고 NIC를 추가합니다.

저장소 유형(PremiumLRS 또는 StandardLRS) 및 OS 디스크의 크기를 입력합니다. 이 예제에서는 계정 유형을 **PremiumLRS**로, 디스크 크기를 **128GB**로, 디스크 캐싱을 **ReadWrite**로 설정합니다.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>VM 만들기

이전에 작성하여 **$vm** 변수에 저장한 구성을 사용하여 새 VM을 만듭니다.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>다음 단계
Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

