<properties
   pageTitle="Azure 리소스 관리자 및 PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기 | Microsoft Azure"
   description="이 문서에서는 리소스 관리자 모델을 사용하여 VNet을 만들고 S2S VPN 게이트웨이 연결을 사용하여 로컬 온-프레미스 네트워크에 연결하는 과정을 안내합니다. 사이트간 연결은 하이브리드 구성에 사용할 수 있습니다. 기존 로컬 사이트에 대한 IP 주소 접두사를 수정하는 추가 단계를 포함합니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="cherylmc"/>

# PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

이 문서에서는 Azure 리소스 관리자 배포 모델을 사용하여 가상 네트워크와 온-프레미스 네트워크에 대한 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 이 구성에 대한 다른 배포를 찾고 있다면 위의 탭을 사용하여 원하는 문서를 선택합니다. Vnet끼리 서로 연결하지만 온-프레미스에는 연결하지 않을 경우 [VNet간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.


**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

- 호환되는 VPN 장치(및 구성할 수 있는 사람). [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.

- VPN 장치에 대한 외부 연결 공용 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
	
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

## PowerShell 모듈 설치

연결을 구성하려면 최신 버전의 Azure 리소스 관리자 PowerShell cmdlet이 필요합니다.
	
[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## 1\. 구독에 연결 


리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

	Login-AzureRmAccount

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription 

사용할 구독을 지정합니다.

	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## 2\. 가상 네트워크 및 게이트웨이 서브넷 만들기

- 게이트웨이 서브넷이 포함된 가상 네트워크가 이미 있는 경우 **3단계 - 로컬 사이트 추가**로 바로 이동할 수 있습니다. 
- 가상 네트워크가 이미 있고 VNet에 게이트웨이 서브넷을 추가하려는 경우 [VNet에 게이트웨이 서브넷 추가](#gatewaysubnet)를 참조하세요.

### 가상 네트워크 및 게이트웨이 서브넷을 만들려면

아래 샘플을 사용하여 가상 네트워크 및 게이트웨이 서브넷을 만듭니다. 사용자 고유의 값으로 대체합니다.

먼저 리소스 그룹을 만듭니다.

	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

그런 다음 가상 네트워크를 만듭니다. 지정한 주소 공간이 온-프레미스 네트워크에 가지고 있는 주소 공간과 겹치지 않는지 확인하세요.

아래 샘플은 *testvnet*이라는 가상 네트워크와 *GatewaySubnet* 및 *Subnet1*이라는 두 서브넷을 만듭니다. 특히 *GatewaySubnet*이라는 서브넷을 만드는 것이 중요합니다. 다른 이름을 지정하는 경우 연결 구성이 실패합니다.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>VNet에 게이트웨이 서브넷을 추가하려면(선택 사항)

이 단계는 이전에 만든 VNet에 게이트웨이 서브넷을 추가해야 하는 경우에만 필요합니다.

이미 기존 가상 네트워크가 있으며 게이트웨이 서브넷을 추가하려는 경우 아래 샘플을 사용하여 게이트웨이 서브넷을 만들 수 있습니다. 게이트웨이 서브넷의 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하는 경우 서브넷이 만들어지지만 Azure에서 게이트웨이 서브넷으로 표시되지 않습니다.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

이제 구성을 설정합니다.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. 로컬 사이트 추가

가상 네트워크에서 *로컬 사이트*는 일반적으로 온-프레미스 위치를 가리킵니다. Azure에서 참조할 수 있는 이름을 사이트에 지정합니다.

또한 로컬 사이트에 대한 주소 공간 접두사를 지정합니다. Azure는 지정된 IP 주소 접두사를 사용하여 로컬 사이트로 보낼 트래픽을 식별합니다. 즉, 로컬 사이트에 연결하려는 각 주소 접두사를 지정해야 합니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다.

PowerShell 예제를 사용할 때는 다음 사항에 유의하세요.
	
- *GatewayIPAddress*는 온-프레미스 VPN 장치의 IP 주소입니다. VPN 장치는 NAT 뒤에 배치할 수 없습니다. 
- *AddressPrefix*는 온-프레미스 주소 공간입니다.

로컬 사이트에 단일 주소 접두사를 추가하려면:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

로컬 사이트에 여러 주소 접두사를 추가하려면:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### 로컬 사이트에 대한 IP 주소 접두사를 수정하려면

경우에 따라 로컬 사이트 접두사를 변경합니다. IP 주소 접두사를 수정하기 위해 수행하는 단계는 VPN 게이트웨이 연결을 만들었는지 여부에 따라 달라집니다. [로컬 사이트에 대한 IP 주소 접두사 수정](#to-modify-ip-address-prefixes-for-a-local-site)을 참조하세요.


## 4\. 게이트웨이에 대한 공용 IP 주소 요청

다음에는 Azure VNet VPN 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 이 주소는 VPN 장치에 할당되는 것과 동일한 IP 주소가 아니라 Azure VPN 게이트웨이 자체에 할당됩니다. 사용하려는 IP 주소를 지정할 수 없습니다. 동적으로 게이트웨이에 할당됩니다. 게이트웨이에 연결할 온-프레미스 VPN 장치를 구성할 때 이 IP 주소를 사용합니다.

아래 PowerShell 샘플을 사용합니다. 이 주소의 할당 방법은 동적이어야 합니다.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. 게이트웨이 IP 주소 지정 구성 만들기

게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 아래 샘플을 사용하여 게이트웨이 구성을 만듭니다.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. 게이트웨이 만들기

이 단계에서는 가상 네트워크 게이트웨이를 만듭니다. 게이트웨이 만들기는 완료하는 데는 다소 시간이 소요됩니다. 20분 이상 걸리기도 합니다.

다음 값을 사용합니다.

- 게이트웨이 형식은 *Vpn*입니다.
- VpnType은 RouteBased*(일부 설명서에서는 동적 게이트웨이라고도 함) 또는 *정책 기반*(일부 설명서에서는 정적 게이트웨이라고도 함)일 수 있습니다. VPN 게이트웨이 형식에 대한 자세한 내용은 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md)를 참조하세요. 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. VPN 장치 구성

이제 온-프레미스 VPN 장치를 구성하기 위한 가상 네트워크 게이트웨이의 공용 IP 주소가 필요합니다. 특정 구성 정보는 장치 제조업체에 문의하세요. 또한 자세한 내용은 [VPN 장치](http://go.microsoft.com/fwlink/p/?linkid=615099)를 참조하세요.

가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 다음 샘플을 사용합니다.

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. VPN 연결 만들기

가상 네트워크 게이트웨이와 VPN 장치 사이에 사이트 간 VPN 연결을 만들겠습니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 장치 구성에 사용한 값과 일치해야 합니다.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

잠시 후, 연결이 설정됩니다.

## 9\. VPN 연결 확인

지금은 리소스 관리자를 사용하여 만든 사이트 간 VPN 연결이 Preview 포털에 표시되지 않습니다. 하지만 *Get-AzureRmVirtualNetworkGatewayConnection –Debug*를 사용하여 연결이 성공했는지 확인할 수 있습니다. 향후에는 이에 대한 cmdlet이 제공되며 Preview 포털에서 연결을 확인하는 기능도 제공됩니다.

일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용할 수 있습니다. 메시지가 표시되면 모두 실행하기 위해 *A*를 선택합니다.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 cmdlet이 완료되면 스크롤하여 값을 확인합니다. 아래 예제에서는 연결 상태가 *연결됨*으로 표시되고 송/수신 바이트를 볼 수 있습니다.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## 로컬 사이트에 대한 IP 주소 접두사를 수정하려면

로컬 사이트에 대한 접두사를 변경해야 하는 경우 아래 지침을 사용합니다. VPN 게이트웨이 연결이 이미 작성되었는지 여부에 따라 두 집합의 지침이 제공됩니다.

### VPN 게이트웨이 연결 없이 접두사를 추가 또는 제거합니다.


- 생성한 로컬 사이트에 추가 주소 접두사를 **추가하지만** VPN 게이트웨이 연결이 아직 없는 경우 다음 예제를 사용합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- VPN 연결이 없는 로컬 사이트에서 주소 접두사를 **제거하려면** 다음 예제를 사용합니다. 더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 접두사 20.0.0.0/24(이전 예제)가 더 이상 필요하지 않으므로 로컬 사이트를 업데이트하고 해당 접두사를 제외합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### VPN 게이트웨이 연결이 있는 상태에서 접두사 추가 또는 제거

VPN 연결을 만들었고 로컬 사이트에 포함된 IP 주소 접두사를 추가 또는 제거하려면 다음 단계를 순서대로 수행해야 합니다. 게이트웨이를 제거하고 다시 빌드해야 하므로 VPN 연결에 가동 중지 시간이 발생합니다. 그러나 연결을 위한 IP 주소를 요청했기 때문에 이전에 사용한 값을 변경하려는 경우가 아니면 온-프레미스 VPN 라우터를 다시 구성할 필요가 없습니다.

 
1. 게이트웨이 연결을 제거합니다. 
2. 로컬 사이트에 대한 접두사를 수정합니다. 
3. 새 게이트웨이 연결을 만듭니다. 

다음 샘플을 지침으로 사용할 수 있습니다.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## 다음 단계

연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->