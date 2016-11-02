### <a name="noconnection"></a>접두사를 추가 또는 제거하는 방법 - VPN 게이트웨이 연결 없음

- 생성한 로컬 네트워크 게이트웨이에 추가 주소 접두사를 **추가하지만** 게이트웨이 연결이 아직 없는 경우 다음 예제를 사용합니다. 고유한 값으로 변경해야 합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- VPN 연결이 없는 로컬 네트워크 게이트웨이에서 주소 접두사를 **제거하려면** 다음 예제를 사용합니다. 더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 접두사 20.0.0.0/24(이전 예제)가 더 이상 필요하지 않으므로 로컬 네트워크 게이트웨이를 업데이트하고 해당 접두사를 제외합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>접두사를 추가 또는 제거하는 방법 - 기존 VPN 게이트웨이 연결

게이트웨이 연결을 만들었고 로컬 네트워크 게이트웨이에 포함된 IP 주소 접두사를 추가 또는 제거하려면 다음 단계를 순서대로 수행해야 합니다. 이로 인해 VPN 연결에 다소 간의 가동 중지 시간이 발생합니다. 접두사를 업데이트할 때 먼저 연결을 제거하고 접두사를 수정한 다음 새 연결을 만듭니다. 다음 예제에서 고유한 값으로 변경해야 합니다.

>[AZURE.IMPORTANT] VPN 게이트웨이는 삭제하지 않습니다. 삭제할 경우 다시 만들기 위해 이전 단계를 수행해야 할 뿐 아니라 온-프레미스 라우터를 새 설정으로 다시 구성해야 합니다.
 
1. 연결을 제거합니다.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 수정합니다.

	LocalNetworkGateway에 대한 변수를 설정합니다.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

	접두사를 수정합니다.

		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. 연결을 만듭니다. 이 예제에서는 IPsec 연결 형식을 구성합니다. 연결을 다시 만들 때 구성에 대해 지정된 연결 유형을 사용합니다. 추가 연결 형식은 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 페이지를 참조하세요.

 	VirtualNetworkGateway에 대한 변수를 설정합니다.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

	연결을 만듭니다. 이 샘플에서는 이전 단계에서 설정한 변수 $local을 사용합니다.


		New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
		-ResourceGroupName MyRGName -Location 'West US' `
		-VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
		-ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->