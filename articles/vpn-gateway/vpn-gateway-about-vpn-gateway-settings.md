---
title: "크로스-프레미스 Azure 연결에 대한 VPN 게이트웨이 설정 | Microsoft Docs"
description: "Azure Virtual Network 게이트웨이의 VPN Gateway 설정에 대해 알아봅니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b902d2e79633959a6f76ddd45b1193177b0e8465
ms.openlocfilehash: 1ac5a78c8d9419e4c641bf66f8dac7aa8cbcd179


---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN Gateway 구성 설정 정보
VPN Gateway는 공용 연결을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하는 가상 네트워크 게이트웨이의 유형입니다. 또한 VPN Gateway를 사용하여 Azure 백본에 있는 가상 네트워크 간에 트래픽을 전송할 수 있습니다.

VPN 게이트웨이 연결은 각각이 구성 가능한 설정을 포함하는 여러 리소스의 구성에 따라 좌우됩니다. 이 문서의 섹션에서는 Resource Manager 배포 모델에서 생성된 가상 네트워크의 VPN Gateway와 관련된 리소스 및 설정에 대해 설명합니다. [VPN Gateway 정보](vpn-gateway-about-vpngateways.md) 문서에서 각 연결 솔루션에 대한 설명 및 토폴로지 다이어그램을 찾을 수 있습니다.  

## <a name="a-namegwtypeagateway-types"></a><a name="gwtype"></a>게이트웨이 유형
가상 네트워크마다 각 유형의 가상 네트워크 게이트웨이를 하나씩만 포함할 수 있습니다. 가상 네트워크 게이트웨이를 만들 때 게이트웨이 유형이 구성에 정확한지 확인해야 합니다.

-GatewayType에 사용 가능한 값은 다음과 같습니다. 

* Vpn
* Express 경로

VPN Gateway에는 `-GatewayType` *Vpn*이 필요합니다.  

예:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased


## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>게이트웨이 SKU
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>게이트웨이 SKU 구성
####<a name="specifying-the-gateway-sku-in-the-azure-portal"></a>Azure 포털에서 게이트웨이 SKU 지정

Azure Portal을 사용하여 Resource Manager 가상 네트워크 게이트웨이를 만드는 경우 드롭다운을 사용하여 게이트웨이 SKU를 선택할 수 있습니다. 표시되는 옵션은 선택한 게이트웨이 형식 및 선택한 VPN 형식에 해당합니다.

예를 들어 게이트웨이 형식 'VPN' 및 VPN 형식 '정책 기반'을 선택하면 PolicyBased VPN에 대해 사용할 수 있는 유일한 SKU인 '기본' SKU만 표시됩니다. '경로 기반'을 선택하는 경우 기본, 표준 및 HighPerformance SKU 중에서 선택할 수 있습니다. 

####<a name="specifying-the-gateway-sku-using-powershell"></a>PowerShell을 사용하여 게이트웨이 SKU 지정

다음 PowerShell 예제에서는 `-GatewaySku` 를 *표준*으로 지정합니다.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

####<a name="changing-a-gateway-sku"></a>게이트웨이 SKU 변경

게이트웨이 SKU를 좀 더 강력한 SKU로 업그레이드하려면(기본/표준에서 HighPerformance로) `Resize-AzureRmVirtualNetworkGateway` PowerShell cmdlet을 사용할 수 있습니다. 또한 이 cmdlet을 사용하여 게이트웨이 SKU 크기를 다운그레이드할 수도 있습니다.

다음 PowerShell 예제에서는 HighPerformance로 크기가 조정되는 게이트웨이 SKU를 보여 줍니다.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>게이트웨이 SKU 및 형식별 예상 총 처리량
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="a-nameconnectiontypeaconnection-types"></a><a name="connectiontype"></a>연결 유형
Resource Manager 배포 모델에서 각 구성이 작동하려면 특정 가상 네트워크 게이트웨이 연결 유형이 필요합니다. `-ConnectionType` 에 대해 사용 가능한 Resource Manager PowerShell 값은 다음과 같습니다.

* IPsec
* Vnet2Vnet
* Express 경로
* VPNClient

다음 PowerShell 예제에서는 *IPsec*연결 유형이 필요한 S2S 연결을 만듭니다.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="a-namevpntypeavpn-types"></a><a name="vpntype"></a>VPN 유형
VPN Gateway 구성에 대한 가상 네트워크 게이트웨이 만들 때 VPN 유형을 지정해야 합니다. 선택하는 VPN 유형은 만들려는 연결 토폴로지에 따라 달라집니다. 예를 들어 P2S 연결에는 RouteBased VPN 유형이 필요합니다. 또한 VPN 유형은 사용하는 하드웨어에 따라서도 달라질 수 있습니다. S2S 구성에는 VPN 장치가 필요합니다. 일부 VPN 장치는 특정 VPN 유형을 지원합니다.

