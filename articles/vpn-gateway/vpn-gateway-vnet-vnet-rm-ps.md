<properties
   pageTitle="VNet에 대해 Azure 리소스 관리자 및 PowerShell을 사용하여 VNet 간 VPN 게이트웨이 연결 만들기 | Microsoft Azure"
   description="이 문서에서는 Azure 리소스 관리자 및 PowerShell을 사용하여 가상 네트워크를 함께 연결하는 과정을 안내합니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="cherylmc"/>

# Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성

> [AZURE.SELECTOR]
- [Azure 클래식 포털](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure 리소스 관리자](vpn-gateway-vnet-vnet-rm-ps.md)

이 문서에서는 **리소스 관리자** 배포 모델 및 PowerShell을 사용하여 가상 네트워크 간에 연결을 만드는 단계를 안내합니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## VNet 간 연결 정보

가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 매우 유사합니다. 두 연결 유형 모두 Azure VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 연결하는 VNet은 서로 다른 지역에 있을 수 있습니다. 또는 다른 구독에 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 아래 다이어그램에 표시된 것처럼 크로스-프레미스 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.


![연결 정보](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### 가상 네트워크에 연결하는 이유

다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

- **지역 간 지리적 중복 및 지리적 상태**
	- 인터넷 연결 끝점으로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
	- Azure 트래픽 관리자 및 부하 분산 장치를 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.

- **분리 또는 관리 경계를 가진 지역별 다중 계층 응용 프로그램**
	- 같은 지역 내에서 분리 또는 관리 요구 사항 때문에 여러 가상 네트워크가 함께 연결된 다중 계층 응용 프로그램을 설정할 수 있습니다.


### VNet-VNet FAQ

- 가상 네트워크는 같은 Azure 지역(위치)에 있을 수도 있고 다른 Azure 지역(위치)에 있을 수도 있습니다.

- 클라우드 서비스 또는 부하 분산 끝점은 연결되어 있더라도 여러 가상 네트워크에 분산될 수 없습니다.

- 크로스-프레미스 연결이 필요한 경우가 아니면 여러 Azure 가상 네트워크를 연결할 때 온-프레미스 VPN 게이트웨이는 필요하지 않습니다.

- VNet 간 연결은 가상 네트워크 연결을 지원합니다. 그러나 가상 네트워크에 포함되어 있지 않은 가상 컴퓨터 또는 클라우드 서비스 연결은 지원하지 않습니다.

- VNet 간 연결에는 RouteBased(이전에는 동적 라우팅으로 지칭) VPN 유형의 Azure VPN 게이트웨이가 필요합니다.

- 가상 네트워크 연결을 다중 사이트 VPN과 함께 사용할 수 있으며 가상 네트워크 VPN 게이트웨이당 최대 10개(기본/표준 게이트웨이) 또는 30개(고성능 게이트웨이)의 VPN 터널을 다른 가상 네트워크 또는 온-프레미스 사이트에 연결할 수 있습니다.

- 가상 네트워크 및 온-프레미스 로컬 네트워크 사이트의 주소 공간이 겹쳐서는 안 됩니다. 주소 공간이 겹치면 VNet 간 연결 만들기가 실패합니다.

- 가상 네트워크 한 쌍 간의 중복 터널은 지원되지 않습니다.

- 가상 네트워크의 모든 VPN 터널은 Azure VPN 게이트웨이의 사용 가능한 대역폭 및 Azure의 동일 VPN 게이트웨이 작동 시간 SLA를 공유합니다.

- VNet 간 트래픽은 인터넷이 아닌 Microsoft 네트워크를 통해 전달됩니다.

- 동일한 지역 내의 VNet 간 트래픽은 양방향 모두 무료입니다. 지역 VNet 간 송신 크로스 트래픽은 소스 지역을 기반으로 아웃바운드 VNet 간 데이터 전송 요금으로 청구됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/vpn-gateway/)를 참조하세요.


## 어느 단계 집합을 사용해야 합니까?

이 문서에서 리소스 관리자 배포 모델을 사용하여 생성된 VNet에 적용되는 여러 가지 단계 집합을 보게 됩니다. VNet 간 구성 단계는 Vnet이 동일한 구독에 있는지 아니면 다른 구독에 있는지에 따라 달라집니다.

