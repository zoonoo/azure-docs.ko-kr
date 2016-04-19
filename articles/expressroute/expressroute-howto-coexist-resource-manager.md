<properties
   pageTitle="Resource Manager 배포 모델에 대해 공존할 수 있는 Express 경로 및 사이트 간 VPN 연결 구성 | Microsoft Azure"
   description="이 문서에서는 Resource Manager 모델에 대해 공존할 수 있는 Express 경로와 사이트 간 VPN 연결을 구성하는 과정을 안내합니다."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charleywen"/>

# Resource Manager 배포 모델에 대한 Express 경로 및 사이트 간 공존 연결 구성

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - 클래식](expressroute-howto-coexist-classic.md)

사이트 간 VPN 및 Express 경로를 구성하는 기능이 있으면 여러 장점이 있습니다. 사이트 간 VPN을 Exress 경로에 대한 안전한 장애 조치(failover) 경로로 구성하거나 사이트 간 VPN을 사용하여 Express 경로를 통해 연결되지 않은 사이트에 연결할 수 있습니다. 이 문서에서는 두 시나리오 모두를 구성하는 단계를 설명합니다. 이 문서는 Resource Manager 배포 모델에 적용됩니다. 이 구성은 Azure 포털에서 사용할 수 없습니다.


**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

>[AZURE.IMPORTANT] Express 경로 회로는 아래 지침을 수행하기 전에 미리 구성되어야 합니다. 다음 단계를 수행하기 전에 [Express 경로 회로 만들기](expressroute-howto-circuit-arm.md) 및 [라우팅 구성](expressroute-howto-routing-arm.md)을 위해 지침을 수행했는지 확인합니다.

## 제한 및 제한 사항

