<properties 
   pageTitle="VPN 게이트웨이 설정 정보 | Microsoft Azure"
   description="Azure 가상 네트워크용 VPN 게이트웨이 설정에 대해 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="cherylmc" />

# VPN 게이트웨이 설정 정보

VPN 게이트웨이는 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용되는 설정의 컬렉션입니다. 또한 VPN 게이트웨이를 사용하여 Azure 내에서 VNet 간에 트래픽을 전송할 수 있습니다. 이 문서의 섹션에서는 VPN 게이트웨이와 관련된 설정을 설명합니다.

경우에 따라 연결 다이어그램을 사용하여 사용 가능한 구성을 볼 수 있습니다. 이 문서의 [VPN 게이트웨이 연결 정보](vpn-gateway-topology.md) 섹션에서 각 구성을 배포하는 방법에 대한 다이어그램을 찾을 수 있습니다.


## <a name="gwsku"></a>게이트웨이 SKU

VPN 게이트웨이 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 게이트웨이 SKU는 Express 경로 및 VPN 게이트웨이 유형에 모두 적용됩니다. 가격은 게이트웨이 SKU마다 다릅니다. 가격에 대한 자세한 내용은 [VPN 게이트웨이 가격](https://azure.microsoft.com/pricing/details/vpn-gateway/)을 참조하세요. Express 경로에 대한 자세한 내용은 [Express 경로 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.

세 가지 VPN 게이트웨이 SKU가 있습니다.

- Basic
- Standard
- HighPerformance

다음 PowerShell 예제에서는 `-GatewaySku`를 *표준*으로 지정합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>SKU 및 게이트웨이 유형에서 예상된 총 처리량


다음 테이블에서는 게이트웨이 형식과 예상 총 처리량을 보여 줍니다. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>게이트웨이 유형

게이트웨이 형식은 게이트웨이를 연결하는 방법을 지정하며 Resource Manager 배포 모델에 대한 필수 구성 설정입니다. VPN에 대한 라우팅의 유형을 지정하는 VPN 유형과 게이트웨이 유형을 혼동하지 마세요. `-GatewayType`에 사용 가능한 값은 다음과 같습니다.

- Vpn
- Express 경로


이 PowerShell은 Resource Manager 배포 모델에 대한 이 예제는 -GatewayType을 *Vpn*으로 지정합니다. 게이트웨이를 만들 때 게이트웨이 유형이 구성에 정확한지 확인해야 합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>연결 유형

각 구성이 작동하려면 특정 연결 형식이 필요합니다. `-ConnectionType`에 대해 사용 가능한 Resource Manager PowerShell 값은 다음과 같습니다.

- IPsec
- Vnet2Vnet
- Express 경로
- VPNClient

다음 PowerShell 예제에서는 연결 형식 "IPsec"을 필요로 하는 S2S 연결을 만듭니다.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN 유형

각 구성이 작동하려면 특정 VPN 유형이 필요합니다. 구성을 만든 경우 연결에 필요한 VPN 형식을 선택합니다.

예를 들어 사이트 간(S2S) 및 지점 및 사이트 간(P2S)을 사용하여 VNet에 연결하려는 경우 두 구성 모두에 대한 연결 요구를 충족하는 VPN 형식을 사용해야 합니다. P2S 연결에는 경로 기반 VPN 형식(동적 라우팅 게이트웨이)이 필요하지만 S2S 연결은 경우에 따라 정책 기반 VPN 형식을 지원할 수 있습니다. 즉, 정책 기반 VPN 형식(정적 라우팅)을 사용하는 S2S 연결이 이미 있는 경우 경로 기반 VPN 형식(동적 라우팅)을 사용하는 게이트웨이를 다시 만들어 P2S를 지원해야 합니다.

두 가지 VPN 유형이 있습니다.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



Resource Manager 배포 모델에 대한 이 PowerShell 예제는 `-VpnType`을 *RouteBased*로 지정합니다. 게이트웨이를 만들 때 -VpnType이 구성에 정확한지 확인해야 합니다.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>게이트웨이 요구 사항

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>게이트웨이 서브넷

VPN 게이트웨이를 구성하려면 먼저 VNet에 대한 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷이 제대로 작동하려면 이름을 *GatewaySubnet*으로 지정해야 합니다. 이 이름을 통해 Azure는 이 서브넷이 게이트웨이에 사용됨을 알 수 있습니다.<BR>클래식 포털을 사용하는 경우 포털 인터페이스에서 게이트웨이 서브넷의 이름을 *게이트웨이*로 자동으로 지정합니다. 클래식 포털에서 게이트웨이 서브넷을 보는 것에만 해당됩니다. 이 경우에 서브넷은 *GatewaySubnet* 값으로 실제로 만들어지며 Azure 포털 및 PowerShell에서 이러한 방식으로 볼 수 있습니다.

게이트웨이 서브넷의 최소 크기는 전적으로 만들려는 구성에 따라 달라집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /28 이상으로 만드는 것이 좋습니다(/28, /27, /26등).

더 큰 게이트웨이 크기를 만드는 경우 게이트웨이 크기 제한에 대해 실행되지 않도록 방지합니다. 예를 들어 S2S 연결에 대해 게이트웨이 서브넷 크기가 /29인 가상 네트워크 게이트웨이를 만들었을 수 있습니다. 이제 S2S/Express 경로 공존 구성을 지정하려고 합니다. 해당 구성에는 게이트웨이 서브넷 최소 크기 /28이 필요합니다. 구성을 만들려면 연결에 대한 최소 요구 수준인 /28을 충족하도록 게이트웨이 서브넷을 수정해야 합니다.

다음 PowerShell 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] 연결에 오류를 발생시킬 수 있기 때문에 GatewaySubnet에 적용되는 NSG(네트워크 보안 그룹)가 없는지 확인합니다.


## <a name="lng"></a>로컬 네트워크 게이트웨이

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 클래식 배포 모델에서 로컬 네트워크 게이트웨이는 로컬 사이트라고 했습니다. 로컬 네트워크 게이트웨이에 이름인 온-프레미스 VPN 장치의 공용 IP 주소를 지정하고 온-프레미스 위치에 있는 주소 접두사를 지정합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다.

로컬 네트워크 게이트웨이 설정을 수정해야 하는 경우도 있습니다. 예를 들어 주소 범위를 추가 또는 수정할 경우 또는 VPN 장치의 IP 주소가 변경될 때가 여기에 해당합니다. 클래식 VNet의 경우 로컬 네트워크 페이지의 클래식 포털에서 이러한 설정을 변경할 수 있습니다. Resource Manager의 경우 [PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정](vpn-gateway-modify-local-network-gateway.md)을 참조하세요.

## <a name="resources"></a>REST API 및 PowerShell

추가 기술 리소스 및 REST API와 PowerShell을 사용할 때의 특정 구문 요구 사항에 대해서는 다음 페이지를 참조하세요.

|**클래식** | **리소스 관리자**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## 다음 단계

사용 가능한 연결 구성에 대한 자세한 내용은 [VPN 게이트웨이 연결](vpn-gateway-topology.md)을 참조하세요.







 

<!---HONumber=AcomDC_0824_2016-->