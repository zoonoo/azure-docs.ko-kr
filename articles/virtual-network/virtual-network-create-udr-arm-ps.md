<properties 
   pageTitle="PowerShell을 사용하여 리소스 관리자에서 라우팅 제어 및 가상 어플라이언스 사용 | Microsoft Azure"
   description="Azure PowerShell에서 라우팅 제어 및 가상 어플라이언스 사용 방법 알아보기"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="telmos" />

#PowerShell에서 사용자 정의 경로(UDR) 만들기

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 UDR을 만들](virtual-network-create-udr-classic-ps.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)을 배포하여 테스트 환경을 구축하고 **Azure에 배포**를 클릭한 다음 필요한 경우 기본 매개 변수 값을 바꾸고 포털의 지침을 따릅니다.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. 백 엔드 서브넷(192.168.2.0/24)으로 보내진 모든 트래픽을 **FW1** 가상 어플라이언스(192.168.0.4)로 라우팅하는 데 사용된 경로를 만듭니다.

		$route = New-AzureRouteConfig -Name RouteToBackEnd `
		    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. 위에서 만든 경로가 포함된 **westus** 지역에 **UDR-FrontEnd**라는 이름의 경로 테이블을 만듭니다.

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-FrontEnd -Route $route

5. 서브넷이 있는 VNet을 포함하는 변수를 만듭니다. 이 시나리오에서 VNet 이름은 **TestVNet**입니다.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. 위에서 만든 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.
		
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

>[AZURE.WARNING]위의 명령에 대한 출력에서는 PowerShell을 실행 중인 컴퓨터에만 존재하는 가상 네트워크 구성 개체에 대한 콘텐츠를 보여 줍니다. 이러한 설정을 Azure에 저장하려면 **Set-AzureVirtualNetwork** cmdlet을 실행해야 합니다.

7. Azure에서 새 서브넷 구성을 저장합니다.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

	예상된 출력:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
								...,
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      },
								...
		                    ]	

## 백 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

1. 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** 가상 어플라이언스(192.168.0.4)로 라우팅하는 데 사용된 경로를 만듭니다.

		$route = New-AzureRouteConfig -Name RouteToFrontEnd `
		    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. 위에서 만든 경로가 포함된 **uswest** 지역에 **UDR-BackEnd**라는 이름의 경로 테이블을 만듭니다.

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-BackEnd -Route $route

5. 위에서 만든 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

7. Azure에서 새 서브넷 구성을 저장합니다.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

	예상된 출력:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      ...,
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

## FW1에서 IP 전달을 사용하도록 설정
**FW1**에 사용된 NIC에서 IP 전달을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. FW1에 사용된 NIC에 대한 설정을 포함하는 변수를 만듭니다. 이 시나리오에서 NIC 이름은 **NICFW1**입니다.

		$nicfw1 = Get-AzureNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. IP 전달을 사용하도록 설정하고 NIC 설정을 저장합니다.

		$nicfw1.EnableIPForwarding = 1
		Set-AzureNetworkInterface -NetworkInterface $nicfw1

	예상된 출력:

		Name                 : NICFW1
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState    : Succeeded
		Tags                 : 
		                       Name         Value                  
		                       ===========  =======================
		                       displayName  NetworkInterfaces - DMZ
		                       
		VirtualMachine       : {
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
		                       }
		IpConfigurations     : [
		                         {
		                           "Name": "ipconfig1",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
		                           "PrivateIpAddress": "192.168.0.4",
		                           "PrivateIpAllocationMethod": "Static",
		                           "Subnet": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
		                           },
		                           "PublicIpAddress": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
		                           },
		                           "LoadBalancerBackendAddressPools": [],
		                           "LoadBalancerInboundNatRules": [],
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DnsSettings          : {
		                         "DnsServers": [],
		                         "AppliedDnsServers": [],
		                         "InternalDnsNameLabel": null,
		                         "InternalFqdn": null
		                       }
		EnableIPForwarding   : True
		NetworkSecurityGroup : null
		Primary              : True

<!---HONumber=Oct15_HO4-->