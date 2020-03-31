---
title: PowerShell을 사용하여 Azure 스팟 VM 배포
description: Azure PowerShell을 사용하여 스팟 VM을 배포하여 비용을 절감하는 방법을 알아봅니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158947"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>미리 보기: Azure PowerShell을 사용하여 스팟 VM 배포


[스팟 VM을](spot-vms.md) 사용하면 사용되지 않는 용량을 크게 절감할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스팟 VM을 제거합니다. 따라서 스팟 VM은 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 적합합니다.

스팟 VM의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows용](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)VM 가격을 참조하십시오. 최대 가격 설정에 대한 자세한 내용은 [스팟 VM - 가격 책정을](spot-vms.md#pricing)참조하십시오.

VM에 대해 시간당 지불할 최대 가격을 설정할 수 있습니다. 스팟 VM의 최대 가격은 소수점 이하 5자리까지 사용하여 미국 달러(USD)로 설정할 수 있습니다. 예를 들어 값은 `0.98765`시간당 0.98765 USD의 최대 가격입니다. 최대 가격을 `-1`설정하면 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 현재 현물 가격 또는 표준 VM의 가격이 될 것이며, 용량과 할당량이 있는 한 더 적습니다.

> [!IMPORTANT]
> 스팟 인스턴스는 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>



## <a name="create-the-vm"></a>VM 만들기

[New-AzVmConfig를](/powershell/module/az.compute/new-azvmconfig) 사용하여 spotVM을 만들어 구성을 만듭니다. 다음 `-Priority Spot` 중 `-MaxPrice` 하나를 포함 및 설정합니다.
- `-1`따라서 VM은 가격에 따라 제거되지 않습니다.
- 달러 금액, 최대 5 자리. 예를 들어, `-MaxPrice .98765` spotVM의 가격이 시간당 약 $.98765가 되면 VM이 할당처리됩니다.


이 예제에서는 Azure에서 용량을 다시 필요로 하는 경우에만 가격 책정에 따라 할당 할당되지 않는 spotVM을 만듭니다.

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM을 만든 후 리소스 그룹의 모든 VM에 대한 최대 가격을 확인하도록 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](../linux/spot-cli.md) 또는 [템플릿을](../linux/spot-template.md)사용하여 스팟 VM을 만들 수도 있습니다.

오류가 발생하면 [오류 코드를](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)참조하십시오.