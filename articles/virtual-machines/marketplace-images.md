---
title: Azure PowerShell를 사용 하 여 Marketplace 구매 계획 정보 지정
description: 공유 이미지 갤러리에서 이미지를 만들 때 Azure Marketplace 구매 계획 정보를 지정 하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3de79e5cb3db2d0c52d13826900ec7160271edf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86225161"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공

공유 갤러리에서 이미지를 만드는 경우 원래 Azure Marketplace 이미지에서 만든 원본을 사용 하면 구매 계획 정보를 추적 해야 할 수 있습니다. 이 문서에서는 VM에 대 한 구매 계획 정보를 찾은 다음 이미지 정의를 만들 때 해당 정보를 사용 하는 방법을 보여 줍니다. 이미지에 대 한 VM을 만들 때 구매 계획 정보를 간편 하 게 제공 하기 위해 이미지 정의의 정보를 사용 하는 방법도 다룹니다.

Marketplace 이미지를 찾고 사용 하는 방법에 대 한 자세한 내용은 [Azure Marketplace 이미지 찾기 및 사용](./windows/cli-ps-findimage.md)을 참조 하세요.


## <a name="get-the-source-vm-information"></a>원본 VM 정보 가져오기
여전히 원래 VM이 있는 경우 New-azvm를 사용 하 여 계획, 게시자 및 sku 정보를 가져올 수 있습니다. 이 예제에서는 *Myvm* 리소스 그룹에서 *MYVM* 이라는 vm을 가져온 다음 구매 계획 정보를 표시 합니다.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan.Publisher
$vm.Plan.Name
$vm.Plan.Product
```

## <a name="create-the-image-definition"></a>이미지 정의 만들기

이미지를 저장 하는 데 사용 하려는 이미지 갤러리를 가져옵니다. 모든 갤러리를 먼저 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

그런 다음 사용 하려는 갤러리에 대 한 변수를 만듭니다. 이 예에서는 `$gallery` *myGalleryRG* 리소스 그룹에서 *mygallery* 에 대해 라는 변수를 만듭니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

`-PurchasePlanPublisher`, `-PurchasePlanProduct` 및 매개 변수를 사용 하 여 이미지 정의를 만듭니다 `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

그런 다음 [AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)를 사용 하 여 이미지 버전을 만듭니다. [VM](image-version-vm-powershell.md#create-an-image-version), [관리 되는 이미지](image-version-managed-image-powershell.md#create-an-image-version), [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)또는 [다른 이미지 버전](image-version-another-gallery-powershell.md#create-the-image-version)에서 이미지 버전을 만들 수 있습니다. 


## <a name="create-the-vm"></a>VM 만들기

이미지에서 VM 만들기로 이동 하는 경우 [AzVMPlan](/powershell/module/az.compute/set-azvmplan)를 사용 하 여 게시자 정보를 전달 하기 위해 이미지 정의의 정보를 사용할 수 있습니다.


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>다음 단계

Marketplace 이미지를 찾고 사용 하는 방법에 대 한 자세한 내용은 [Azure Marketplace 이미지 찾기 및 사용](./windows/cli-ps-findimage.md)을 참조 하세요.