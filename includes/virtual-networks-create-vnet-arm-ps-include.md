## PowerShell을 사용하여 VNet을 만드는 방법
PowerShell을 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](../articles/powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.
	
2. 필요에 따라 아래와 같이 새 리소스 그룹을 만듭니다. 이 시나리오의 경우 이름이 *TestRG* 인 리소스 그룹을 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../articles/resource-group-overview.md)를 참조하세요.

		New-AzureRmResourceGroup -Name TestRG -Location centralus

	예상 출력:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

3. 아래와 같이 이름이 *TestVNet* 인 새 VNet을 만듭니다.

		New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	예상 출력:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag           		: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState      	: Succeeded
		Tags                   	: 
		AddressSpace           	: {
		                           "AddressPrefixes": [
		                             "192.168.0.0/16"
		                           ]
                         		 }
		DhcpOptions            	: {}
		Subnets                	: []
		VirtualNetworkPeerings 	: []

4. 아래와 같이 가상 네트워크 개체를 변수에 저장합니다.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP] **$vnet = New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**을 실행하여 3단계와 4단계를 결합할 수 있습니다.

5. 아래와 같이 새 VNet 변수에 서브넷을 추가합니다.

		Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	예상 출력:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {}
		Subnets         	: [
			                      {
			                        "Name": "FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24"
			                      }
			                    ]
		VirtualNetworkPeerings 	: []

6. 만들려는 각 서브넷에 대해 위의 5단계를 반복합니다. 아래 명령은 이 시나리오에 대해 *BackEnd* 서브넷을 만듭니다.

		Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. 서브넷을 만들 수 있지만 현재는 위의 4단계에서 만드는 VNet을 검색하기 위해 사용하는 지역 변수에만 있습니다. Azure에 변경 내용을 저장하려면 아래와 같이 **Set-AzureRMVirtualNetwork** cmdlet을 실행합니다.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet	
		
	예상 출력:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {
			                      "DnsServers": []
			                    }
		Subnets           	: [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      },
			                      {
			                        "Name": "BackEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
			                        "AddressPrefix": "192.168.2.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      }
			                    ]
		VirtualNetworkPeerings : []

<!---HONumber=AcomDC_0831_2016-->