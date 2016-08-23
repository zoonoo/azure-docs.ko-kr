<properties 
   pageTitle="리소스 관리자에서 PowerShell을 사용하여 NSG 관리 | Microsoft Azure"
   description="리소스 관리자에서 PowerShell을 사용하여 기존 NSG를 관리하는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# PowerShell을 사용하여 NSG 관리

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] 클래식 배포 모델.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 정보 검색

기존 NSG를 보고 기존 NSG에 대한 규칙을 검색하며 NSG가 연결된 리소스에 대해 알아볼 수 있습니다.

### 기존 NSG 보기
구독에서 모든 기존 NSG를 보려면 아래와 같이 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

	Get-AzureRmNetworkSecurityGroup

예상된 결과:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


특정 리소스 그룹에 있는 NSG 목록을 보려면 아래와 같이 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

예상 출력:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### NSG에 대한 모든 규칙 나열

**NSG-FrontEnd**로 명명된 NSG의 규칙을 보려면 아래와 같이 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

예상 출력:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] 또한 `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules`을 사용하여 **NSG-FrontEnd** NSG에서 기본 규칙을 나열할 수 있습니다.

### NSG 연결 보기

**NSG-FrontEnd** NSG가 연결된 리소스를 보려면 아래와 같이 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

아래와 같이 **NetworkInterfaces** 및 **서브넷** 속성을 찾아봅니다.

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

위의 예에서 NSG는 NIC(네트워크 인터페이스)에 연결되지 않고 **FrontEnd**라는 서브넷에 연결됩니다.

## 규칙 관리

기존 NSG에 규칙을 추가하고 기존 규칙을 편집하며 규칙을 제거할 수 있습니다.

### 규칙 추가

컴퓨터에서 **NSG-FrontEnd** NSG에 포트 **443**에 대한 **인바운드** 트래픽을 허용하는 규칙을 추가하려면 아래 단계를 수행합니다.

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 다음과 같이 `Add-AzureRmNetworkSecurityRuleConfig` cmdlet을 실행합니다.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. NSG에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	보안 규칙만 표시하는 확장된 출력:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 규칙 변경

**인터넷**에서 인바운드 트래픽을 허용하도록 위에서 만든 규칙을 변경하려면는 아래 단계를 수행합니다.

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 다음과 같이 `Set-AzureRmNetworkSecurityRuleConfig` cmdlet을 실행합니다.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. NSG에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	보안 규칙만 표시하는 확장된 출력:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 규칙 삭제

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 다음과 같이 `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet을 실행합니다.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. NSG에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	보안 규칙만 표시하는 예상된 출력에는 더 이상 **https-rule**이 나열되지 않습니다.

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## 연결 관리

NSG를 서브넷 및 NIC에 연결할 수 있습니다. 또한 연결된 모든 리소스에서 NSG를 분리할 수 있습니다.

### NIC에 NSG 연결

**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에 연결하려면 아래 단계를 수행합니다.

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. `Get-AzureRmNetworkInterface` cmdlet을 실행하여 아래와 같이 기존 NIC를 검색하고 변수에 저장합니다.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. **NIC** 변수의 **NetworkSecurityGroup** 속성을 아래와 같이 **NSG** 변수의 값으로 설정합니다.

		$nic.NetworkSecurityGroup = $nsg

4. NIC에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmNetworkInterface` cmdlet을 실행합니다.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	**NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### NIC에서 NSG 분리

**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에서 분리하려면 아래 단계를 수행합니다.

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. `Get-AzureRmNetworkInterface` cmdlet을 실행하여 아래와 같이 기존 NIC를 검색하고 변수에 저장합니다.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. **NIC** 변수의 **NetworkSecurityGroup** 속성을 아래와 같이 **$null**로 설정합니다.

		$nic.NetworkSecurityGroup = $null

4. NIC에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmNetworkInterface` cmdlet을 실행합니다.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	**NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.

		NetworkSecurityGroup : null

### 서브넷에서 NSG 분리

**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에서 분리하려면 아래 단계를 수행합니다.

1. `Get-AzureRmVirtualNetwork` cmdlet을 실행하여 아래와 같이 기존 VNet를 검색하고 변수에 저장합니다.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet을 실행하여 아래와 같이 **FrontEnd** 서브넷을 검색하고 변수에 저장합니다.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. **서브넷** 변수의 **NetworkSecurityGroup** 속성을 아래와 같이 **$null**로 설정합니다.

		$subnet.NetworkSecurityGroup = $null

4. 서브넷에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmVirtualNetwork` cmdlet을 실행합니다.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	**FrontEnd** 서브넷의 속성만을 표시하는 예상된 출력입니다. **NetworkSecurityGroup**에 대한 속성이 없습니다.

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### 서브넷에 NSG 연결

**NSG-FrontEnd** NSG를 **FronEnd** 서브넷에 다시 연결하려면 아래 단계를 수행합니다.

1. `Get-AzureRmVirtualNetwork` cmdlet을 실행하여 아래와 같이 기존 VNet를 검색하고 변수에 저장합니다.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet을 실행하여 아래와 같이 **FrontEnd** 서브넷을 검색하고 변수에 저장합니다.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행하여 아래와 같이 기존 NSG를 검색하고 변수에 저장합니다.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. **서브넷** 변수의 **NetworkSecurityGroup** 속성을 아래와 같이 **$null**로 설정합니다.

		$subnet.NetworkSecurityGroup = $nsg

4. 서브넷에 대한 변경 사항을 저장하려면 아래와 같이 `Set-AzureRmVirtualNetwork` cmdlet을 실행합니다.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	**FrontEnd** 서브넷의 **NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## NSG 삭제

리소스에 연결되지 않은 경우 NSG를 삭제할 수 있습니다. NSG를 삭제하려면 다음 단계를 수행합니다.

1. NSG에 연결된 리소스를 확인하려면 [NSG 연결 보기](#View-NSGs-associations)에서처럼 `azure network nsg show`을 실행합니다.
2. NSG가 NIC에 연결된 경우 각 NIC에 대한 [NIC에서 NSG 분리](#Dissociate-an-NSG-from-a-NIC)에서처럼 `azure network nic set`을 실행합니다.
3. NSG가 서브넷에 연결된 경우 각 서브넷에 대한 [서브넷에서 NSG 분리](#Dissociate-an-NSG-from-a-subnet)에서처럼 `azure network vnet subnet set`을 실행합니다.
4. NSG를 삭제하려면 아래와 같이 `Remove-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] **-Force** 매개 변수를 사용하면 삭제를 확인하지 않아도 됩니다.

## 다음 단계

- NSG에 대한 [로깅을 사용합니다](virtual-network-nsg-manage-log.md).

<!---HONumber=AcomDC_0810_2016-->