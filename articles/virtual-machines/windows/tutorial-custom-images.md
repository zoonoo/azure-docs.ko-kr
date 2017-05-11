---
title: "Azure PowerShell을 사용하여 사용자 지정 VM 이미지 만들기 | Microsoft Docs"
description: "자습서 - Azure PowerShell을 사용하여 사용자 지정 VM 이미지 만들기"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ab19073735816ebb32a9840ec03b31b358ccb565
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>PowerShell을 사용하여 Azure VM의 사용자 지정 이미지 만들기

이 자습서에서는 Azure 가상 컴퓨터의 사용자 지정 이미지를 정의하는 방법에 대해 알아봅니다. 사용자 지정 이미지를 사용하면 이미 구성되어 있는 이미지를 사용하여 VM을 만들 수 있습니다. 사용자 지정 이미지는 이진 파일 및 응용 프로그램 사전 로드, 응용 프로그램 구성, VM 데이터 디스크 정의, 기타 OS 구성을 부트스트랩하는 데 사용할 수 있습니다. 사용자 지정 이미지를 만들 때 사용자 지정하는 VM 및 연결된 모든 디스크가 이미지에 포함됩니다.

최신 [Azure PowerShell](/powershell/azure/overview) 모듈을 사용하여 이 자습서의 단계를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 컴퓨터가 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="prepare-vm"></a>VM 준비

가상 컴퓨터의 이미지를 만들려면 VM을 일반화하고 할당을 취소한 후 Azure에서 원본 VM을 일반화된 것으로 표시하여 VM을 준비해야 합니다.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.


1. 가상 컴퓨터에 연결합니다.
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 *%windir%\system32\sysprep*로 변경한 후 *sysprep.exe*를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 *시스템 OOBE(첫 실행 경험) 입력*을 선택하고 *일반화* 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 *종료*를 선택한 다음 **확인**을 클릭합니다.
5. Sysprep이 완료되면 가상 컴퓨터를 종료합니다. **VM을 다시 시작하지 마세요**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>할당을 취소하고 VM을 일반화된 것으로 표시

이미지를 만들려면 VM의 할당을 취소하고 Azure에서 일반화된 것으로 표시해야 합니다.

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm)을 사용하여 VM의 할당을 취소합니다.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

[Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm)을 사용하여 가상 컴퓨터의 상태를 `-Generalized`으로 설정합니다. 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>이미지 만들기

이제 [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) 및 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage)를 사용하여 VM 이미지를 만들 수 있습니다. 다음 예제에서는 *myVM*이라는 VM에서 *myImage*라는 이미지를 만듭니다.

가상 컴퓨터를 가져옵니다. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

이미지 구성을 만듭니다.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

이미지를 만듭니다.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```    

 
## <a name="create-vms-from-the-image"></a>이미지에서 VM 만들기

이미지가 생겼으니, 이 이미지에서 하나 이상의 새 VM을 만들 수 있습니다. 사용자 지정 이미지에서 VM을 만드는 방법은 Marketplace 이미지를 사용하여 VM을 만드는 방법과 매우 비슷합니다. Marketplace 이미지를 사용하는 경우 이미지, 이미지 공급자, 제품, SKU 및 버전에 대한 정보를 제공해야 합니다. 사용자 지정 이미지를 사용하는 경우 사용자 지정 이미지 리소스의 ID만 제공하면 됩니다. 

다음 스크립트에서는 [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage)를 사용하여 사용자 지정 이미지에 대한 정보를 저장하는 *$image* 변수를 만든 다음 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)를 사용하고 방금 만든 *$image* 변수를 사용하여 ID를 지정합니다. 

이 스크립트는 *미국 서부* 위치의 새 리소스 그룹 *myResourceGroupFromImage*에 있는 사용자 지정 이미지로 *myVMfromImage*라는 VM을 만듭니다.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지 만들기에 대해 알아보았습니다. 고가용성 Virtual Machines에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

[항상 사용 가능한 VM 만들기](tutorial-availability-sets.md)




