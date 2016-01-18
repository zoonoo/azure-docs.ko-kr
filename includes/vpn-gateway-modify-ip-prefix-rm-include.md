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

<!---HONumber=AcomDC_0107_2016-->