## Azure CLI를 사용하여 클래식 VNet을 만드는 방법
Windows, Linux 또는 OSX를 실행하는 컴퓨터의 명령 프롬프트에서 Azure CLI를 사용하여 Azure 리소스를 관리할 수 있습니다. Azure CLI를 사용하여 VNet을 만들려면 다음 단계를 수행합니다.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../articles/xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure network vnet create** 명령을 실행하여 VNet과 서브넷을 만듭니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    예상 출력:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. 만들 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
   * **-e(또는 --address-space)**. VNet 주소 공간입니다. 이 시나리오에서는 *192.168.0.0*입니다.
   * **-i(또는 -cidr)**. CIDR 형식의 네트워크 마스크입니다. 이 시나리오에서는 *16*입니다.
   * **-n(또는 --subnet-name**). 첫 번째 서브넷의 이름입니다. 이 시나리오에서는 *FrontEnd*입니다.
   * **-p(또는 --subnet-start-ip)**. 서브넷 또는 서브넷 주소 공간의 시작 IP 주소입니다. 이 시나리오에서는 *192.168.1.0*입니다.
   * **-r(또는 --subnet-cidr)**. 서브넷에 대한 CIDR 형식의 네트워크 마스크입니다. 이 시나리오에서는 *24*입니다.
   * **-l(또는 --location)**. VNet을 만들 Azure 지역입니다. 이 시나리오에서는 *Central US*입니다.
3. 아래와 같이 **azure network vnet subnet create** 명령을 실행하여 서브넷을 만듭니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    다음은 위의 명령에 대해 예상된 출력입니다.
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t(또는 --vnet-name)**. 서브넷이 만들어지는 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
   * **-n(또는 --name)**. 새 서브넷의 이름입니다. 이 시나리오에서는 *BackEnd*입니다.
   * **-a(또는 --address-prefix)**. 서브넷 CIDR 블록입니다. 이 시나리오에서는 *192.168.2.0/24*입니다.
4. 아래와 같이 새 VNet의 속성을 보려면 **azure network vnet show** 명령을 실행합니다.
   
            azure network vnet show
   
    다음은 위의 명령에 대해 예상된 출력입니다.
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

<!---HONumber=AcomDC_0323_2016-->