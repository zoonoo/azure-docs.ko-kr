---
title: '자습서 - Azure ExpressRoute: VNet에 게이트웨이 추가 - Azure PowerShell'
description: 이 자습서는 Azure PowerShell을 사용하여 이미 만들어진 ExpressRoute용 Resource Manager VNet에 VNet 게이트웨이를 추가하는 데 도움이 됩니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 120bfe2eefae3c1721073060231c6c2a1962b7c8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110277"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>자습서: PowerShell을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

이 자습서는 기존 VNet에 대한 VNet(가상 네트워크) 게이트웨이를 추가, 크기 조정 및 제거하는 데 도움이 됩니다. 이 구성의 단계는 ExpressRoute 구성에 대해 Resource Manager 배포 모델을 사용하여 만든 VNet에 적용됩니다. 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 게이트웨이 서브넷을 만듭니다.
> - 가상 네트워크 게이트웨이를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="configuration-reference-list"></a>구성 참조 목록

이 작업의 단계는 다음 구성 참조 목록의 값을 기반으로 VNet을 사용합니다. 추가 설정 및 이름도 이 목록에 설명되어 있습니다. 이 목록의 값을 기준으로 변수를 추가하지만 어느 단계에서도 이 목록을 직접 사용하지 않습니다. 목록을 복사하여 참조로 사용하고 값을 사용자 값으로 바꿀 수 있습니다.

| 설정                   | 값                                              |
| ---                       | ---                                                |
| Virtual Network 이름 | *TestVNet* |    
| 가상 네트워크 주소 공간 | *192.168.0.0/16* |
| 리소스 그룹 | *TestRG* |
| Subnet1 이름 | *FrontEnd* |   
| Subnet1 주소 공간 | *192.168.1.0/24* |
| Subnet1 이름 | *FrontEnd* |
| 게이트웨이 서브넷 이름 | *GatewaySubnet* |    
| 게이트웨이 서브넷 주소 공간 | *192.168.200.0/26* |
| 지역 | *미국 동부* |
| 게이트웨이 이름 | *GW* |   
| 게이트웨이 IP 이름 | *GWIP* |
| 게이트웨이 IP 구성 이름 | *gwipconf* |
| 유형 | *ExpressRoute* |
| 게이트웨이 공용 IP 이름  | *gwpip* |

> [!IMPORTANT]
> 프라이빗 피어링에 대한 IPv6 지원은 현재 **퍼블릭 미리 보기** 로 제공됩니다. IPv6 기반 개인 피어링이 구성된 ExpressRoute 회로에 가상 네트워크를 연결하려면 가상 네트워크가 이중 스택인지 확인하고 [여기](../virtual-network/ipv6-overview.md)에 설명된 지침을 따르세요.
> 
> 

## <a name="add-a-gateway"></a>게이트웨이를 추가합니다.

1. Azure에 연결하려면 `Connect-AzAccount`를 실행합니다.

1. 이 연습에 대한 변수를 선언합니다. 사용하려는 설정을 반영하도록 샘플을 편집합니다.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. 가상 네트워크 개체를 변수로 저장합니다.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Virtual Network에 게이트웨이 서브넷을 추가합니다. 게이트웨이 서브넷의 이름을 "GatewaySubnet"으로 지정해야 합니다. 게이트웨이 서브넷은 /27 이상(/26,/25 등)이어야 합니다. 16개의 ExpressRoute 회로를 게이트웨이에 연결하려는 경우 /26개 이상의 게이트웨이 서브넷을 **만들어야 합니다**.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    이중 스택 가상 네트워크를 사용하고 ExpressRoute를 통해 IPv6 기반 프라이빗 피어링을 사용하려는 경우 대신 이중 스택 게이트웨이 서브넷을 만듭니다.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. 구성을 설정합니다.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. 게이트웨이 서브넷을 변수로 저장합니다.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. 공용 IP 주소를 요청합니다. 게이트웨이를 만들기 전에 IP 주소를 요청합니다. 사용할 IP 주소를 지정할 수는 없으며 IP 주소는 동적으로 할당됩니다. 다음 구성 섹션에서 이 IP 주소를 사용합니다. AllocationMethod는 동적이어야 합니다.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. 게이트웨이에 대한 구성을 만듭니다. 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 이 단계에서 게이트웨이를 만들 때 사용되는 구성을 지정합니다. 다음 샘플을 사용하여 게이트웨이 구성을 만듭니다.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. 게이트웨이를 만듭니다. 이 단계에서는 **-GatewayType** 이 특히 중요합니다. 값 **ExpressRoute** 를 사용해야 합니다. 이러한 cmdlet을 실행한 후 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> ExpressRoute를 통해 IPv6 기반 프라이빗 피어링을 사용하려는 경우 **-GatewaySku** 에 대해 AZ SKU(ErGw1AZ, ErGw2AZ, ErGw3AZ)를 선택해야 합니다.
> 
> 

## <a name="verify-the-gateway-was-created"></a>게이트웨이가 만들어졌는지 확인합니다.
다음 명령을 사용하여 게이트웨이가 만들어졌는지 확인합니다.

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>게이트웨이 크기 조정
다양한 [게이트웨이 SKU](expressroute-about-virtual-network-gateways.md)가 있습니다. 다음 명령을 사용하여 언제든지 게이트웨이 SKU를 변경합니다.

> [!IMPORTANT]
> 이 명령은 UltraPerformance 게이트웨이에는 작동하지 않습니다. 게이트웨이를 UltraPerformance 게이트웨이로 변경하려면 먼저 기존 ExpressRoute 게이트웨이를 제거한 다음 새 UltraPerformance 게이트웨이를 만듭니다. 게이트웨이를 UltraPerformance 게이트웨이에서 다운그레이드하려면 먼저 UltraPerformance 게이트웨이를 제거한 후 새 게이트웨이를 만듭니다.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>리소스 정리
다음 명령을 사용하여 게이트웨이를 제거합니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. 

> [!div class="nextstepaction"]
> [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