![두 연결 모두](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


구성 단계의 경우 둘 사이의 주요 차이점은 동일한 PowerShell 세션 내에서 모든 가상 네트워크 및 게이트웨이 리소스를 생성하고 구성할 수 있는지 여부입니다. 이 문서는 각각의 경우에 대한 단계를 포함하고 있습니다. 위 그림에는 같은 구독 VNet 간 연결과 크로스 구독 VNet 간 연결을 모두 보여줍니다.


- [같은 구독에 있는 VNet](#samesub)
- [다른 구독에 있는 VNet](#difsub)


## <a name ="samesub"/>같은 구독에 있는 VNet을 연결하는 방법

이 구성은 아래 다이어그램에 표시 된 것처럼 동일한 구독에 있는 가상 네트워크에 적용됩니다.

![VNet 간 동일한 구독](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### 시작하기 전에

- Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	
- Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

### <a name ="Step1"/>1단계 - IP 주소 범위 계획


네트워크 구성을 구성하는 데 사용할 범위를 결정하는 것이 중요합니다. 따라서 VNet 범위 또는 로컬 네트워크 범위가 겹치지 않는지 확인해야 합니다.

아래 단계에서는 두 개의 가상 네트워크와 해당 게이트웨이 서브넷 및 구성을 만듭니다. 그런 다음 두 VNet 간에 VPN 게이트웨이 연결을 만듭니다.

이 연습에서는 VNet에 대해 다음 값을 사용합니다.

**TestVNet1에 대한 값:**

- VNet 이름: TestVNet1
- 리소스 그룹: TestRG1
- 위치: 미국 동부
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- 프런트 엔드: 10.11.0.0/24
- 백 엔드: 10.12.0.0/24
- 게이트웨이 서브넷 = 10.12.255.0/27
- DNS 서버: 8.8.8.8
- 게이트웨이 이름: VNet1GW
- 공용 IP: VNet1GWIP
- VpnType: 경로 기반
- 연결(1 대 4): VNet1 대 VNet4
- 연결(1 대 5): VNet1 대 VNet5
- 연결 유형: VNet 간


**TestVNet4에 대한 값:**

- VNet 이름: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- 프런트 엔드: 10.41.0.0/24
- 백 엔드: 10.42.0.0/24
- 게이트웨이 서브넷 = 10.42.255.0.0/27
- 리소스 그룹: TestRG4
- 위치: 미국 서부
- DNS 서버: 8.8.8.8
- 게이트웨이 이름: VNet4GW
- 공용 IP: VNet4GWIP
- VpnType: 경로 기반
- 연결: VNet4 대 VNet1
- 연결 유형: VNet 간



### <a name ="Step2"/>2단계 - TestVNet1 만들기 및 구성

1. 변수 선언

	이 연습에서는 먼저 변수를 선언합니다. 아래 예제에서는 이 연습에 대한 값을 사용하여 변수를 선언합니다. 생산을 위해 구성하는 경우 값을 사용자의 값으로 바꾸어야 합니다. 이 구성 유형에 익숙해지기 위해 단계를 차례로 실행하는 경우 이 변수를 사용할 수 있습니다. 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. 구독 1에 연결

	리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

	PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

		Login-AzureRmAccount

	계정에 대한 구독을 확인합니다.

		Get-AzureRmSubscription 

	사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. 새 리소스 그룹 만들기

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. TestVNet1에 대한 서브넷 구성 만들기

	아래 샘플은 TestVNet1이라는 가상 네트워크 및 GatewaySubnet, FrontEnd 및 Backend라는 서브넷 세 개를 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 GatewaySubnet라고 명시적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

	아래 예제에서는 게이트웨이 서브넷이 /27을 사용합니다. 기술적으로 보면 /29처럼 작은 서브넷을 사용하여 게이트웨이 서브넷을 만들 수 있지만 이를 수행하지 않는 것이 좋습니다. 더 큰 게이트웨이 서브넷이 필요할 수 있는 기존 또는 미래 구성을 이용하려는 경우 /27 또는 /26 등과 같이 더 큰 것을 사용하는 것이 좋습니다.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. TestVNet1 만들기

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. 공용 IP 주소 요청

	다음에는 VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 사용할 IP 주소를 지정할 수는 없으며 IP 주소는 게이트웨이에 동적으로 할당됩니다. 다음 구성 섹션에서 이 IP 주소를 사용합니다. 아래 예제를 사용하세요. 이 주소의 할당 방법은 동적이어야 합니다.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. 게이트웨이 구성 만들기

	게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 아래 샘플을 사용하여 게이트웨이 구성을 만듭니다.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. TestVNet1에 대한 게이트웨이 만들기

	이 단계에서는 TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. VNet-VNet 구성에는 RouteBased VpnType이 필요합니다. 게이트웨이 만들기는 꽤 시간이 걸릴 수 있습니다(완료되려면 30분 이상).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 3단계 - TestVNet4 만들기 및 구성

TestVNet1를 구성한 후 단계를 반복하여 TestVNet4를 만듭니다. 아래 단계에 따라 필요한 경우 값을 사용자의 고유한 값으로 바꾸십시오. 이 단계는 같은 구독에 있기 때문에 같은 PowerShell 세션 내에서 수행할 수 있습니다.

1. 변수 선언

	값을 구성에 사용할 값으로 바꾸어야 합니다.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	계속하기 전에 여전히 구독 1에 연결되어 있는지 확인하십시오.

2. 새 리소스 그룹 만들기

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. TestVNet4에 대한 서브넷 구성 만들기

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. TestVNet4 만들기

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. 공용 IP 주소 요청

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. 게이트웨이 구성 만들기

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. TestVNet4 게이트웨이 만들기

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 4단계 - 게이트웨이 연결

1. 두 개의 가상 네트워크 게이트웨이 모두 가져오기

	이 예제에서는 두 게이트웨이가 동일한 구독에 있기 때문에 동일한 PowerShell 세션에서 이 단계를 완료할 수 있습니다.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. TestVNet1 대 TestVNet4 연결 만들기

	이 단계에서는 TestVNet1에서 TestVNet4까지 연결을 만듭니다. 예제에서 참조된 공유 키를 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. TestVNet4 대 TestVNet1 연결 만들기

	이 단계는 TestVNet4에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 공유된 키가 일치하는지 확인합니다.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	몇 분 후 연결이 설정됩니다.

## <a name ="Verify"/>VNet 간 연결을 확인하는 방법

아래 예제는 연결을 확인하는 방법을 보여줍니다. 반드시 사용자의 환경에 맞는 값으로 변경해야 합니다.

### Azure 포털을 사용하여 연결을 확인하려면

**가상 네트워크 게이트웨이** -> **게이트웨이 이름 클릭** -> **설정** -> **연결**로 이동하여 Azure 포털에서 VPN 연결을 확인할 수 있습니다. 연결의 이름의 선택하여 **연결** 블레이드에서 자세한 정보를 볼 수 있습니다.


### PowerShell을 사용하여 연결을 확인하려면

또한 *Get-AzureRmVirtualNetworkGatewayConnection –Debug*를 사용하여 연결이 성공했는지 확인할 수 있습니다. 다음 예제를 사용하여 값을 사용자 고유의 값과 일치하도록 변경할 수 있습니다. 메시지가 표시되면 모두 실행하기 위해 A를 선택합니다.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

cmdlet이 완료되면 스크롤하여 값을 확인합니다. 아래 PowerShell 출력 예제에서는 연결 상태가 *연결됨*으로 표시되고 송/수신 바이트를 볼 수 있습니다.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>다른 구독에 있는 VNet을 연결하는 방법

아래 구성 단계에서는 VNet 간 연결을 더 추가하여 TestVNet1을 다른 구독에 있는 TestVNet5에 연결합니다. 여기서 차이점은 두 번째 구독의 상황에서, 특히 두 구독이 다른 조직에 속한 경우 구성 단계의 일부를 별도의 PowerShell 세션에서 수행해야 한다는 것입니다. 아래 단계를 완료하면 결과적인 구성이 아래 다이어그램에 표시됩니다.

![VNet 간 다른 구독](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

아래 지침은 위에 나열된 이전 단계에서 계속합니다. TestVNet1 및 TestVNet1용 VPN 게이트웨이를 만들고 구성하려면 [1단계](#Step1) 및 [2단계](#Step2)를 완료해야 합니다. 다른 구독의 VNet을 연결하기만 할 계획인 경우 이전 연습의 3단계 및 4단계를 걱정할 필요 없이 아래의 5단계로 진행할 수 있습니다.

### 5단계-추가 IP 주소 범위 확인

새 가상 네트워크 TestVNet5의 IP 주소 공간이 VNet 범위 또는 로컬 네트워크 게이트웨이 범위와 겹치지 않는지 확인해야 합니다.

이 예제에서 가상 네트워크는 서로 다른 조직에 속할 수 있습니다. 이 연습에서는 TestVNet5에 대해 다음 값을 사용할 수 있습니다.

**TestVNet5에 대한 값:**

- VNet 이름: TestVNet5
- 리소스 그룹: TestRG5
- 위치: 일본 동부
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- 프런트 엔드: 10.51.0.0/24
- 백 엔드: 10.52.0.0/24
- 게이트웨이 서브넷: 10.52.255.0.0/27
- DNS 서버: 8.8.8.8
- 게이트웨이 이름: VNet5GW
- 공용 IP: VNet5GWIP
- VpnType: 경로 기반
- 연결: VNet5 대 VNet1
- 연결 유형: VNet 간

**TestVNet1에 대한 추가 값:**

- 연결: VNet1 대 VNet5


### 6단계 - TestVNet5 만들기 및 구성

이 단계는 새 구독의 상황에서 수행해야 합니다. 이 부분은 구독을 소유한 다른 조직의 관리자가 수행할 수 있습니다.

1. 변수 선언

	값을 구성에 사용할 값으로 바꾸어야 합니다.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. 구독 5에 연결

	PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

		Login-AzureRmAccount

	계정에 대한 구독을 확인합니다.

		Get-AzureRmSubscription 

	사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. 새 리소스 그룹 만들기

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. TestVNet4에 대한 서브넷 구성 만들기
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. TestVNet5 만들기

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. 공용 IP 주소 요청

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. 게이트웨이 구성 만들기

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig 

8. TestVNet5 게이트웨이 만들기

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 7단계 - 게이트웨이 연결

이 예제에서는 게이트웨이가 다른 구독에 있기 때문에 이 단계를 [구독 1] 및 [구독 5]로 표시된 두 개의 PowerShell 세션으로 분할했습니다.

1. **[구독 1]** 구독 1에 대한 가상 네트워크 게이트웨이 가져오기

	로그인하고 구독 1에 연결해야 합니다.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	다음 요소의 출력을 복사하고 전자 메일 또는 다른 방법을 통해 구독 5의 관리자에게 보냅니다.

		$vnet1gw.Name
		$vnet1gw.Id

	이 두 요소는 다음 예제 출력과 유사한 값을 갖게 됩니다.

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[구독 5]** 구독 5에 대한 가상 네트워크 게이트웨이 가져오기

	로그인하고 구독 5에 연결해야 합니다.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	다음 요소의 출력을 복사하고 전자 메일 또는 다른 방법을 통해 구독 1의 관리자에게 보냅니다.

		$vnet5gw.Name
		$vnet5gw.Id

	이 두 요소는 다음 예제 출력과 유사한 값을 갖게 됩니다.

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[구독 1]** TestVNet1 대 TestVNet5 연결 만들기

	이 단계에서는 TestVNet1에서 TestVNet5까지 연결을 만듭니다. 여기서 차이점은 $vnet5gw가 다른 구독에 있기 때문에 직접 가져올 수 없다는 것입니다. 위의 단계에서 구독 1에서 전달한 값을 사용하여 새 PowerShell 개체를 만들어야 합니다. 이름, ID 및 공유 키를 사용자의 고유한 값으로 바꾸십시오. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.

	구독 1에 연결해야 합니다.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[구독 5]** TestVNet5 대 TestVNet1 연결 만들기

	이 단계는 TestVNet5에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 구독 1에서 가져온 값을 기반으로 PowerShell 개체를 만드는 동일한 과정이 여기에도 적용됩니다. 이 단계에서는 공유된 키가 일치해야 합니다.

	구독 5에 연결해야 합니다.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. 연결 확인

	이 단계를 완료한 후 [VNet 간 연결을 확인하는 방법](#Verify)을 사용하여 연결을 확인할 수 있습니다.

## 다음 단계

연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_0309_2016-->