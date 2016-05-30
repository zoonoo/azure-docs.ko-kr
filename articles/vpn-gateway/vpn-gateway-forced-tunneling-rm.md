<properties 
   pageTitle="리소스 관리자 배포 모델을 사용하여 사이트 간 연결에 대한 강제 터널링 구성 | Microsoft Azure"
   description="모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 '강제 적용'하는 방법입니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Azure Resource Manager 배포 모델을 사용하여 강제 터널링 구성

> [AZURE.SELECTOR]
- [PowerShell - 서비스 관리](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)

강제 터널링을 사용하면 검사 및 감사에 대한 사이트 간 VPN 터널을 통해 모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 "force"할 수 있습니다. 대부분의 엔터프라이즈 IT 정책에 있어서 중요한 보안 요구 사항입니다.

강제 터널링 없이 Azure의 VM에서 인터넷 바인딩된 트래픽은 항상 트래픽을 검사 또는 감사하도록 허용하는 옵션 없이 Azure 네트워크 인프라에서 직접 인터넷으로 트래버스합니다. 인증되지 않은 인터넷 액세스는 잠재적으로 정보 공개 또는 다른 유형의 보안 위반을 발생시킬 수 있습니다.

이 문서에서는 리소스 관리자 배포 모델을 사용하여 만든 가상 네트워크에 대한 강제 터널링을 구성하는 과정을 안내합니다.

**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**강제 터널링에 대한 배포 모델 및 도구**

두 배포 모델에서 다양한 도구를 사용하여 강제 터널링 연결을 구성할 수 있습니다. 자세한 내용은 아래 표를 참조하세요. 이 구성에 사용할 수 있게 된 새 문서, 새로운 배포 모델 및 추가 도구로 이 표를 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


## 강제 터널링 정보


다음 다이어그램에서는 강제 터널링 작동 방법을 보여 줍니다.

![강제 터널링](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

위의 예에서 프런트 엔드 서브넷은 강제 터널링되지 않았습니다. 프런트 엔드 서브넷에서 작업은 계속해서 인터넷에서 직접 고객의 요청을 수락하고 응답할 수 있습니다. 중간 계층 및 백 엔드 서브넷은 강제 터널링됩니다. 이러한 두 서브넷에서 인터넷으로의 모든 아웃바운드 연결은 S2S VPN 터널 중 하나를 통해 온-프레미스 사이트로 다시 force되거나 리디렉션됩니다.

이를 통해 필요한 다중 계층 서비스 아키텍처를 계속 사용하면서 Azure의 가상 컴퓨터 또는 클라우드 서비스에서 인터넷 액세스를 제한하고 검사할 수 있습니다. 가상 네트워크에 인터넷 연결 작업이 없는 경우 강제 터널링을 전체 가상 네트워크에 적용할 수 있는 옵션이 있습니다.

## 요구 사항 및 고려 사항

Azure에서 강제 터널링은 가상 네트워크 사용자 정의 경로를 통해 구성됩니다. 온-프레미스 사이트에 트래픽을 리디렉션하는 것은 Azure VPN 게이트웨이에 기본 경로로 표현됩니다. 사용자 정의 경로 및 가상 네트워크에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

- 각 가상 네트워크 서브넷에는 기본 제공 시스템 라우팅 테이블이 있습니다. 시스템 라우팅 테이블에는 다음 3개의 경로 그룹이 있습니다.

	- **로컬 VNet 경로:** 동일한 가상 네트워크에서 대상 VM으로 직접
	
	- **온-프레미스 경로:** Azure VPN 게이트웨이로
	
	- **기본 경로:** 인터넷으로 직접. 이전의 두 경로를 벗어나는 개인 IP 주소로 향하는 패킷은 삭제됩니다.

-  이 절차는 UDR(사용자 정의 경로)을 사용하여 라우팅 테이블을 만들어 기본 경로에 추가한 다음 라우팅 테이블을 VNet 서브넷에 연결하여 해당 서브넷에 강제 터널링을 사용할 수 있습니다.

- 강제 터널링은 경로 기반 VPN 게이트웨이가 있는 VNet에 연결되어야 합니다. 가상 네트워크에 연결된 크로스-프레미스 로컬 사이트 사이에서 "기본 사이트"를 설정해야 합니다.

- Express 경로 강제 터널링은 이 메커니즘을 통해 구성되지 않지만 대신 Express 경로 BGP 피어링 세션을 통해 기본 경로를 보급하여 활성화됩니다. 자세한 내용은 [Express 경로 설명서](https://azure.microsoft.com/documentation/services/expressroute/)를 참조하세요.

## 구성 개요

아래 절차를 사용하면 리소스 그룹과 VNet을 만들 수 있습니다. 그런 다음 VPN 게이트웨이를 만들고 강제 터널링을 구성합니다. 이 절차에서 가상 네트워크 "MultiTier-VNet"에는 3개의 서브넷(*프런트 엔드*, *중간 계층* 및 *백 엔드*)과 함께 4개의 프레미스 간 연결(*DefaultSiteHQ* 및 3개의 *분기*)이 있습니다.

절차 단계에서는 강제 터널링에 대한 기본 사이트 연결로 *DefaultSiteHQ*를 설정하고 중간 계층 및 백 엔드 서브넷을 구성하여 강제 터널링을 사용합니다.

	
## 시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

- 최신 버전(1.0 이상)의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.


## 강제 터널링 구성

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 이 cmdlet은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

		Login-AzureRmAccount 

2. Azure 구독 목록을 가져옵니다.

		Get-AzureRmSubscription

2. 사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
		
3. 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. 가상 네트워크를 만들고 서브넷을 지정합니다.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. 로컬 네트워크 게이트웨이를 만듭니다.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. 경로 테이블 및 경로 규칙을 만듭니다.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


7. 중간 계층 및 백 엔드 서브넷에 경로 테이블을 연결합니다.

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. 기본 사이트로 게이트웨이를 만듭니다. 이 단계에서 게이트웨이를 만들고 구성하기 때문에 완료되기까지 약간의 시간이 걸리며, 20분 이상 걸리는 경우도 있습니다. 콘솔에서는 몇 개의 cmdlet이 수행되는 것처럼 보이지만 백그라운드로 많은 작업이 진행되고 있습니다. GatewayDefaultSite는 강제 라우팅 구성 작업을 허용하는 cmdlet 매개 변수이므로 사용자는 이 단계를 건너뛰는 것을 원치 않을 것입니다. PowerShell 1.0 이상에서만 사용할 수 있습니다.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. 사이트 간 VPN 연결 설정

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_0518_2016-->