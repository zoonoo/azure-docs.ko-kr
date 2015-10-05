<properties 
   pageTitle="PowerShell을 사용하여 ARM 모드에서 NSG를 만드는 방법 | Microsoft Azure"
   description="PowerShell을 사용하여 ARM에서 NSG를 만들고 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# PowerShell에서 NSG를 만드는 방법

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-classic-ps.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

아래 샘플 PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)을 배포하여 테스트 환경을 구축하고 **Azure에 배포**를 클릭한 다음 필요한 경우 기본 매개 변수 값을 바꾸고 포털의 지침을 따릅니다.

## 프런트 엔드 서브넷에 대한 NSG를 만드는 방법
위의 시나리오에 따라 *NSG-FrontEnd*라는 NSG를 만들려면 다음 단계를 따르세요.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.
2. Azure PowerShell 프롬프트에서 다음과 같이 **Switch-AzureMode** cmdlet을 실행하여 리소스 관리자 모드로 전환합니다.

		Switch-AzureMode AzureResourceManager
	
	예상 출력:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode cmdlet은 곧 더 이상 사용되지 않습니다. 이 경우 모든 리소스 관리자 cmdlet의 이름이 바뀝니다.

3. 인터넷에서 포트 3389에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. 인터넷에서 포트 80에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. 위에서 만든 규칙을 **NSG-FrontEnd**라는 새 NSG에 추가합니다.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

	예상 출력(기본 규칙을 표시하지 않음):

		Name                 : NSG-FrontEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-FrontEnd
		Etag                 : W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [	
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [ ... ]
		NetworkInterfaces    : []
		Subnets              : []		

6. 위에서 만든 NSG를 *FrontEnd* 서브넷에 연결합니다.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	*FrontEnd* 서브넷 설정을 보여 주는 예상 출력에서 **NetworkSecurityGroup** 속성의 값을 확인합니다.

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

## 백 엔드 서브넷에 대한 NSG를 만드는 방법
위의 시나리오에 따라 *NSG-BackEnd*라는 NSG를 만들려면 다음 단계를 따르세요.

1. 프런트 엔드 서브넷에서 포트 1433(SQL Server에서 사용되는 기본 포트)에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. 인터넷에 대한 액세스를 차단하는 보안 규칙을 만듭니다.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. 위에서 만든 규칙을 **NSG-BackEnd**라는 새 NSG에 추가합니다.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

	예상 출력(기본 규칙을 표시하지 않음):

		Name                 : NSG-BackEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-BackEnd
		Etag                 : W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [
		                         {
		                           "Name": "frontend-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/frontend-rule",
		                           "Description": "Allow FE subnet",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "1433",
		                           "SourceAddressPrefix": "192.168.1.0/24",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
		                           "Description": "Block Internet",
		                           "Protocol": "*",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "*",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "Internet",
		                           "Access": "Deny",
		                           "Priority": 200,
		                           "Direction": "Outbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [...]
		NetworkInterfaces    : []
		Subnets              : []		

6. 위에서 만든 NSG를 *BackEnd* 서브넷에 연결합니다.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	*BackEnd* 서브넷 설정을 보여 주는 예상 출력에서 **NetworkSecurityGroup** 속성의 값을 확인합니다.

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ipConfigurations/ipconfig1"
                          },
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ipConfigurations/ipconfig1"
                          }
                        ],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

<!---HONumber=Sept15_HO4-->