---
title: 고정 공용 IP 주소를 사용하는 VM 만들기 - PowerShell | Microsoft Docs
description: PowerShell을 사용하여 고정 공용 IP 주소를 사용하는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 208cff3c816b8243bc31b3db819f13dafe58c1d1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683198"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>PowerShell을 사용하여 고정 공용 IP 주소를 사용하는 가상 머신 만들기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

고정 공용 IP 주소를 사용하여 가상 머신을 만들 수 있습니다. 공용 IP 주소를 사용하면 인터넷에서 가상 머신과 통신할 수 있습니다. 동적 주소 대신 고정 공용 IP 주소를 할당하여 주소가 변경되지 않도록 합니다. [고정 공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#allocation-method)에 대해 자세히 알아봅니다. 기존 가상 머신에 할당된 공용 IP 주소를 동적에서 고정으로 변경하거나 개인 IP 주소를 사용하려면 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)를 참조하세요. 공용 IP 주소에는 [명목 요금](https://azure.microsoft.com/pricing/details/ip-addresses)이 부과되며, 구독당 사용할 수 있는 공용 IP 주소의 수에 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)이 있습니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

로컬 컴퓨터에서 또는 Azure Cloud Shell을 사용하여 다음 단계를 완료할 수 있습니다. 로컬 컴퓨터를 사용하려면 [Azure PowerShell을 설치](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)했는지 확인합니다. Azure Cloud Shell을 사용하려면 뒤에 오는 명령 상자의 오른쪽 상단 모서리에서 **시도**를 선택합니다. Cloud Shell을 통해 Azure에 로그인합니다.

1. Cloud Shell을 사용하는 경우 2단계로 건너뜁니다. 명령 세션을 열고 `Connect-AzAccount`를 사용하여 Azure에 로그인합니다.
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 미국 동부 Azure 지역의 리소스 그룹을 만듭니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. 사용 하 여 가상 머신 만들기를 [New-azvm](/powershell/module/az.Compute/New-azVM) 명령입니다. `-AllocationMethod "Static"` 옵션은 가상 머신에 고정 공용 IP 주소를 할당합니다. 다음 예제는 *myPublicIpAddress*라는 고정적인 기본 SKU 공용 IP 주소를 사용하여 Windows Server 가상 머신을 만듭니다. 메시지가 표시되면 가상 머신에 대한 로그인 자격 증명으로 사용할 사용자 이름과 암호를 입력합니다.

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   공용 IP 주소가 표준 SKU여야 하는 경우 별도의 단계로 [공용 IP 주소를 만들고](virtual-network-public-ip-address.md#create-a-public-ip-address), [네트워크 인터페이스를 만들고](virtual-network-network-interface.md#create-a-network-interface), [네트워크 인터페이스에 공용 IP주소를 할당](virtual-network-network-interface-addresses.md#add-ip-addresses)한 다음, [네트워크 인터페이스를 사용하여 가상 머신을 만들](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)어야 합니다. [공용 IP 주소 SKU](virtual-network-ip-addresses-overview-arm.md#sku)에 대해 자세히 알아봅니다. 가상 머신이 공용 Azure Load Balancer의 백 엔드 풀에 추가되는 경우 가상 머신 공용 IP 주소의 SKU는 부하 분산 장치의 공용 IP 주소의 SKU와 일치해야 합니다. 자세한 내용은 [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus)를 참조하세요.

4. 할당 된 공용 IP 주소를 확인 하 고 생성 된 것으로 정적 주소를 사용 하 여 확인 [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure는 가상 머신을 만든 지역에서 사용되는 주소에서 공용 IP 주소를 할당했습니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.

> [!WARNING]
> 가상 머신의 운영 체제 내에서 IP 주소 설정을 수정하지 마십시오. 운영 체제는 Azure 공용 IP 주소를 인식하지 않습니다. 운영 체제에 개인 IP 주소 설정을 추가할 수 있지만 필요하지 않는 한 그리고 [운영 체제에 개인 IP 주소 추가](virtual-network-network-interface-addresses.md#private)를 읽기 전까지 추가하지 않는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대한 자세한 정보
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대한 자세한 정보
- [사설 IP 주소](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) 및 Azure 가상 머신에 [고정 사설 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대한 자세한 정보
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 만들기에 대한 자세한 정보
