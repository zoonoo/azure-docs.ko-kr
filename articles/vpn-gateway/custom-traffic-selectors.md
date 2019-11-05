---
title: Azure VPN Gateway 연결에 대 한 사용자 지정 트래픽 선택기 | Microsoft Docs
description: VPN Gateway 연결에서 사용자 지정 트래픽 선택기를 사용 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512159"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>VPN Gateway 연결에 대 한 사용자 지정 트래픽 선택기

VPN gateway 연결에 사용자 지정 트래픽 선택기를 설정할 수 있습니다. 트래픽 선택기를 설정 하는 기능을 사용 하면 트래픽을 전송 하려는 VPN 터널의 양쪽에서 주소 공간 범위를 좁힐 수 있습니다. 사용자 지정 트래픽 선택기는 VNet 주소 공간이 클 때 특히 IPsec/IKE 협상에 서브넷 중 하나를 사용 하려는 경우에 유용 합니다.

새 연결을 만들거나 기존 연결에 대해 업데이트할 수 있는 사용자 지정 트래픽 선택기를 추가할 수 있습니다. `TrafficSelectorPolicies` 매개 변수는 트래픽 선택기의 배열로 구성 됩니다. 각 트래픽 선택기는 CIDR 형식의 로컬 및 원격 주소 범위 컬렉션을 포함 합니다.

## <a name="add-custom-traffic-selectors"></a>사용자 지정 트래픽 선택기 추가

다음 예에서는 PowerShell 명령을 사용 하 여 가상 네트워크 게이트웨이 연결에 대 한 사용자 지정 트래픽 선택기를 만드는 방법을 보여 줍니다. 가상 네트워크 게이트웨이 연결을 만드는 방법에 대 한 도움말 [은 사이트 간 연결 구성](vpn-gateway-create-site-to-site-rm-powershell.md)을 참조 하세요.

1. $Trafficselectorpolicy에서 *trafficselectorpolicies* 매개 변수에 대 한 값을 설정 합니다. 이 설정을 사용 하는 경우 *AzTrafficSelectorPolicy* 값은 배열에서 로컬 및 원격 주소 범위를 사용 합니다.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. 새 가상 네트워크 게이트웨이 연결을 만듭니다. 요구 사항에 대 한 값 매개 변수를 조정 합니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

' AzVirtualNetworkGatewayConnection '를 사용 하 여 기존 가상 네트워크 게이트웨이 연결에 대 한 사용자 지정 트래픽 선택기를 업데이트할 수도 있습니다. PowerShell 값은 [가상 네트워크 게이트웨이 연결](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

VPN 게이트웨이에 대 한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조 하세요.