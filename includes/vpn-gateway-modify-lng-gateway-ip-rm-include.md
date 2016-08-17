게이트웨이 IP 주소를 수정하려면 `New-AzureRmVirtualNetworkGatewayConnection` cmdlet을 사용합니다. 로컬 네트워크 게이트웨이의 이름을 기존 이름과 동일하게 유지면 설정이 덮어쓰여 집니다. 이 경우에 "Set" cmdlet은 게이트웨이 IP 주소를 수정하도록 지원하지 않습니다.

### <a name="gwipnoconnection"></a>게이트웨이 IP 주소를 수정하는 방법 - 게이트웨이 연결 없음

아직 연결되지 않은 로컬 네트워크 게이트웨이에 대한 게이트웨이 IP 주소를 업데이트하려면 다음 예제를 사용합니다. 주소 접두사를 동시에 업데이트할 수도 있습니다. 지정한 설정은 기존 설정을 덮어씁니다. 로컬 네트워크 게이트웨이의 기존 이름을 사용해야 합니다. 이렇게 하지 않으면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

사용자 고유의 값을 대체하여 다음 샘플을 사용합니다.

	New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
	-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
	-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>게이트웨이 IP 주소를 수정하는 방법 - 기존 게이트웨이 연결

게이트웨이 연결이 이미 있는 경우 먼저 연결을 제거해야 합니다. 그런 다음 게이트웨이 IP 주소를 수정하고 새 연결을 다시 만들 수 있습니다. 이로 인해 VPN 연결에 다소 간의 가동 중지 시간이 발생합니다.


>[AZURE.IMPORTANT] VPN 게이트웨이는 삭제하지 않습니다. 삭제할 경우 다시 만들기 위해 이전 단계를 수행해야 할 뿐 아니라 온-프레미스 라우터를 새롭게 만든 게이트웨이에 할당할 IP 주소로 다시 구성해야 합니다.
 

1. 연결을 제거합니다. `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet을 사용하여 연결의 이름을 찾을 수 있습니다.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
		-ResourceGroupName MyRGName

2. GatewayIpAddress 값을 수정합니다. 이 경우에 필요하면 주소 접두사를 수정할 수도 있습니다. 그러면 기존 로컬 네트워크 게이트웨이 설정을 덮어쓰게 됩니다. 설정을 덮어쓰도록 수정하는 경우 로컬 네트워크 게이트웨이의 기존 이름을 사용합니다. 이렇게 하지 않으면 기존 게이트웨이를 수정하지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

		New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
		-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
		-GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. 연결을 만듭니다. 이 예제에서는 IPsec 연결 형식을 구성합니다. 연결을 다시 만들 때 구성에 대해 지정된 연결 유형을 사용합니다. 추가 연결 형식은 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 페이지를 참조하세요. VirtualNetworkGateway 이름을 가져오려면 `Get-AzureRmVirtualNetworkGateway` cmdlet을 실행할 수 있습니다.

	변수를 설정합니다.

		$local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		$vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

	연결을 만듭니다.
	
		New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
		-Location "West US" `
		-VirtualNetworkGateway1 $vnetgw `
		-LocalNetworkGateway2 $local `
		-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->