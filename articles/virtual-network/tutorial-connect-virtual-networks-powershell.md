---
title: 가상 네트워크 피어링으로 가상 네트워크 연결 - PowerShell | Microsoft Docs
description: 가상 네트워크 피어링을 사용하여 가상 네트워크를 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>PowerShell을 사용하여 가상 네트워크 피어링으로 가상 네트워크 연결

가상 네트워크 피어링을 사용하여 가상 네트워크를 서로 연결할 수 있습니다. 가상 네트워크가 피어링되면 두 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대기 시간 및 대역폭으로 서로 통신할 수 있습니다. 이 문서에서는 두 가상 네트워크의 생성 및 피어링에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 두 가상 네트워크 만들기
> * 가상 네트워크 간에 피어링 만들기
> * 피어링 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 문서에서 만든 다른 모든 리소스를 만들어야 합니다. [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork1*이라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷 구성을 만듭니다. 다음 예제에서는 10.0.0.0/24 주소 접두사가 포함된 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork)를 사용하여 가상 네트워크에 서브넷 구성을 씁니다. 그러면 서브넷이 만들어집니다.

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

10.1.0.0/16 주소 접두사 및 서브넷 1개를 사용하여 가상 네트워크를 만듭니다.

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

*myVirtualNetwork2* 가상 네트워크의 주소 접두사는 *myVirtualNetwork1* 가상 네트워크의 주소 접두사와 겹치지 않습니다. 겹치는 주소 접두사가 있는 가상 네트워크는 피어링할 수 없습니다.

## <a name="peer-virtual-networks"></a>가상 네트워크 피어링

[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)을 사용하여 피어링을 만듭니다. 다음 예제에서는 *myVirtualNetwork1*을 *myVirtualNetwork2*에 피어링합니다.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

이전 명령 실행 후 반환된 출력에서 **PeeringState**는 *Initiated*로 표시됩니다. *myVirtualNetwork2*에서 *myVirtualNetwork1*으로 피어링을 만들 때까지 해당 피어링은 *Initiated* 상태를 유지합니다. *myVirtualNetwork2*에서 *myVirtualNetwork1*으로 피어링을 만듭니다. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

이전 명령 실행 후 반환된 출력에서 **PeeringState**는 *Connected*로 표시됩니다. 또한 Azure에서 *myVirtualNetwork1-myVirtualNetwork2* 피어링의 피어링 상태가 *Connected*로 변경되었습니다. [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering)을 사용하여 *myVirtualNetwork1-myVirtualNetwork2* 피어링에 대한 피어링 상태가 *Connected*로 변경되었는지 확인합니다.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

두 가상 네트워크의 피어링에 대한 **PeeringState**가 *Connected*가 될 때까지, 한 가상 네트워크의 리소스는 다른 가상 네트워크의 리소스와 통신할 수 없습니다. 

피어링은 두 가상 네트워크 간에 이루어지지만 전이적이지 않습니다. 따라서, 예를 들어 *myVirtualNetwork2*를 *myVirtualNetwork3*에 피어링하려는 경우 가상 네트워크 *myVirtualNetwork2* 및 *myVirtualNetwork3* 간에 추가 피어링을 만들어야 합니다. *myVirtualNetwork1*이 *myVirtualNetwork2*와 피어링되더라도, *myVirtualNetwork1*도 *myVirtualNetwork3*와 피어링되면 *myVirtualNetwork1* 내의 리소스는 *myVirtualNetwork3*의 리소스에만 액세스할 수 있습니다. 

