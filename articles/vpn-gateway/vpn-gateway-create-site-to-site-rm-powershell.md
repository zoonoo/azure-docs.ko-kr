<properties
   pageTitle="Azure 리소스 관리자 및 PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기 | Microsoft Azure"
	description="Azure 리소스 관리자 및 PowerShell을 사용하여 가상 네트워크에서 온-프레미스 위치로 사이트 간 VPN 연결 만들기"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""
	tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/21/2015"
	ms.author="cherylmc"/>

# Azure 리소스 관리자 및 PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


이 항목에서는 Azure 리소스 관리자 가상 네트워크와 온-프레미스 네트워크에 대한 사이트 간 VPN 연결을 만드는 과정을 안내합니다.

Azure에는 현재 클래식 배포 모델 및 Azure 리소스 관리자 배포 모델의 두 가지 배포 모델이 있습니다. 사이트 간 설정은 가상 네트워크 배포에 사용된 모델에 따라 달라집니다. 이러한 지침은 리소스 관리자에 적용됩니다. 클래식 배포 모델을 사용하여 사이트 간 VPN 게이트웨이 연결을 만들려는 경우 [관리 포털에서 사이트 간 VPN 연결 만들기](vpn-gateway-site-to-site-create.md)를 참조하세요.


## 시작하기 전에

시작하기 전에 다음이 있는지 확인합니다.

- 호환되는 VPN 장치(및 구성할 수 있는 사람). [VPN 장치 정보](vpn-gateway-vpn-devices.md)를 참조하세요.
- VPN 장치에 대한 외부 연결 공용 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
- 최신 버전의 Azure PowerShell cmdlet. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 Windows PowerShell 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다. 
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	

## 구독에 연결 


PowerShell 콘솔을 열고 Azure 리소스 관리자 모드로 전환합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

		Add-AzureAccount

Select-AzureSubscription을 사용하여 사용하려는 구독에 연결합니다.

		Select-AzureSubscription "yoursubscription"

ARM 모드로 전환합니다. 이렇게 하면 모드를 전환하여 ARM cmdlet을 사용할 수 있습니다.

		Switch-AzureMode -Name AzureResourceManager


## 가상 네트워크 및 게이트웨이 서브넷 만들기

- 게이트웨이 서브넷이 포함된 가상 네트워크가 이미 있는 경우 [로컬 사이트 추가](#add-your-local-site)로 바로 이동할 수 있습니다. 
- 가상 네트워크가 있고 VNet에 게이트웨이 서브넷을 추가하려는 경우 [VNet에 게이트웨이 서브넷 추가](#gatewaysubnet)를 참조하세요.

### 가상 네트워크 및 게이트웨이 서브넷을 만들려면

아래 샘플을 사용하여 가상 네트워크 및 게이트웨이 서브넷을 만듭니다. 사용자 고유의 값으로 대체합니다.

먼저 리소스 그룹을 만듭니다.

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

그런 다음 가상 네트워크를 만듭니다. 아래 샘플은 *testvnet*이라는 가상 네트워크와 *GatewaySubnet* 및 *Subnet1*이라는 두 서브넷을 만듭니다. 특히 *GatewaySubnet*이라는 서브넷을 만드는 것이 중요합니다. 다른 이름을 지정하는 경우 연결 구성이 실패합니다.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>VNet에 게이트웨이 서브넷을 추가하려면

이미 기존 가상 네트워크가 있으며 게이트웨이 서브넷을 추가하려는 경우 아래 샘플을 사용하여 게이트웨이 서브넷을 만들 수 있습니다. 게이트웨이 서브넷의 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하는 경우 VPN 구성이 예상대로 작동하지 않습니다.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 로컬 사이트 추가

가상 네트워크에서 *로컬 사이트*는 일반적으로 온-프레미스 위치를 가리킵니다. Azure에서 참조할 수 있는 이름을 사이트에 지정합니다.

또한 로컬 사이트에 대한 주소 공간 접두사를 지정합니다. Azure는 지정된 IP 주소 접두사를 사용하여 로컬 사이트로 보낼 트래픽을 식별합니다. 즉, 로컬 사이트에 연결하려는 각 주소 접두사를 지정해야 합니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다. 아래 PowerShell 샘플을 사용하여 로컬 사이트를 지정합니다.

	
- *GatewayIPAddress*는 온-프레미스 VPN 장치의 IP 주소입니다. VPN 장치는 NAT 뒤에 배치할 수 없습니다. 
- *AddressPrefix*는 온-프레미스 주소 공간입니다.

이 예제를 사용하여 로컬 사이트에 단일 주소 접두사를 추가합니다.

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

로컬 사이트에 여러 주소 접두사를 추가하려는 경우 이 예제를 사용합니다.

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


이미 만든 로컬 사이트에 주소 접두사를 더 추가하려면 아래 예제를 사용합니다.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


로컬 사이트에서 주소 접두사를 제거하려면 아래 예제를 사용합니다. 더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 접두사 20.0.0.0/24(이전 예제)가 더 이상 필요하지 않으므로 로컬 사이트를 업데이트하고 해당 접두사를 제외합니다.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')


## VNet 게이트웨이에 대한 공용 IP 주소 요청

다음에는 Azure VNet VPN 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 이 주소는 VPN 장치에 할당되는 것과 동일한 IP 주소가 아니라 Azure VPN 게이트웨이 자체에 할당됩니다. 사용하려는 IP 주소를 지정할 수 없습니다. 동적으로 게이트웨이에 할당됩니다. 게이트웨이에 연결할 온-프레미스 VPN 장치를 구성할 때 이 IP 주소를 사용합니다.

아래 PowerShell 샘플을 사용합니다. 이 주소의 할당 방법은 동적이어야 합니다.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 게이트웨이 IP 주소 지정 구성 만들기

게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 아래 샘플을 사용하여 게이트웨이 구성을 만듭니다.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## 게이트웨이 만들기

이 단계에서는 가상 네트워크 게이트웨이를 만듭니다. 다음 값을 사용합니다.

- 게이트웨이 형식은 *Vpn*입니다.
- VpnType은 RouteBased*(일부 설명서에서는 동적 게이트웨이라고도 함) 또는 *정책 기반*(일부 설명서에서는 정적 게이트웨이라고도 함)일 수 있습니다. VPN 게이트웨이 형식에 대한 자세한 내용은 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md)를 참조하세요. 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## VPN 장치 구성

이제 온-프레미스 VPN 장치를 구성하기 위한 가상 네트워크 게이트웨이의 공용 IP 주소가 필요합니다. 특정 구성 정보는 장치 제조업체에 문의하세요. 또한 자세한 내용은 [VPN 장치](http://go.microsoft.com/fwlink/p/?linkid=615099)를 참조하세요.

가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 다음 샘플을 사용합니다.

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## VPN 연결 만들기

가상 네트워크 게이트웨이와 VPN 장치 사이에 사이트 간 VPN 연결을 만들겠습니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 장치 구성에 사용한 값과 일치해야 합니다.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

잠시 후에 연결이 설정됩니다. 지금은 리소스 관리자를 사용하여 만든 사이트 간 VPN 연결이 포털에 표시되지 않습니다.


## 다음 단계

가상 네트워크에 가상 컴퓨터를 추가합니다. [가상 컴퓨터를 만듭니다](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->