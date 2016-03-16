## Azure CLI를 사용하여 VNet을 만드는 방법

Windows, Linux 또는 OSX를 실행하는 컴퓨터의 명령 프롬프트에서 Azure CLI를 사용하여 Azure 리소스를 관리할 수 있습니다. Azure CLI를 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    New mode is arm

3. 필요한 경우 아래와 같이 **azure group create**를 실행하여 새 리소스 그룹을 만듭니다. 명령의 출력을 확인합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md/#resource-groups)를 참조하세요.

		azure group create -n TestRG -l centralus

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n(또는 --name)**. 새 리소스 그룹의 이름입니다. 이 시나리오에서는 *TestRG*입니다.
	- **-l(또는 --location)**. 새 리소스 그룹이 생성되는 Azure 지역입니다. 이 시나리오에서는 *centralus*입니다.

4. 아래와 같이 **azure network vnet create** 명령을 실행하여 VNet과 서브넷을 만듭니다.

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g(또는 --resource-group)**. VNet이 만들어지는 리소스 그룹의 이름입니다. 이 시나리오에서는 *TestRG*입니다.
	- **-n(또는 --name)**. 만들 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
	- **-a(또는 --address-prefixes)**. VNet 주소 공간에 사용되는 CIDR 블록의 목록입니다. 이 시나리오에서는 *192.168.0.0/16*입니다.
	- **-l(또는 --location)**. VNet을 만들 Azure 지역입니다. 이 시나리오에서는 *centralus*입니다.

5. 아래와 같이 **azure network vnet subnet create** 명령을 실행하여 서브넷을 만듭니다. 명령의 출력을 확인합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-e(또는 --vnet-name)**. 서브넷이 만들어지는 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
	- **-n(또는 --name)**. 새 서브넷의 이름입니다. 이 시나리오에서는 *FrontEnd*입니다.
	- **-a(또는 --address-prefix)**. 서브넷 CIDR 블록입니다. 이 시나리오에서는 *192.168.1.0/24*입니다.

6. 필요한 경우 위의 5단계를 반복하여 다른 서브넷을 만드세요. 이 시나리오에서는 아래 명령을 실행하여 *BackEnd* 서브넷을 만듭니다.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. 아래와 같이 새 VNet의 속성을 보려면 **azure network vnet show** 명령을 실행합니다.

		azure network vnet show -g TestRG -n TestVNet

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=Oct15_HO3-->