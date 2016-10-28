<properties 
   pageTitle="Azure CLI를 사용하여 리소스 관리자에서 라우팅 제어 및 가상 어플라이언스 사용 | Microsoft Azure"
   description="Azure CLI를 사용하여 라우팅 제어 및 가상 어플라이언스 사용 방법 알아보기"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#Azure CLI에서 사용자 정의 경로(UDR) 만들기

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 UDR을 만들](virtual-network-create-udr-classic-cli.md) 수도 있습니다.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 Azure CLI 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)을 배포하여 테스트 환경을 구축하고 **Azure에 배포**를 클릭한 다음 필요한 경우 기본 매개 변수 값을 바꾸고 포털의 지침을 따릅니다.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

3. **`azure network route-table create`** 명령을 실행하여 프런트 엔드 서브넷에 대한 경로 테이블을 만듭니다.

		azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

	출력:

		info:    Executing command network route-table create
		info:    Looking up route table "UDR-FrontEnd"
		info:    Creating route table "UDR-FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    Name                            : UDR-FrontEnd
		data:    Type                            : Microsoft.Network/routeTables
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		info:    network route-table create command OK

	매개 변수:
	- **-g (or --resource-group)**. UDR이 만들어지는 리소스 그룹의 이름입니다. 이 시나리오에서는 *TestRG*입니다.
	- **-l(또는 --location)**. 새 UDR을 만들 Azure 지역입니다. 이 시나리오에서는 *westus*입니다.
	- **-n (or --name)**. 새 UDR의 이름입니다. 이 시나리오에서는 *UDR-FrontEnd*입니다.

4. **`azure network route-table route create`** 명령을 실행하여 위에서 만든 경로 테이블에 경로를 만들고 백 엔드 서브넷(192.168.2.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

		azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

	출력:

		info:    Executing command network route-table route create
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd/routes/RouteToBackEnd
		data:    Name                            : RouteToBackEnd
		data:    Provisioning state              : Succeeded
		data:    Next hop type                   : VirtualAppliance
		data:    Next hop IP address             : 192.168.0.4
		data:    Address prefix                  : 192.168.2.0/24
		info:    network route-table route create command OK

	매개 변수:
	- **-r(또는 --route-table-name)**. 경로가 추가될 경로 테이블의 이름입니다. 이 시나리오에서는 *UDR-FrontEnd*입니다.
	- **-a(또는 --address-prefix)**. 패킷을 보내는 서브넷에 대한 주소 접두사입니다. 이 시나리오에서는 *192.168.2.0/24*입니다.
	- **-y(또는 --next-hop-type)**. 전송할 개체 트래픽 유형입니다. 가능한 값은 *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* 또는 *None*입니다.
	- **-p(또는 --next-hop-ip-address**). 다음 홉에 대한 IP 주소입니다. 이 시나리오에서는 *192.168.0.4*입니다.

5. **`azure network vnet subnet set`** 명령을 실행하여 위에서 만든 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

	출력:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
		igurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
		igurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

	매개 변수:
	- **-e(또는 --vnet-name)**. 서브넷이 위치한 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
 
## 백 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

1. **`azure network route-table create`** 명령을 실행하여 백 엔드 서브넷에 대한 경로 테이블을 만듭니다.

		azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. **`azure network route-table route create`** 명령을 실행하여 위에서 만든 경로 테이블에 경로를 만들고 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

		azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. **`azure network vnet subnet set`** 명령을 실행하여 위에서 만든 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## FW1에서 IP 전달을 사용하도록 설정
**FW1**에 사용된 NIC에서 IP 전달을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. **`azure network nic show`** 명령을 실행하고 **IP 전달 사용**에 대한 값을 확인합니다. *false*로 설정해야 합니다.

		azure network nic show -g TestRG -n NICFW1

	출력:
		
		info:    Executing command network nic show
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : false
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic show command OK

2. **`azure network nic set`** 명령을 실행하여 IP 전달을 사용하도록 설정합니다.

		azure network nic set -g TestRG -n NICFW1 -f true

	출력:

		info:    Executing command network nic set
		info:    Looking up the network interface "NICFW1"
		info:    Updating network interface "NICFW1"
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : true
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic set command OK

	매개 변수:

	- **-f(또는 --enable-ip-forwarding)**. *true* 또는 *false*.

<!---HONumber=AcomDC_0831_2016-->