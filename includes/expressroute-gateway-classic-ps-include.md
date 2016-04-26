다음 작업을 수행하기 전에 VNet과 게이트웨이 서브넷을 먼저 만들어야 합니다. 자세한 내용은 [클래식 포털을 사용하여 가상 네트워크 구성](../articles/expressroute/expressroute-howto-vnet-portal-classic.md)을 참조하세요.

## 게이트웨이를 추가합니다.

다음 명령을 사용하여 게이트웨이를 만듭니다. 모든 값을 자신의 고유한 값으로 대체해야 합니다.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## 게이트웨이가 만들어졌는지 확인합니다.

아래 명령을 사용하여 게이트웨이가 만들어졌는지 확인합니다. 또한 이 명령은 다른 작업에 필요한 게이트웨이 ID를 검색합니다.

	Get-AzureVirtualNetworkGateway

## 게이트웨이 크기 조정

세 가지 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md)가 있습니다. 다음 명령을 사용하여 언제든지 게이트웨이 SKU를 변경합니다.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## 게이트웨이 제거

다음 명령을 사용하여 게이트웨이를 제거합니다.

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->