프로덕션 가상 네트워크를 피어링하기 전에, [피어링 개요](virtual-network-peering-overview.md), [피어링 관리](virtual-network-manage-peering.md) 및 [가상 네트워크 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)에 충분히 익숙해지는 것이 좋습니다. 이 문서에서는 동일한 구독 및 위치에 있는 두 가상 네트워크 간의 피어링을 보여 주지만, [서로 다른 지역](#register) 및 [각기 다른 Azure 구독](create-peering-different-subscriptions.md#powershell)에 있는 가상 네트워크를 피어링할 수도 있습니다. 피어링을 사용하여 [허브 및 스포크 네트워크 디자인](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)을 만들 수도 있습니다.

## <a name="test-peering"></a>피어링 테스트

피어링을 통해 서로 다른 가상 네트워크의 가상 머신 간 네트워크 통신을 테스트하려면 각 서브넷에 가상 머신을 배포하고 가상 머신 간에 통신을 수행합니다. 

### <a name="create-virtual-machines"></a>가상 머신 만들기

각 가상 네트워크에서 가상 머신을 만들어 이후 단계에서 둘 간의 통신 유효성을 검사할 수 있도록 합니다.

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다. 다음 예제에서는 *myVirtualNetwork1* 가상 네트워크에 이름이 *myVm1*인 가상 머신을 만듭니다. `-AsJob` 옵션은 백그라운드에서 가상 머신을 만들기 때문에 다음 단계를 계속 진행할 수 있습니다. 메시지가 나타나면 가상 머신에 로그인할 사용자 이름과 암호를 입력합니다.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure는 10.0.0.4를 가상 머신의 개인 IP 주소로 자동 할당합니다. *myVirtualNetwork1*의 *Subnet1*에서 사용 가능한 첫 번째 IP 주소가 10.0.0.4이기 때문입니다. 

*myVirtualNetwork2* 가상 네트워크에서 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

가상 머신을 만드는 데 몇 분 정도 걸립니다. 반환된 결과는 아니지만 Azure는 가상 머신의 개인 IP 주소로 10.1.0.4를 할당했습니다. *myVirtualNetwork2*의 *Subnet1*에서 사용 가능한 첫 번째 IP 주소가 10.1.0.4이기 때문입니다. 

Azure에서 가상 머신을 만들고 출력을 PowerShell로 반환할 때까지 이후 단계를 계속하지 마세요.

### <a name="test-virtual-machine-communication"></a>가상 머신 통신 테스트

인터넷에서 가상 머신의 공용 IP 주소에 연결할 수 있습니다. [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 가상 머신의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVm1* 가상 머신의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 *myVm1* 가상 머신과의 원격 데스크톱 세션을 만듭니다. `<publicIpAddress>`를 이전 명령에서 반환된 IP 주소로 바꿉니다.

```
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 마들어지고 컴퓨터에 다운로드된 후 열립니다. 사용자 이름과 암호를 입력한 다음, **확인**을 클릭합니다. (가상 머신을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있습니다.) 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

명령 프롬프트에서 ping이 Windows 방화벽을 통과하도록 하여, 이후 단계에서 *myVm2*로부터 가상 머신을 Ping할 수 있도록 합니다.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

이 문서에서는 ping이 테스트용으로 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.

*myVm2* 가상 머신에 연결하려면 *myVm1* 가상 머신의 명령 프롬프트에서 다음 명령을 입력합니다.

```
mstsc /v:10.1.0.4
```

*myVm1*에서 ping을 사용하도록 설정했으므로 이제, *myVm2* 가상 머신의 명령 프롬프트에서 IP 주소로 ping할 수 있습니다.

```
ping 10.0.0.4
```

4개의 응답을 받게 됩니다. 해당 IP 주소가 아닌 가상 머신의 이름(*myVm1*)으로 ping하면 *myVm1*이 알 수 없는 호스트 이름이므로 ping이 실패합니다. Azure의 기본 이름 확인은 다른 가상 네트워크의 가상 머신 간이 아니라 동일한 가상 네트워크의 가상 머신 간에 작동합니다. 여러 가상 네트워크에서 이름을 확인하려면 [자체 DNS 서버를 배포](virtual-networks-name-resolution-for-vms-and-role-instances.md)하거나 [Azure DNS 개인 도메인](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용해야 합니다.

*myVm1* 및 *myVm2*에 대한 RDP 세션 연결을 모두 끊습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>전역 가상 네트워크 피어링 미리 보기에 등록**

동일한 지역의 가상 네트워크 피어링은 일반 공급됩니다. 서로 다른 지역에서 가상 네트워크를 피어링하는 기능은 현재 미리 보기 상태입니다. 사용 가능한 지역에 대해서는 [가상 네트워크 업데이트](https://azure.microsoft.com/updates/?product=virtual-network)를 참조하세요. 여러 지역에 걸쳐 가상 네트워크를 피어링하려면 먼저 (피어링하려는 각 가상 네트워크가 있는 구독 내에서) 다음 단계를 완료하여 미리 보기에 등록해야 합니다.

1. 다음 명령을 입력하여 피어링하려는 각 가상 네트워크가 미리 보기를 위해 있는 구독을 등록합니다.

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    이전 명령을 입력한 후에 받은 **RegistrationState** 출력이 두 구독에 대해 **Registered**가 되기 전에 다른 지역의 가상 네트워크를 피어링하려고 하면 피어링이 실패합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크 피어링으로 두 네트워크를 연결하는 방법을 배웠습니다. VPN을 통해 [자신의 컴퓨터를 가상 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하고, 가상 네트워크 또는 피어링된 가상 네트워크의 리소스와 상호 작용할 수 있습니다.

재사용이 가능한 스크립트에 대한 스크립트 샘플을 계속 진행하여 가상 네트워크 문서에 설명된 많은 태스크를 완료할 수 있습니다.

> [!div class="nextstepaction"]
> [가상 네트워크 스크립트 예제](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
