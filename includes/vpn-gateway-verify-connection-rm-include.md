**가상 네트워크 게이트웨이** **>** ***게이트웨이 이름 클릭*** **>** **설정** **>** **연결**로 이동하여 Azure 포털에서 VPN 연결을 확인할 수 있습니다. 연결의 이름을 선택하여 연결에 대한 자세한 내용을 볼 수 있습니다. 아래 예제에서 연결되어 있지 않고 이를 통해 흐르는 데이터도 없습니다.


![연결 확인](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### PowerShell을 사용하여 연결을 확인하려면

`Get-AzureRmVirtualNetworkGatewayConnection –Debug`을 사용하여 연결이 성공했는지 확인하는 것도 가능합니다. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용할 수 있습니다. 메시지가 표시되면 모두 실행하기 위해 'A'를 선택합니다.

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