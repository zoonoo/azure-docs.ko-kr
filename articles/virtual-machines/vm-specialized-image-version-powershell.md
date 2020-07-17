---
title: 특수 이미지에서 VM 만들기
description: 공유 이미지 갤러리에서 특수 이미지를 사용 하 여 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: fdf1e6cf15279a0ff5be4b45385a13a3b967d22e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374630"
---
# <a name="create-a-vm-using-a-specialized-image"></a>특수 이미지를 사용 하 여 VM 만들기 

공유 이미지 갤러리에 저장 된 특수 이미지 버전에서 VM을 만듭니다. 일반화 된 이미지 버전을 사용 하 여 VM을 만들려면 일반화 된 [이미지를 사용 하 여 Vm 만들기](vm-generalized-image-version-powershell.md)를 참조 하세요.

전문 이미지 버전이 있으면 하나 이상의 새 Vm을 만들 수 있습니다. [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet을 사용합니다. 

이 예제에서는 이미지 정의 ID를 사용 하 여 새 VM에서 최신 버전의 이미지를 사용 하는지 확인 합니다. 의 이미지 버전 ID를 사용 하 여 특정 버전을 사용할 수도 있습니다 `Set-AzVMSourceImage -Id` . 예를 들어 이미지 버전 *1.0.0* 을 사용 하려면을 입력 `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` 합니다. 

특정 이미지 버전을 사용 하면 특정 이미지 버전이 지역에서 삭제 되거나 제거 되어 해당 이미지 버전이 제공 되지 않는 경우 자동화가 실패할 수 있음을 의미 합니다. 특정 이미지 버전이 필요 하지 않는 한 새 VM을 만들기 위해 이미지 정의 ID를 사용 하는 것이 좋습니다.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


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

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

## <a name="attach-the-data-disk"></a>데이터 디스크 연결
이미지에 데이터 디스크가 포함 되어 있는 경우 데이터 디스크를 VM에 연결 해야 합니다.

```azurepowershell-interactive

$vm = Get-AzVM -Name $vmName -ResourceGroupName $resourceGroup 

$lun = $imageVersion.StorageProfile.DataDiskImages.Lun

Add-AzVMDataDisk `
   -CreateOption FromImage `
   -SourceImageUri $imageversion.Id `
   -Lun $lun `
   -Caching $imageVersion.StorageProfile.DataDiskImages.HostCaching `
   -DiskSizeInGB $imageVersion.StorageProfile.DataDiskImages.SizeInGB `
   -VM $vm

```


## <a name="next-steps"></a>다음 단계
[Azure 이미지 작성기 (미리 보기)](./linux/image-builder-overview.md) 는 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전](./linux/image-builder-gallery-update-image-version.md)을 업데이트 하 고 만드는 데에도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](./windows/shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](./windows/troubleshooting-shared-images.md)을 참조하세요.