- **통과 라우팅은 지원되지 않음:** 사이트 간 VPN을 통해 연결된 로컬 네트워크와 Express 경로를 통해 연결된 로컬 네트워크 간에는 Azure를 통해 라우트할 수 없습니다.
- **강제 터널링은 사이트 간 VPN 게이트웨이에서 사용할 수 없음:** Express 경로를 통해 온-프레미스 네트워크에 인터넷 바인딩된 트래픽을 모두 다시 "강제"할 수 있습니다. 
- **표준 또는 높은 성능 게이트웨이만:** Express 경로 게이트웨이 및 사이트 간 VPN 게이트웨이 모두에 표준 또는 높은 성능 게이트웨이를 사용해야 합니다. 게이트웨이 SKU에 대한 내용은 [게이트웨이 SKU](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.
- **경로 기반 VPN 게이트웨이 전용:** 경로 기반 VPN 게이트웨이를 사용해야 합니다. 경로 기반 VPN 게이트웨이에 대한 정보는 [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.
- **정적 경로 요구 사항:** 로컬 네트워크가 Express 경로 및 사이트 간 VPN 모두에 연결된 경우 로컬 네트워크에서 정적 경로를 구성하여 사이트 간 VPN 연결을 공용 인터넷에 라우팅해야 합니다.
- **Express 경로 게이트웨이를 먼저 구성해야 함:** 사이트 간 VPN 게이트웨이를 추가하기 전에 Express 경로 게이트웨이를 먼저 만들어야 합니다.


## 구성 디자인

### 사이트 간 VPN을 Express 경로에 대한 장애 조치(failover) 경로로 구성

Express 경로에 대한 백업으로 사이트 간 VPN 연결을 구성할 수 있습니다. Azure 개인 피어링 경로에 연결된 가상 네트워크에만 적용됩니다. 공용 Azure 및 Microsoft 피어링을 통해 액세스할 수 있는 서비스에 대한 VPN 기반 장애 조치 솔루션은 없습니다. Express 경로 회로는 항상 기본 링크입니다. Express 경로 회로가 실패하면 데이터는 사이트 간 VPN 경로를 통해 전송됩니다.

![공존](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### 사이트 간 VPN을 구성하여 Express 경로를 통해 연결되지 않은 사이트에 연결

일부 사이트는 사이트 간 VPN을 통해 Azure에 직접 연결하고 일부 사이트는 Express 경로를 통해 연결된 네트워크를 구성할 수 있습니다.

![공존](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] 가상 네트워크를 통과 라우터로 구성할 수 없습니다.

## 사용할 단계 선택

연결이 공존할 수 있도록 구성하기 위해 선택하는 서로 다른 두 절차가 있습니다. 연결할 기존 가상 네트워크가 있는지, 아니면 새 가상 네트워크를 만들 것인지에 따라 구성 절차를 선택합니다.


- VNet이 없어서 만들어야 하는 경우
	
	가상 네트워크가 아직 없는 경우 이 절차에서는 Resource Manager 배포 모델을 사용하여 새 가상 네트워크를 만들고 새 Express 경로 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 구성하려면 이 문서의 [새 가상 네트워크 및 공존 연결을 만들려면](#new) 섹션에 있는 단계를 따릅니다.

- 이미 Resource Manager 배포 모델 VNet이 있는 경우

	기존 사이트 간 VPN 또는 Express 경로에 연결된 가상 네트워크가 이미 있을 수 있습니다. [기존 VNet에 대해 공존 연결을 구성하려면](#add) 섹션에서는 게이트웨이를 삭제한 다음 새 Express 경로 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 새 연결을 만들 때 지정된 순서대로 단계를 완료해야 합니다. 게이트웨이 및 연결을 만들 때 다른 문서의 지침을 사용하지 마세요.

	이 절차에서 함께 사용할 수 있는 연결을 만들려면 게이트웨이를 삭제한 다음 공존할 수 있는 새 게이트웨이를 구성해야 합니다. 이 경우 게이트웨이 및 연결을 삭제하고 다시 만드는 동안 크로스-프레미스 연결을 위한 가동 중지 시간이 발생하지만 VM 또는 서비스를 새 가상 네트워크로 마이그레이션할 필요는 없습니다. VM 및 서비스는 그렇게 구성된 경우 게이트웨이를 구성하는 동안 부하 분산 장치를 통해 계속 통신할 수 있습니다.


## <a name="new"></a> 새 가상 네트워크 및 공존 연결을 만들려면

이 절차는 VNet 만들기를 안내하고 함께 사용하는 사이트 간 및 Express 경로 연결을 만듭니다.
	
1. 최신 버전의 Azure PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 익숙하던 cmdlet과는 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다.

2. 계정에 로그인하여 환경을 설정합니다.
	
		login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
		$location = "Central US"
		$resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. 게이트웨이 서브넷을 포함하여 가상 네트워크를 만듭니다. 가상 네트워크 구성에 대한 자세한 내용은 [Azure 가상 네트워크 구성](../virtual-network/virtual-networks-create-vnet-arm-ps.md)을 참조하세요.

	>[AZURE.IMPORTANT] 게이트웨이 서브넷은 /27 또는 더 짧은 접두사(예: /26 또는 /25)여야 합니다.
	
	새 VNet을 만듭니다.

		$vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

	서브넷을 추가합니다.

		Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	VNet 구성을 저장합니다.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Express 경로 게이트웨이를 만듭니다. Express 경로 게이트웨이 구성에 대한 자세한 내용은 [Express 경로 게이트웨이 구성](expressroute-howto-add-gateway-resource-manager.md)을 참조하세요. GatewaySKU는 *표준* 또는 *HighPerformance*여야 합니다.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		$gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Express 경로 게이트웨이를 Express 경로 회로에 연결합니다. 이 단계를 완료하면 Express 경로를 통해 온-프레미스 네트워크와 Azure 간의 연결이 설정됩니다. 링크 작업에 대한 자세한 내용은 [Vnet을 Express 경로에 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
		New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. 다음으로, 사이트 간 VPN 게이트웨이를 만듭니다. VPN 게이트웨이 구성에 대한 자세한 내용은 [VNet 간 연결 구성](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요. GatewaySKU는 *표준* 또는 *HighPerformance*여야 합니다. VpnType은 *RouteBased*여야 합니다.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. 로컬 사이트 VPN 게이트웨이 엔터티를 만듭니다. 이 명령은 온-프레미스 VPN 게이트웨이를 구성하지 않습니다. 대신, Azure VPN 게이트웨이를 연결할 수 있도록 공용 IP 주소 및 온-프레미스 주소 공간과 같은 로컬 게이트웨이 설정을 제공할 수 있게 해줍니다.
	>[AZURE.NOTE] 로컬 네트워크에 여러 경로가 있는 경우 모두 배열로 전달할 수 있습니다. $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")

		$localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. 새 Azure VPN 게이트웨이에 연결할 로컬 VPN 장치를 구성합니다. VPN 장치 구성에 대한 자세한 내용은 [VPN 장치 구성](../vpn-gateway/vpn-gateway-about-vpn-devices.md)을 참조하세요.

9. Azure의 사이트 간 VPN 게이트웨이를 로컬 게이트웨이에 연결합니다.

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a> 기존 VNet에 대한 공존 연결을 구성하려면

Express 경로 또는 사이트 간 VPN 연결을 통해 연결된 기존 가상 네트워크가 있는 경우 두 연결 모두를 기존 게이트웨이에 연결하려면 먼저 기존 게이트웨이를 삭제해야 합니다. 이 구성에서 작업하는 동안에는 로컬 프레미스에서 게이트웨이를 통한 가상 네트워크 연결이 손실됩니다.

**구성을 시작하기 전에:** 게이트웨이 서브넷 크기를 늘릴 수 있도록 가상 네트워크에 충분한 IP 주소가 남아 있는지 확인합니다. 충분한 IP 주소가 있어도 게이트웨이를 삭제하고 다시 만들어야 합니다. 공존 연결을 수용하기 위해 게이트웨이를 다시 만들어야 하기 때문입니다.

1. 최신 버전의 Azure PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. 이 구성에 사용할 cmdlet은 지금까지 익숙하던 cmdlet과는 약간 다를 수 있습니다. 다음 지침에 지정된 cmdlet을 사용해야 합니다. 

2. 기존 Express 경로 또는 사이트 간 VPN 게이트웨이를 삭제합니다.

		Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. 게이트웨이 서브넷을 삭제합니다.
		
		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
		Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. /27 이상인 게이트웨이 서브넷을 추가합니다.

		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	VNet 구성을 저장합니다.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. 이제 게이트웨이가 없는 VNet이 설정됩니다. 새 게이트웨이를 만들고 연결을 완료하기 위해 이전 단계에 위치한 [4단계 - Express 경로 게이트웨이 만들기](#gw)를 진행할 수 있습니다.

## VPN 게이트웨이에 지점 및 사이트 간 구성을 추가하려면
아래 단계에 따라 공존 설정에서 VPN 게이트웨이에 지점 및 사이트 간 구성을 추가할 수 있습니다.

1. VPN 클라이언트 주소 풀을 추가합니다. 

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. VPN 게이트웨이에 대한 Azure에 VPN 루트 인증서를 업로드합니다. 이 예에서는 다음 PowerShell cmdlet이 실행되는 로컬 컴퓨터에 루트 인증서가 저장되는 것으로 가정합니다.

		$p2sCertFullName = "RootErVpnCoexP2S.cer"
		$p2sCertMatchName = "RootErVpnCoexP2S"
		$p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
		if ($p2sCertToUpload.count -eq 1){
		    write-host "cert found"
		} else {
		    write-host "cert not found"
		    exit
		} 
		$p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)을 참조하세요.

## 다음 단계

Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

<!---HONumber=AcomDC_0413_2016-->