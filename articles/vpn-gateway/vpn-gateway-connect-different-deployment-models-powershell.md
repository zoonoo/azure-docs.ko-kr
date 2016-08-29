<properties 
   pageTitle="PowerShell을 사용하여 Resource Manager 가상 네트워크에 클래식 가상 네트워크를 연결하는 방법 | Microsoft Azure"
   description="VPN 게이트웨이 및 PowerShell을 사용하여 클래식 VNet 및 Resource Manager VNet 간에 VPN 연결을 만드는 방법을 알아봅니다"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="cherylmc" />

# PowerShell을 사용하여 다양한 배포 모델에서 가상 네트워크 연결

> [AZURE.SELECTOR]
- [포털](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure에는 현재 클래식 및 RM(Resource Manager) 등 두 개의 관리 모델이 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 이후의 VM 및 역할 인스턴스는 Resource Manager에서 만들 VNet에서 실행되고 있을 것입니다. 이 문서에서는 Resource Manager VNet에 클래식 VNet을 연결하여 별도 배포 모델에 있는 리소스가 게이트웨이 연결을 통해 서로 통신하도록 허용하는 과정을 안내합니다.

다른 구독 및 다른 지역에 있는 다른 배포 모델인 VNet 간에 연결을 만들 수 있습니다. 또한 함께 구성된 게이트웨이가 동적이거나 경로 기반이면 온-프레미스 네트워크에 이미 연결된 VNet을 연결할 수 있습니다. VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 FAQ](#faq)를 참조하세요. 
[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## 시작하기 전에

다음 단계에서는 각 VNet에 대한 동적 또는 경로 기반 게이트웨이를 구성하고 게이트웨이 간에 VPN 연결을 만드는 데 필요한 설정을 안내합니다. 이 구성은 고정 또는 정책 기반 게이트웨이를 지원하지 않습니다.

시작하기 전에 다음 사항을 확인합니다.

 - 두 VNet이 이미 생성되었습니다.
 - VNet에 대한 주소 범위는 서로 겹치거나 게이트웨이가 연결되어 있는 다른 연결의 범위와 겹치지 않습니다.
 - 최신 PowerShell cmdlet(1.0.2 이상)을 설치했습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. SM(서비스 관리)와 RM(Resource Manager) cmdlet을 모두 설치해야 합니다.

### <a name="exampleref"></a>예제 설정

다음 단계에서 PowerShell cmdlet을 사용하는 경우 참조로 예제 설정을 사용할 수 있습니다.

**클래식 VNet 설정**

VNet 이름 = ClassicVNet <br> 위치 = 미국 서부 <br> 가상 네트워크 주소 공간 = 10.0.0.0/24 <br> 서브넷-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> 로컬 네트워크 이름 = RMVNetLocal <br> GatewayType = DynamicRouting

**Resource Manager VNet 설정**

VNet 이름 = RMVNet <br> 리소스 그룹 = RG1 <br> 가상 네트워크 IP 주소 공간 = 192.168.1.0/16 <br> 서브넷 -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> 위치 = 미국 동부 <br> 게이트웨이 공용 IP 이름 = gwpip <br> 로컬 네트워크 게이트웨이 = ClassicVNetLocal <br> 가상 네트워크 게이트웨이 이름 = RMGateway <br> 게이트웨이 IP 주소 구성 = gwipconfig


## <a name="createsmgw"></a>섹션 1 - 클래식 VNet 구성

### 1부 - 네트워크 구성 파일 다운로드

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 이 cmdlet은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. 구성의 이 부분을 완료하려면 SM PowerShell cmdlet을 사용합니다.

		Add-AzureAccount

2. 다음 명령을 실행하여 Azure 네트워크 구성 파일을 내보냅니다. 필요한 경우 다른 위치로 내보낼 파일의 위치를 변경할 수 있습니다. 파일을 편집한 다음 Azure로 가져옵니다.

		Get-AzureVNetConfig -ExportToFile c:\AzureNet\NetworkConfig.xml

3. 다운로드한 .xml 파일을 열고 편집합니다. 네트워크 구성 파일의 예는 [네트워크 구성 스키마](https://msdn.microsoft.com/library/jj157100.aspx)를 참조하세요.
 

### 2부 - 게이트웨이 서브넷 확인

**VirtualNetworkSites** 요소에 게이트웨이 서브넷을 아직 만들지 않은 경우 VNet에 추가합니다. 네트워크 구성 파일로 작업할 때 게이트웨이 서브넷의 이름은 "GatewaySubnet"이어야 합니다. 또는 Azure에서 게이트웨이 서브넷으로 인식하고 사용할 수 없습니다.
	
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### 3단계 - 로컬 네트워크 사이트 추가

추가한 로컬 네트워크 사이트는 연결하려는 RM VNet을 나타냅니다. 존재하지 않는 경우 파일에 **LocalNetworkSites** 요소를 추가해야 합니다. 구성의 이 시점에서 Resource Manager VNet에 대한 게이트웨이 아직 만들지 않았기 때문에 VPNGatewayAddress는 유효한 공용 IP 주소일 수 있습니다. 게이트웨이를 만들고 나면 이 자리 표시자 IP 주소를 RM 게이트웨이에 할당된 올바른 공용 IP 주소로 바꿉니다.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.1.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### 4부 - 로컬 네트워크 사이트와 VNet 연결

이 섹션에서는 VNet을 연결하려는 로컬 네트워크 사이트를 지정합니다. 이 경우에 앞서 참조한 Resource Manager VNet입니다. 이름이 일치해야 합니다. 이 단계에서는 게이트웨이를 만들지 않습니다. 게이트웨이를 연결할 로컬 네트워크를 지정합니다.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### 5부 - 파일 저장 및 업로드

파일을 저장하고 다음 명령을 실행하여 Azure에 가져옵니다. 사용자 환경에 필요한 대로 파일 경로를 변경해야 합니다.

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\ClassicVNet.xml

가져오기가 성공했음을 보여 주는 이 결과와 유사하게 표시됩니다.

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### 6부 - 가상 네트워크 게이트웨이 만들기 

클래식 포털 또는 PowerShell을 사용하여 가상 네트워크 게이트웨이를 만들 수 있습니다. 현재, Azure 포털에서 클래식 게이트웨이를 만들 수는 없습니다.

#### 클래식 포털에서 게이트웨이를 만들려면

1. [클래식 포털](https://manage.windowsazure.com)에서 **네트워크**로 이동하고 가상 네트워크 게이트웨이를 만들려는 클래식 VNet을 클릭합니다. VNet에 대한 기본 페이지를 열게 됩니다.
2. 페이지 맨 위에 있는 **대시보드**를 클릭하여 대시보드 페이지를 변경합니다.
3. 대시보드 페이지 아래에서 **게이트웨이 만들기**를 클릭하고 **동적 라우팅**을 클릭합니다.
4. **예**를 클릭하여 게이트웨이 만들기를 시작합니다.
5. 게이트웨이를 만드는 동안 기다립니다. 경우에 따라 완료하는 데 45분 이상 걸릴 수 있습니다.
6. 게이트웨이를 만든 후에 **대시보드** 페이지에서 게이트웨이 IP 주소를 볼 수 있습니다. 이것이 게이트웨이의 공용 IP 주소입니다. 공용 IP 주소를 적어두거나 복사합니다. Resource Manager VNet 구성에 대한 로컬 네트워크를 만드는 이후 단계에서 사용할 수 있습니다.
7. 필요에 따라 이 cmdlet을 사용하여 게이트웨이 설정을 검색하고 게이트웨이의 상태를 확인할 수 있습니다. `Get-AzureVirtualNetworkGateway`

#### PowerShell을 사용하여 게이트웨이를 만들려면

PowerShell을 사용하여 게이트웨이를 만들려면 다음 예제를 사용합니다.

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting


## <a name="creatermgw"></a>섹션 2: RM VNet 게이트웨이 구성

RM VNet에 대한 VPN 게이트웨이를 만들려면 다음 지침을 따르세요. 클래식 VNet의 게이트웨이에 대한 공용 IP 주소를 검색할 때까지 단계를 시작하지 않습니다.

1. PowerShell 콘솔에서 **Azure 계정에 로그인합니다**. 다음 cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정이 다운로드됩니다.

		Login-AzureRmAccount 

 	둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

		Get-AzureRmSubscription

	사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **로컬 네트워크 게이트웨이 만들기**. 가상 네트워크에서 로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 이 경우에 로컬 네트워크 게이트웨이는 클래식 VNet을 가리킵니다. Azure에서 참조할 수 있는 이름을 지정하고 주소 공간 접두사를 지정합니다. Azure는 지정된 IP 주소 접두사를 사용하여 온-프레미스 위치로 보낼 트래픽을 식별합니다. 나중에 게이트웨이를 만들기 전에 여기서 정보를 조정하는 경우 값을 수정하고 샘플을 다시 실행할 수 있습니다.<br><br>
	- `-Name`은 로컬 네트워크 게이트웨이에 참조하기 위해 할당하려는 이름입니다.<br>
	- `-AddressPrefix`은 클래식 VNet에 대한 주소 공간입니다.<br>
	- `-GatewayIpAddress`은 클래식 VNet의 게이트웨이 공용 IP 주소입니다. 다음 샘플이 올바른 IP 주소를 반영하도록 변경해야 합니다.
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. Azure Resource Manager VNet VPN 게이트웨이에 할당할 **공용 IP 주소를 요청**합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다. 그러나 이로 인해 IP 주소가 변경되지는 않습니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 Azure VPN 게이트웨이 IP 주소가 변경됩니다. 크기 조정, 다시 설정 또는 Azure VPN 게이트웨이의 기타 내부 유지 관리/업그레이드를 변경하지 않습니다.<br>이 단계에서는 이후 단계에 사용되는 변수도 설정합니다.


		$ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
		-ResourceGroupName RG1 -Location 'EastUS' `
		-AllocationMethod Dynamic

4. **가상 네트워크에 게이트웨이 서브넷이 있는지 확인합니다**. 게이트웨이 서브넷이 없다면 추가합니다. 게이트웨이 서브넷의 이름을 *GatewaySubnet*으로 지정해야 합니다.

5. 아래 명령을 실행하여 게이트웨이에 사용되는 **서브넷을 검색**합니다. 이 단계에서는 다음 단계에서 사용할 변수도 설정합니다.
	- `-Name`은 Resource Manager VNet의 이름입니다.
	- `-ResourceGroupName`은 VNet과 연결된 리소스 그룹입니다. 제대로 작동하려면 게이트웨이 서브넷이 이 VNet에 이미 존재하고 이름을 *GatewaySubnet*으로 지정해야 합니다.


			$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **게이트웨이 IP 주소 지정 구성을 만듭니다**. 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 다음 샘플을 사용하여 게이트웨이 구성을 만듭니다.<br>이 단계에서 `-SubnetId` 및 `-PublicIpAddressId` 매개 변수는 서브넷 및 IP 주소 개체에서 각각 ID 속성을 전달해야 합니다. 간단한 문자열을 사용할 수 없습니다. 이러한 변수는 공용 IP를 요청하는 단계 및 서브넷을 검색하는 단계에서 설정됩니다.

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name gwipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id
	
7. 다음 명령을 실행하여 **Resource Manager VNet 게이트웨이를 만듭니다**. `-VpnType`는 *RouteBased*여야 합니다.

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. VPN 게이트웨이를 만들면 **공용 IP 주소를 복사**합니다. 클래식 VNet에 대한 로컬 네트워크 설정을 구성할 때 사용합니다. 다음 cmdlet을 사용하여 공용 IP 주소를 검색할 수 있습니다. 공용 IP 주소가 *IpAddress*로 반환에 나열됩니다.

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## 섹션 3: 클래식 VNet에 대한 로컬 네트워크 수정

네트워크 구성 파일을 내보내고 편집한 다음 저장하고 Azure에 파일을 다시 가져와서 이 단계를 수행할 수 있습니다. 또는 클래식 포털에서 이 설정을 수정할 수 있습니다.

### 포털에서 수정하려면

1. [클래식 포털](https://manage.windowsazure.com)을 엽니다.

2. 클래식 포털의 왼쪽에서 아래로 스크롤하여 **네트워크**를 클릭합니다. **네트워크** 페이지에서 페이지의 위쪽에 있는 **로컬 네트워크**를 클릭합니다.

3. **로컬 네트워크** 페이지에서 1부에 구성한 로컬 네트워크를 선택하도록 클릭한 다음 페이지 맨 아래에 이동하고 **편집**을 클릭합니다.

4. **로컬 네트워크 세부 정보 지정** 페이지에서 자리 표시자 IP 주소를 이전 섹션에서 만든 Resource Manager 게이트웨이의 공용 IP 주소로 바꿉니다. 화살표를 클릭하여 다음 섹션으로 이동합니다. **주소 공간**이 올바른지 확인하고 변경 내용을 수용한다는 확인 표시를 클릭합니다.

### 네트워크 구성 파일을 사용하여 수정하려면

1. 네트워크 구성 파일을 내보냅니다.
2. 텍스트 편집기에서 VPN 게이트웨이 IP 주소를 수정합니다.

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. 변경 내용을 저장하고 Azure에 다시 편집한 파일을 가져옵니다.


## <a name="connect"></a>섹션 4: 게이트웨이 간의 연결 만들기

게이트웨이 간의 연결을 만들려면 PowerShell이 필요합니다. Azure 계정을 추가하여 클래식 PowerShell cmdlet을 사용해야 합니다. 이렇게 하려면 다음 cmdlet 예제를 사용할 수 있습니다.
		
	Add-AzureAccount

1. 다음 명령을 실행하여 **공유 키를 설정**합니다. 이 샘플에서는 `-VNetName`는 클래식 VNet의 이름이고 `-LocalNetworkSiteName`은 클래식 포털에서 구성한 경우 로컬 네트워크에 대해 지정한 이름입니다. `-SharedKey`는 생성하고 지정할 수 있는 값입니다. 여기에서 지정한 값은 연결을 만드는 다음 단계에서 지정한 것과 동일한 값이어야 합니다. 이 샘플에 대한 반환은 상태:성공을 표시해야 합니다.

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. 다음 명령을 실행하여 VPN 연결을 만듭니다.

	**변수 설정**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**연결 만들기**<br> `-ConnectionType`은 Vnet2Vnet이 아닌 IPsec입니다.
		
		New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet을 사용하여 포털에서 연결을 볼 수 있습니다.

		Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>VNet 간 FAQ

VNet 간 연결에 대한 자세한 내용은 FAQ 세부 정보를 봅니다.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016-->
