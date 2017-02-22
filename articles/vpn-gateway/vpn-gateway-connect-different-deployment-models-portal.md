---
title: "Azure Resource Manager VNet에 클래식 가상 네트워크를 연결하는 방법: 포털 | Microsoft Docs"
description: "VPN 게이트웨이 및 포털을 사용하여 클래식 VNet 및 Resource Manager VNet 간에 VPN 연결을 만드는 방법을 알아봅니다"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>포털을 사용하여 다양한 배포 모델에서 가상 네트워크 연결
> [!div class="op_single_selector"]
> * [포털](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure에는 현재 클래식 및 RM(Resource Manager) 등 두 개의 관리 모델이 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 이후의 VM 및 역할 인스턴스는 Resource Manager에서 만들 VNet에서 실행되고 있을 것입니다. 이 문서에서는 Resource Manager VNet에 클래식 VNet을 연결하여 별도 배포 모델에 있는 리소스가 게이트웨이 연결을 통해 서로 통신하도록 허용하는 과정을 안내합니다. 

다른 구독 및 다른 지역에 있는 VNet 간에 연결을 만들 수 있습니다. 또한 함께 구성된 게이트웨이가 동적이거나 경로 기반이면 온-프레미스 네트워크에 이미 연결된 VNet을 연결할 수 있습니다. VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 고려 사항](#faq) 섹션을 참조하세요.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 간 연결 배포 모델 및 메서드
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

새 문서 및 추가 도구를 이 구성에 사용할 수 있게 되었으므로 다음 테이블을 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.<br><br>

**VNet-VNet**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 피어링**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>시작하기 전에
다음 단계에서는 각 VNet에 대한 동적 또는 경로 기반 게이트웨이를 구성하고 게이트웨이 간에 VPN 연결을 만드는 데 필요한 설정을 안내합니다. 이 구성은 고정 또는 정책 기반 게이트웨이를 지원하지 않습니다. 

이 문서에서는 Azure Portal 및 PowerShell을 사용합니다. 가상 네트워크 간에 연결을 만드는 데 PowerShell이 필요합니다. 포털에서는 이 구성에 대한 연결을 만들 수 없습니다.

### <a name="prerequisites"></a>필수 조건
* 두 VNet이 이미 생성되었습니다.
* VNet에 대한 주소 범위는 서로 겹치거나 게이트웨이가 연결되어 있는 다른 연결의 범위와 겹치지 않습니다.
* 최신 PowerShell cmdlet을 설치했습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요. SM(서비스 관리)와 RM(Resource Manager) cmdlet을 모두 설치해야 합니다. 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>예제 설정
예제 설정을 참조로 사용할 수 있습니다.

**클래식 VNet 설정**

VNet 이름 = ClassicVNet  <br>
Address space = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Resource Group = ClassicRG <br>
Location = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Resource Manager VNet 설정**

VNet 이름 = RMVNet  <br>
Address space = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Resource Group = RG1 <br>
Location = East US <br>
Virtual network gateway name = RMGateway <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
Gateway public IP name = gwpip <br>
Local network gateway = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>섹션 1: 클래식 VNet 설정 구성
이 섹션에서는 클래식 VNet에 대한 로컬 네트워크(로컬 사이트) 및 가상 네트워크 게이트웨이를 만듭니다. 이 섹션의 지침은 Azure Portal을 사용합니다. 여기에 나오는 단계는 VPN 게이트웨이가 클래식 VNet에 대해 생성되지 않았다고 가정합니다. 게이트웨이가 이미 있는 경우 동적 게이트웨이인지 확인합니다. 정적이면 먼저 VPN 게이트웨이를 삭제한 후 다음 단계를 진행합니다.

### <a name="part-1---configure-the-local-site"></a>1부 - 로컬 사이트 구성

[클래식 포털](https://ms.portal.azure.com)을 열고 Azure 계정으로 로그인합니다.

1. **모든 리소스**로 이동하고 클래식 가상 네트워크를 찾습니다.
2. **개요** 블레이드의 **VPN 연결** 섹션에서 **게이트웨이** 그래픽을 클릭하여 게이트웨이를 만듭니다.

    ![VPN 게이트웨이 구성](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN 게이트웨이 구성")
3. **새 VPN 연결** 블레이드에서 **연결 유형**으로 **사이트 간**을 선택합니다.
4. **로컬 사이트**로 **필요한 설정 구성**을 클릭합니다. 이렇게 하면 **로컬 사이트** 블레이드가 열립니다.
5. **로컬 사이트** 블레이드에서 Resource Manager VNet을 참조하는 데 사용하는 이름을 만듭니다. 예를 들어 RMVNetLocal과 같습니다.
6. Resource Manager VNet에 대한 VPN 게이트웨이에 공용 IP 주소가 있는 경우 **VPN 게이트웨이 IP 주소** 필드의 값을 사용합니다. Resource Manager VNet에 대해 가상 네트워크 게이트웨이를 아직 만들지 않은 경우 자리 표시자 IP 주소를 사용할 수 있습니다. 자리 표시자 IP 주소 형식이 올바른지 확인합니다. 나중에 자리 표시자 IP 주소를 Resource Manager 가상 네트워크 게이트웨이의 공용 IP 주소로 바꿔야 합니다.
7. **클라이언트 주소 공간**에 Resource Manager VNet에 대한 가상 네트워크 IP 주소 공간 값을 사용합니다. 이 설정은 RM VNet으로 라우팅될 주소 공간을 지정하는 데 사용합니다.
8. **확인**을 클릭하여 값을 저장하고 **새 VPN 연결** 블레이드로 돌아갑니다.

### <a name="part-2---create-the-virtual-network-gateway"></a>2부 - 가상 네트워크 게이트웨이 만들기
1. **새 VPN 연결** 블레이드에서 **게이트웨이 즉시 만들기** 확인란을 선택하고 **선택적 게이트웨이 구성**을 클릭하여 **게이트웨이 구성** 블레이드를 엽니다. 

    ![게이트웨이 구성 블레이드 열기](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "게이트웨이 구성 블레이드 열기")
2. **서브넷 - 필요한 설정 구성**을 클릭하여 **서브넷 추가** 블레이드를 엽니다. 이 블레이드에서 이름이 필요한 값인 **GatewaySubnet**으로 이미 구성되어 있음을 알 수 있습니다.
3. **주소 범위**는 게이트웨이 서브넷에 대한 범위를 나타냅니다. /29 주소 범위(3개의 주소)를 갖는 게이트웨이 서브넷을 만들 수 있지만 추가 IP 주소를 요구할 수 있는 향후 구성을 고려해서 더 많은 가용 IP 주소를 포함하는 게이트웨이 서브넷을 만드는 것이 좋습니다. 가능하면 /27 또는 /28을 사용합니다. **확인**을 클릭하여 게이트웨이 서브넷을 만듭니다.
4. **게이트웨이 구성** 블레이드에서 **크기**는 게이트웨이 SKU를 나타냅니다. VPN 게이트웨이에 대한 게이트웨이 SKU를 선택합니다.
5. **라우팅 유형**이 **동적**인지 확인하고 **확인**을 클릭하여 **새 VPN 연결** 블레이드로 돌아갑니다.
6. **새 VPN 연결** 블레이드에서 **확인**을 클릭하여 VPN 게이트웨이 만들기를 시작합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>3부 - 가상 네트워크 게이트웨이 공용 IP 주소 복사
가상 네트워크 게이트웨이를 만든 후에 게이트웨이 IP 주소를 볼 수 있습니다. 

1. 클래식 VNet으로 이동한 후 **개요**를 클릭합니다.
2. **VPN 연결**을 클릭하여 VPN 연결 블레이드를 엽니다. VPN 연결 블레이드에서 공용 IP 주소를 볼 수 있습니다. 이 주소는 가상 네트워크 게이트웨이에 할당된 공용 IP 주소입니다. 공용 IP 주소를 적어두거나 복사합니다. 나중에 Resource Manager 로컬 네트워크 게이트웨이 구성 설정 관련 작업을 수행할 때 필요합니다. 게이트웨이 연결의 상태를 볼 수도 있습니다. 만든 로컬 네트워크 사이트는 '연결 중'으로 표시됩니다. 연결을 만든 후에는 상태가 변경됩니다. 게이트웨이 IP 주소를 복사한 후 블레이드를 닫습니다.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>섹션 2: Resource Manager VNet 설정 구성
이 섹션에서는 Resource Manager VNet에 대한 가상 네트워크 게이트웨이 및 로컬 네트워크를 만듭니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다. 게이트웨이를 만들 때 이 [값](#values)을 참조합니다.

### <a name="part-1---create-a-gateway-subnet"></a>1부 - 게이트웨이 서브넷 만들기
가상 네트워크 게이트웨이를 구성하려면 먼저 게이트웨이 서브넷을 만들어야 합니다. CIDR 개수가 /28 이상인 게이트웨이 서브넷을 만듭니다. (/27, /26 등)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>2부 - 가상 네트워크 게이트웨이 만들기
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

하나의 가상 네트워크 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. Resource Manager 가상 네트워크 게이트웨이 만들기가 완료될 때까지 기다리거나 [3부 - 로컬 네트워크 게이트웨이 만들기](#createlng)를 계속 진행할 수 있습니다. 게이트웨이가 만들어지면 공용 IP 주소를 할당합니다. 이후 단계에서 이 IP 주소는 섹션 1에서 만든 클래식 VNet 로컬 사이트 설정에 대한 자리 표시자 IP 주소 정보 대신 사용됩니다. 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>3부 - 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 클래식 VNet 및 가상 네트워크 게이트웨이에 연결된 주소 범위 및 공용 IP 주소를 지정합니다.

- [이전 섹션](#ip)에서 클래식 VNet 게이트웨이에 할당된 공용 IP 주소를 사용합니다. 
- Azure에서 참조할 수 있는 이름을 로컬 네트워크 게이트웨이에 지정합니다. 예를 들어 'ClassicVNetLocal'과 같습니다.
- 클래식 VNet(단지 서브넷이 아님)에 할당된 주소 공간을 사용합니다.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>섹션 3: 클래식 VNet 로컬 사이트 설정 수정

이 섹션에서는 클래식 VNet을 사용합니다. 로컬 사이트 설정을 지정할 때 사용한 자리 표시자 IP 주소를 바꿉니다. 이 섹션에서는 클래식(SM) PowerShell cmdlet을 사용합니다. 아직 Resource Manager VNet에 대한 가상 네트워크 게이트웨이 확인하고 공용 IP 주소를 복사하는 작업을 수행하지 않았으면 지금 수행합니다. 이 주소는 자리 표시자 IP 주소 대신 사용할 IP 주소입니다.

[시작하기 전에](#before) 섹션에 지정된 대로 이러한 cmdlet의 최신 버전을 다운로드했는지 확인합니다.

1. Azure Portal에서 클래식 가상 네트워크로 이동합니다.
2. 가상 네트워크에 대한 블레이드에서 **개요**를 클릭합니다.
3. **VPN 연결** 섹션에서 그래픽의 로컬 사이트 이름을 클릭합니다.

    ![VPN-connections](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 연결")
4. **사이트 간 VPN 연결** 블레이드에서 사이트의 이름을 클릭합니다.

    ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "로컬 사이트 이름")
5. 로컬 사이트에 대한 연결 블레이드에서 로컬 사이트의 이름을 클릭하여 **로컬 사이트** 블레이드를 엽니다.

    ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "로컬 사이트 열기")
6. **로컬 사이트** 블레이드에서 VPN 게이트웨이 IP 주소를 Resource Manager 게이트웨이의 IP 주소로 바꿉니다.

    ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "게이트웨이 IP 주소")
7. **확인**을 클릭하여 IP 주소를 업데이트합니다.


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>섹션 4: 연결 만들기
이 섹션에서는 VNet 간에 연결을 만듭니다. 이러한 단계에는 PowerShell이 필요합니다. 포털에서는 이 연결을 만들 수 없습니다. 클래식(SM) 및 Resource Manager(RM) PowerShell cmdlet을 모두 다운로드하고 설치해야 합니다.

### <a name="part-1---log-in-to-your-azure-account"></a>1부 - Azure 계정에 로그인

1. 관리자 권한으로 PowerShell 콘솔을 열고 Azure 계정에 로그인합니다. 다음 cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정이 다운로드됩니다.
   
        Login-AzureRmAccount 
   
2. 둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.
   
        Get-AzureRmSubscription
   
3. 사용할 구독을 지정합니다. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Azure 계정을 추가하여 클래식 PowerShell cmdlet을 사용합니다. 이렇게 하려면 다음 명령을 사용할 수 있습니다.
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>2부 - 네트워크 구성 파일 다운로드

배포 모델의 차이 때문에 Azure Portal에서 클래식 Vnet 설정을 만들 때 네트워크 구성 파일에서 클래식 Vnet 및 로컬 네트워크 사이트의 이름이 변경되는 경우가 있습니다. 예를 들어 Azure Portal을 사용하여 'Classic VNet’인 클래식 VNet을 'ClassicRG'라는 리소스 그룹에서 만들었습니다. 네트워크 구성 파일에 포함된 이름은 'Group ClassicRG Classic VNet'으로 변환됩니다. 공백이 포함된 VNet의 이름을 지정할 때는 값을 따옴표로 묶습니다.

1. 다음 명령을 실행하여 Azure 네트워크 구성 파일을 내보냅니다. 필요한 경우 다른 위치로 내보낼 파일의 위치를 변경할 수 있습니다.
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. 메모장과 같은 텍스트 편집기를 사용하여 .xml 파일을 열고 내용을 확인합니다. 클래식 가상 네트워크 및 로컬 네트워크 사이트의 이름 값을 확인합니다.

### <a name="part-3---set-the-shared-key"></a>3부 - 공유 키 설정

클래식 VNet에서 Resource Manager VNet으로의 연결에 대한 공유 키를 설정합니다. 이러한 cmdlet을 사용하는 경우 네트워크 구성 파일을 사용하여 `-VNetName` 및 `-LocalNetworkSiteName` 값을 확인해야 합니다. 공백이 포함된 이름을 지정할 때는 값을 따옴표로 묶습니다. 

- 이 예제에서는 `-VNetName`이 클래식 VNet의 이름입니다. 
- `-LocalNetworkSiteName`은 로컬 사이트에 대해 지정한 이름입니다.
- `-SharedKey`는 사용자가 생성하고 지정하는 값입니다. 이 예제에서는 *abc123*을 사용했으나 좀 더 복잡한 항목을 생성할 수 있습니다. 중요한 점은 여기에서 지정한 값이 연결을 만드는 다음 단계에서 지정한 것과 동일한 값이어야 한다는 것입니다.

PowerShell 콘솔에서 다음 샘플을 실행하고 값을 원하는 값으로 바꾸어 공유 키를 설정합니다. 이 샘플에 대한 반환은 **상태:성공**을 표시해야 합니다.
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>4부 - 다음 명령을 실행하여 VPN 연결 만들기
   
1. 변수를 설정합니다.
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. 연결을 만듭니다. 이 예제에서 `-Name`은 이미 만든 이름이 아니라 연결에 대해 지정하려는 이름입니다. 다음 예제에서는 'RM-Classic'이라는 연결을 만듭니다. `-ConnectionType`은 'Vnet2Vnet'이 아니라 'IPsec'이고, `-SharedKey`는 앞서 설정한 키와 일치합니다.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>섹션 5: 연결 확인
Azure Portal 또는 PowerShell을 사용하여 연결을 확인할 수 있습니다. 확인 시, 연결이 만들어지는 동안&1;~2분 정도 기다려야 할 수 있습니다. 연결이 성공적으로 수행되면 연결 상태가 '연결 중'에서 '연결됨'으로 바뀝니다.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>클래식 VNet에서 Resource Manager VNet으로의 연결을 확인하려면

####<a name="verify-your-connection-using-powershell"></a>PowerShell을 사용하여 연결 확인


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 연결 확인

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet에서 클래식 VNet으로의 연결을 확인하려면

####<a name="verify-your-connection-using-powershell"></a>PowerShell을 사용하여 연결 확인

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 연결 확인

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>VNet 간 고려 사항

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