선택하는 VPN 유형은 만들려는 솔루션에 대한 연결 요구 사항을 모두 충족해야 합니다. 예를 들어 동일한 가상 네트워크에 대해 S2S VPN Gateway 연결 및 P2S VPN Gateway 연결을 만들려는 경우 P2S에는 RouteBased VPN 유형이 필요하기 때문에 VPN 유형 *RouteBased* 를 사용해야 합니다. VPN 장치가 RouteBased VPN 연결을 지원하는지 확인해야 합니다. 

가상 네트워크 게이트웨이를 만든 후에는 VPN 유형을 변경할 수 없습니다. 가상 네트워크 게이트웨이를 삭제하고 새로 만들어야 합니다. 두 가지 VPN 유형이 있습니다.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

다음 PowerShell 예제에서는 `-VpnType` 를 *RouteBased*로 지정합니다. 게이트웨이를 만들 때 -VpnType이 구성에 정확한지 확인해야 합니다. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

## <a name="a-namerequirementsagateway-requirements"></a><a name="requirements"></a>게이트웨이 요구 사항
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="a-namegwsubagateway-subnet"></a><a name="gwsub"></a>게이트웨이 서브넷
VNet의 가상 네트워크 게이트웨이를 구성하기 위해 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 IP 주소를 포함합니다. 게이트웨이 서브넷이 제대로 작동하려면 이름을 *GatewaySubnet* 으로 지정해야 합니다. 이 이름을 사용하면 Azur에서 해당 게이트웨이에 이 서브넷을 사용해야 한다는 것을 알게 됩니다.

게이트웨이 서브넷을 만드는 경우 서브넷이 포함하는 IP 주소의 수를 지정합니다. 게이트웨이 서브넷의 IP 주소는 게이트웨이 서비스에 할당됩니다. 일부 구성은 게이트웨이 서비스에 다른 구성보다 더 많은 IP 주소를 할당해야 합니다. 이후 성장 및 새로운 연결 구성이 추가될 가능성에 대비하여 게이트웨이 서브넷에 IP 주소가 충분히 포함되어 있는지 확인하는 것이 좋습니다. 따라서 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /28 이상으로 만드는 것이 좋습니다(/28, /27, /26등). 만들려는 구성에 대한 요구 사항을 검토하고 가지고 있는 게이트웨이 서브넷이 해당 요구 사항을 충족하는지 확인하세요.

다음 Resource Manager PowerShell 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여 줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namelngalocal-network-gateways"></a><a name="lng"></a>로컬 네트워크 게이트웨이
VPN Gateway 구성을 만들 때 로컬 네트워크 게이트웨이는 종종 온-프레미스 위치를 나타냅니다. 클래식 배포 모델에서 로컬 네트워크 게이트웨이는 로컬 사이트라고 했습니다. 

로컬 네트워크 게이트웨이에 이름인 온-프레미스 VPN 장치의 공용 IP 주소를 지정하고 온-프레미스 위치에 있는 주소 접두사를 지정합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다. VPN Gateway 연결을 사용하는 VNet-VNet 구성에 대해서도 로컬 네트워크 게이트웨이를 지정합니다.

다음 PowerShell 예제에서는 새 로컬 네트워크 게이트웨이 만듭니다.

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

로컬 네트워크 게이트웨이 설정을 수정해야 하는 경우도 있습니다. 예를 들어 주소 범위를 추가 또는 수정할 경우 또는 VPN 장치의 IP 주소가 변경될 때가 여기에 해당합니다. 클래식 VNet의 경우 로컬 네트워크 페이지의 클래식 포털에서 이러한 설정을 변경할 수 있습니다. Resource Manager의 경우 [PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정](vpn-gateway-modify-local-network-gateway.md)을 참조하세요.

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API 및 PowerShell cmdlet
추가 기술 리소스 및 VPN Gateway 구성에 대해 REST API와 PowerShell cmdlet을 사용할 때의 특정 구문 요구 사항에 대해서는 다음 페이지를 참조하세요.

| **클래식** | **리소스 관리자** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>다음 단계
사용 가능한 연결 구성에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md) 를 참조하세요. 




<!--HONumber=Feb17_HO2-->


