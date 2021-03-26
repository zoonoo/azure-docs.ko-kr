---
title: 'Azure Express 경로: Azure PowerShell을 사용 하 여 IPv6 지원 추가'
description: Azure PowerShell를 사용 하 여 Azure 배포에 연결 하기 위해 IPv6 지원을 추가 하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 7a9ac98a9566986767016720fda245712197b27f
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566543"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Azure PowerShell를 사용 하 여 개인 피어 링에 대 한 IPv6 지원 추가 (미리 보기)

이 문서에서는 Azure PowerShell를 사용 하 여 Azure에서 Express 경로를 통해 Azure의 리소스에 연결 하기 위해 IPv6 지원을 추가 하는 방법을 설명 합니다.

> [!Note]
> 이 기능은 현재 [가용성 영역를 사용 하 여 Azure 지역](../availability-zones/az-region.md#azure-regions-with-availability-zones)에서 미리 보기에 사용할 수 있습니다. 따라서 Express 경로 회로를 피어 링 위치를 사용 하 여 만들 수 있지만 연결 하는 IPv6 기반 배포는 가용성 영역 있는 지역에 있어야 합니다.

## <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>공개 미리 보기 등록
IPv6 지원을 추가 하기 전에 먼저 구독을 등록 해야 합니다. 등록 하려면 Azure PowerShell를 통해 다음을 수행 하세요.
1.  Azure에 로그인하고 구독을 선택합니다. Express 경로 회로를 포함 하는 구독 및 Azure 배포를 포함 하는 구독 (서로 다른 경우)에 대해이 작업을 수행 해야 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 다음 명령을 사용 하 여 공개 미리 보기에 대 한 구독 등록을 요청 합니다.
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

그러면 2-3 영업일 이내에 Express 경로 팀에서 요청을 승인 하 게 됩니다.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Express 경로 회로에 IPv6 개인 피어 링 추가

1. [Express 경로 회로를 만들거나](./expressroute-howto-circuit-arm.md) 기존 회로를 사용 합니다. **AzExpressRouteCircuit** 명령을 실행 하 여 회로를 검색 합니다.

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. **AzExpressRouteCircuitPeeringConfig** 를 실행 하 여 회로에 대 한 개인 피어 링 구성을 검색 합니다.

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. 기존 IPv4 개인 피어 링 구성에 IPv6 개인 피어 링을 추가 합니다. 기본 링크 및 보조 링크에 대해 소유 하 고 있는/126 IPv6 서브넷을 제공 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.

    > [!Note]
    > 피어 ASN 및 VlanId는 IPv4 개인 피어 링 구성에 있는 것과 일치 해야 합니다.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. 구성이 성공적으로 저장 된 후 **AzExpressRouteCircuit** 명령을 실행 하 여 회로를 다시 가져옵니다. 응답은 다음 예제와 유사 하 게 표시 됩니다.

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대 한 연결 업데이트

에서 IPv6 개인 피어 링을 사용 하려는 가용성 영역 있는 지역에 Azure 리소스의 기존 환경이 있는 경우 아래 단계를 수행 합니다.

1. Express 경로 회로가 연결 된 가상 네트워크를 검색 합니다.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 가상 네트워크에 IPv6 주소 공간을 추가 합니다.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. 게이트웨이 서브넷에 IPv6 주소 공간을 추가 합니다. 게이트웨이 IPv6 서브넷은/64 이상 이어야 합니다.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. 기존 영역 중복 게이트웨이가 있는 경우 다음을 실행 하 여 IPv6 연결을 사용 하도록 설정 합니다. 그렇지 않으면 영역 중복 SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용 하 여 [가상 네트워크 게이트웨이를 만듭니다](./expressroute-howto-add-gateway-resource-manager.md) .

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>새 가상 네트워크에 대 한 연결 만들기

IPv6 개인 피어 링을 사용 하 여 가용성 영역 있는 지역에서 새 Azure 리소스 집합에 연결 하려는 경우 아래 단계를 수행 합니다.

1. IPv4 및 IPv6 주소 공간을 모두 사용 하 여 이중 스택 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 참조 하세요.

2. [이중 스택 게이트웨이 서브넷을 만듭니다](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. 영역 중복 SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용 하 여 [가상 네트워크 게이트웨이를 만듭니다](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) . FastPath를 사용 하려는 경우 ErGw3AZ를 사용 합니다 (이는 Express 경로 직접를 사용 하는 회로에만 사용할 수 있음).

4. [Express 경로 회로에 가상 네트워크를 연결](./expressroute-howto-linkvnet-arm.md)합니다.

## <a name="limitations"></a>제한 사항
I p v 6은 가용성 영역 있는 지역에서 배포에 연결 하는 데 사용할 수 있지만 다음과 같은 사용 사례를 지원 하지 않습니다.

* AZ Express 경로 게이트웨이 SKU를 통해 Azure의 배포에 연결
* 비 AZ regions의 배포에 대 한 연결
* Express 경로 회로 간의 Global Reach 연결
* 가상 WAN과 함께 Express 경로 사용
* 비-Express 경로 직접 회로가 포함 된 FastPath
* 다음 피어 링 위치에 회로가 있는 FastPath: Dubai
* VPN Gateway과 함께 사용

## <a name="next-steps"></a>다음 단계

Express 경로 문제를 해결 하려면 다음 문서를 참조 하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)