<properties 
   pageTitle="포털에서 Resource Manager 가상 네트워크에 클래식 가상 네트워크를 연결하는 방법 | Microsoft Azure"
   description="VPN 게이트웨이 및 포털을 사용하여 클래식 VNet 및 Resource Manager VNet 간에 VPN 연결을 만드는 방법을 알아봅니다"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />


# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>포털에서 다양한 배포 모델에서 가상 네트워크 연결

> [AZURE.SELECTOR]
- [포털](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure에는 현재 클래식 및 RM(Resource Manager) 등 두 개의 관리 모델이 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 이후의 VM 및 역할 인스턴스는 Resource Manager에서 만들 VNet에서 실행되고 있을 것입니다. 이 문서에서는 Resource Manager VNet에 클래식 VNet을 연결하여 별도 배포 모델에 있는 리소스가 게이트웨이 연결을 통해 서로 통신하도록 허용하는 과정을 안내합니다. 

다른 구독 및 다른 지역에 있는 VNet 간에 연결을 만들 수 있습니다. 또한 함께 구성된 게이트웨이가 동적이거나 경로 기반이면 온-프레미스 네트워크에 이미 연결된 VNet을 연결할 수 있습니다. VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 FAQ](#faq) 를 참조하세요.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 간 연결 배포 모델 및 메서드

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

새 문서 및 추가 도구를 이 구성에 사용할 수 있게 되었으므로 다음 테이블을 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.<br><br>

**VNet-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet 피어링

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>시작하기 전에

다음 단계에서는 각 VNet에 대한 동적 또는 경로 기반 게이트웨이를 구성하고 게이트웨이 간에 VPN 연결을 만드는 데 필요한 설정을 안내합니다. 이 구성은 고정 또는 정책 기반 게이트웨이를 지원하지 않습니다. 

이 문서에서는 클래식 포털, Azure 포털 및 PowerShell을 사용합니다. 현재, Azure 포털만을 사용해서 이 구성을 만들 수는 없습니다.

### <a name="prerequisites"></a>필수 조건

 - 두 VNet이 이미 생성되었습니다.
 - VNet에 대한 주소 범위는 서로 겹치거나 게이트웨이가 연결되어 있는 다른 연결의 범위와 겹치지 않습니다.
 - 최신 PowerShell cmdlet(1.0.2 이상)을 설치했습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 을 참조하세요. SM(서비스 관리)와 RM(Resource Manager) cmdlet을 모두 설치해야 합니다. 

### <a name="<a-name="values"></a>example-settings"></a><a name="values"></a>예제 설정

예제 설정을 참조로 사용할 수 있습니다.

**클래식 VNet 설정**

VNet 이름 = ClassicVNet  <br>
Location = West US <br>
Virtual Network Address Spaces = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>

**Resource Manager VNet 설정**

VNet 이름 = RMVNet  <br>
Resource Group = RG1 <br>
Virtual Network IP Address Spaces = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = East US <br>
Virtual network gateway name = RMGateway <br>
Gateway public IP name = gwpip <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
Local network gateway = ClassicVNetLocal <br>

## <a name="<a-name="createsmgw"></a>section-1:-configure-classic-vnet-settings"></a><a name="createsmgw"></a>섹션 1: 클래식 VNet 설정 구성

이 섹션에서는 클래식 VNet에 대한 로컬 네트워크와 게이트웨이를 만듭니다. 이 섹션의 지침은 클래식 포털을 사용합니다. 현재, Azure 포털은 클래식 VNet와 관련된 모든 설정을 제공하지 않습니다.

### <a name="part-1---create-a-new-local-network"></a>1부 - 새 로컬 네트워크 만들기

[클래식 포털](https://manage.windowsazure.com) 을 열고 Azure 계정으로 로그인합니다.

1. 화면 왼쪽 아래에서 **새로 만들기** > **네트워크 서비스** > **가상 네트워크** > **로컬 네트워크 추가**를 클릭합니다.

2. **로컬 네트워크 세부 정보 지정** 창에서 연결하려는 RM VNet의 이름을 입력합니다. **VPN 장치 IP 주소(선택 사항)** 상자에 유효한 공용 IP 주소를 입력합니다. 임시 자리 표시자일 뿐입니다. 나중에 이 IP 주소를 변경합니다. 창의 오른쪽 아래 모서리에 있는 화살표 단추를 클릭합니다.
 
3. **주소 공간 지정** 페이지의 **시작 IP** 텍스트 상자에 연결하려는 Resource Manager VNet의 네트워크 접두사와 CIDR 블록을 입력합니다. 이 설정을 사용하여 RM VNet에 라우트하도록 주소 공간을 지정합니다.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>2부 - VNet에 로컬 네트워크 연결

1. 페이지 맨 위에 있는 **가상 네트워크** 를 클릭하여 가상 네트워크 화면으로 전환한 다음 클릭하여 클래식 VNet을 선택합니다. VNet에 대한 페이지에서 **구성** 을 클릭하여 구성 페이지로 이동합니다.

2. **사이트 간 연결** 연결 섹션에서 **로컬 네트워크에 연결** 확인란을 선택합니다. 그런 다음 만든 로컬 네트워크를 선택합니다. 여러 개의 로컬 네트워크를 만든 경우 드롭다운 목록에서 Resource Manager VNet을 나타내도록 만든 로컬 네트워크를 선택해야 합니다.

3. 페이지 맨 아래에서 **저장** 을 클릭합니다.

### <a name="part-3---create-the-gateway"></a>3부 - 게이트웨이 만들기

1. 설정을 저장한 후에 페이지 맨 위에 있는 **대시보드** 를 클릭하여 대시보드 페이지를 변경합니다. 대시보드 페이지 아래에서 **게이트웨이 만들기**를 클릭하고 **동적 라우팅**을 클릭합니다. **예** 를 클릭하여 게이트웨이 만들기를 시작합니다. 동적 라우팅 게이트웨이가 이 구성에 필요합니다.

2. 게이트웨이를 만드는 동안 기다립니다. 경우에 따라 완료하는 데 45분 이상 걸릴 수 있습니다.

### <a name="<a-name="ip"></a>part-4---view-the-gateway-public-ip-address"></a><a name="ip"></a>4부 - 게이트웨이 공용 IP 주소 보기

게이트웨이를 만든 후에 **대시보드** 페이지에서 게이트웨이 IP 주소를 볼 수 있습니다. 이것이 게이트웨이의 공용 IP 주소입니다. 공용 IP 주소를 적어두거나 복사합니다. Resource Manager VNet 구성에 대한 로컬 네트워크를 만드는 이후 단계에서 사용할 수 있습니다.


## <a name="<a-name="creatermgw"></a>section-2:-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>섹션 2: Resource Manager VNet 설정 구성

이 섹션에서는 Resource Manager VNet에 대한 가상 네트워크 게이트웨이 및 로컬 네트워크를 만듭니다. 클래식 VNet의 게이트웨이에 대한 공용 IP 주소를 검색할 때까지 다음 단계를 시작하지 않습니다.

스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다. 게이트웨이를 만들 때 이 [값](#values)을 참조합니다.


### <a name="part-1---create-a-gateway-subnet"></a>1부 - 게이트웨이 서브넷 만들기

가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. CIDR 개수가 /28 이상인 게이트웨이 서브넷을 만듭니다(/27, /26 등).

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>2부 - 가상 네트워크 게이트웨이 만들기


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>3부 - 로컬 네트워크 게이트웨이 만들기

'로컬 네트워크 게이트웨이'는 일반적으로 온-프레미스 위치를 가리킵니다. 어떤 IP 주소의 범위가 위치 및 해당 위치에 대한 장치의 공용 IP 주소에 대한 경로에 이르는지 Azure에 알려 줍니다. 그러나 이 경우에 클래식 VNet 및 가상 네트워크 게이트웨이에 연결된 주소 범위 및 공용 IP 주소를 가르킵니다.

Azure에서 참조할 수 있는 이름을 로컬 네트워크 게이트웨이에 지정합니다. 가상 네트워크 게이트웨이를 만드는 동안 로컬 네트워크 게이트웨이를 만들 수 있습니다. 이 구성의 경우 [이전 섹션](#ip)에서 클래식 VNet 게이트웨이에 할당된 공용 IP 주소를 사용합니다.

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>4부 - 공용 IP 주소 복사

가상 네트워크 게이트웨이를 만드는 작업을 완료하면 게이트웨이와 연결된 공용 IP 주소를 복사합니다. 클래식 VNet에 대한 로컬 네트워크 설정을 구성할 때 사용합니다. 


## <a name="section-3:-modify-the-local-network-for-the-classic-vnet"></a>섹션 3: 클래식 VNet에 대한 로컬 네트워크 수정

[클래식 포털](https://manage.windowsazure.com)을 엽니다.

2. 클래식 포털의 왼쪽에서 아래로 스크롤하여 **네트워크**를 클릭합니다. **네트워크** 페이지에서 페이지의 위쪽에 있는 **로컬 네트워크**를 클릭합니다. 

3. 1 부에서 구성한 로컬 네트워크를 선택하도록 클릭합니다. 페이지의 아래쪽에서 **편집**을 클릭합니다.

4. **로컬 네트워크 세부 정보 지정** 페이지에서 자리 표시자 IP 주소를 이전 섹션에서 만든 Resource Manager 게이트웨이의 공용 IP 주소로 바꿉니다. 화살표를 클릭하여 다음 섹션으로 이동합니다. **주소 공간** 이 올바른지 확인하고 변경 내용을 수용한다는 확인 표시를 클릭합니다.

## <a name="<a-name="connect"></a>section-4:-create-the-connection"></a><a name="connect"></a>섹션 4: 연결 만들기

이 섹션에서는 VNet 간의 연결을 만듭니다. 이 단계에서는 PowerShell이 필요합니다. 포털에서 이 연결을 만들 수 없습니다. 클래식(SM) 및 Resource Manager(RM) PowerShell cmdlet을 모두 다운로드하고 설치해야 합니다.


1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 다음 cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정이 다운로드됩니다.

        Login-AzureRmAccount 

    둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

        Get-AzureRmSubscription

    사용할 구독을 지정합니다. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Azure 계정을 추가하여 클래식 PowerShell cmdlet을 사용합니다. 이렇게 하려면 다음 명령을 사용할 수 있습니다.

        Add-AzureAccount

3. 다음 샘플을 실행하여 공유 키를 설정합니다. 이 샘플에서는 `-VNetName`는 클래식 VNet의 이름이고 `-LocalNetworkSiteName`은 클래식 포털에서 구성한 경우 로컬 네트워크에 대해 지정한 이름입니다. `-SharedKey` 는 생성하고 지정할 수 있는 값입니다. 여기에서 지정한 값은 연결을 만드는 다음 단계에서 지정한 것과 동일한 값이어야 합니다.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. 다음 명령을 실행하여 VPN 연결을 만듭니다.
    
    **변수 설정**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **연결 만들기**<br> `-ConnectionType`은 ‘Vnet2Vnet’이 아닌 ‘IPsec’입니다. 이 샘플에서는 `-Name` 는 연결을 호출하려는 이름입니다. 다음 예제에서는 '*rm-to-classic-connection*'이라는 연결을 만듭니다.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>연결 확인

클래식 포털, Azure 포털 또는 PowerShell을 사용하여 연결을 확인할 수 있습니다. 다음 단계를 사용하여 연결을 확인할 수 있습니다. 사용자 고유의 값으로 대체합니다.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="<a-name="faq"></a>vnet-to-vnet-faq"></a><a name="faq"></a>VNet 간 FAQ

VNet 간 연결에 대한 자세한 내용은 FAQ 세부 정보를 봅니다.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 





<!--HONumber=Oct16_HO2-->


