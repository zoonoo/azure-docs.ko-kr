<properties 
   pageTitle="Azure CLI를 사용하여 ARM 모드에서 NSG를 만드는 방법 | Microsoft Azure"
   description="Azure CLI를 사용하여 ARM에서 NSG를 만들고 배포하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# Azure CLI에서 NSG를 만드는 방법

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-classic-cli.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

아래 샘플 Azure CLI 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)을 배포하여 테스트 환경을 구축하고 **Azure에 배포**를 클릭한 다음 필요한 경우 기본 매개 변수 값을 바꾸고 포털의 지침을 따릅니다.

## 프런트 엔드 서브넷에 대한 NSG를 만드는 방법
위의 시나리오에 따라 *NSG-FrontEnd*라는 NSG를 만들려면 다음 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.

2. 아래와 같이 **azure config mode** 명령을 실행하여 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	예상 출력:

		info:    New mode is arm

3. **azure network nsg create** 명령을 실행하여 NSG를 만듭니다.

		azure network nsg create -g TestRG -l westus -n NSG-FrontEnd

	예상 출력:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:    Name                            : NSG-FrontEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

	매개 변수:
	- **-g(또는 --resource-group)**. NSG가 만들어지는 리소스 그룹의 이름입니다. 이 시나리오에서는 *TestRG*입니다.
	- **-l(또는 --location)**. 새 NSG를 만들 Azure 지역입니다. 이 시나리오에서는 *westus*입니다.
	- **-n(또는 --name)**. 새 NSG의 이름입니다. 이 시나리오에서는 *NSG-FrontEnd*입니다.

4. **azure network nsg rule create** 명령을 실행하여 인터넷에서 포트 3389(RDP)에 대한 액세스를 허용하는 규칙을 만듭니다.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	예상 출력:

		info:    Executing command network nsg rule create
		warn:    Using default direction: Inbound
		info:    Looking up the network security rule "rdp-rule"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp
		-rule
		data:    Name                            : rdp-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 3389
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	매개 변수:

	- **-a(또는 --nsg-name)**. 규칙이 만들어질 NSG의 이름입니다. 이 시나리오에서는 *NSG-FrontEnd*입니다.
	- **-n(또는 --name)**. 새 규칙의 이름입니다. 이 시나리오에서는 *rdp-rule*입니다.
	- **-c(또는 --access)**. 규칙(허용 또는 거부)에 대한 액세스 수준입니다.
	- **-p(또는 --protocol)**. 규칙에 대한 프로토콜(Tcp, Udp 또는 *)입니다.
	- **-r(또는 --direction)**. 연결 방향(인바운드 또는 아웃바운드)입니다.
	- **-y(또는 --priority)**. 규칙에 대한 우선순위입니다.
	- **-f(또는 --source-address-prefix)**. CIDR 또는 기본 태그를 사용하는 원본 주소 접두사입니다.
	- **-o(또는 --source-port-range)**. 원본 포트 또는 포트 범위입니다.
	- **-e(또는 --destination-address-prefix)**. CIDR 또는 기본 태그를 사용하는 대상 주소 접두사입니다.
	- **-u(또는 --destination-port-range)**. 대상 포트 또는 포트 범위입니다.	

5. **azure network nsg rule create** 명령을 실행하여 인터넷에서 포트 80(HTTP)에 대한 액세스를 허용하는 규칙을 만듭니다.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	예상 출력:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 80
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. **azure network vnet subnet set** 명령을 실행하여 NSG를 프런트 엔드 서브넷에 연결합니다.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -o NSG-FrontEnd

	예상 출력:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

## 백 엔드 서브넷에 대한 NSG를 만드는 방법
위의 시나리오에 따라 *NSG-BackEnd*라는 NSG를 만들려면 다음 단계를 따르세요.

3. **azure network nsg create** 명령을 실행하여 NSG를 만듭니다.

		azure network nsg create -g TestRG -l westus -n NSG-BackEnd

	예상 출력:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd
		data:    Name                            : NSG-BackEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

4. **azure network nsg rule create** 명령을 실행하여 프런트 엔드 서브넷에서 포트 1433(SQL)에 대한 액세스를 허용하는 규칙을 만듭니다.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	예상 출력:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "sql-rule"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule
		data:    Name                            : sql-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 1433
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

5. **azure network nsg rule create** 명령을 실행하여 인터넷으로 액세스를 거부하는 규칙을 만듭니다.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n web-rule -c Deny -p * -r Outbound -y 200 -f * -o * -e Internet -u *

	예상 출력:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : *
		data:    Source Port                     : *
		data:    Destination IP                  : Internet
		data:    Destination Port                : *
		data:    Protocol                        : *
		data:    Direction                       : Outbound
		data:    Access                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. **azure network vnet subnet set** 명령을 실행하여 NSG를 백 엔드 서브넷에 연결합니다.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -o NSG-BackEnd

	예상 출력:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Setting subnet "BackEnd"
		info:    Looking up the subnet "BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/BackEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : BackEnd
		data:    Address prefix                  : 192.168.2.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

<!---HONumber=AcomDC_0810_2016-->