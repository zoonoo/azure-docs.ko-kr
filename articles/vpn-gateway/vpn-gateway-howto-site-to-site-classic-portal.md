---
title: "온-프레미스 네트워크를 Azure Virtual Network에 연결: 사이트 간 VPN(Classic): Portal | Microsoft Docs"
description: "공용 인터넷을 통해 온-프레미스 네트워크에서 Azure Virtual Network에 IPsec을 만드는 단계입니다. 이 단계는 포털을 사용하여 크로스-프레미스 사이트 간 VPN 게이트웨이 연결을 만드는 데 도움이 됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure Portal(클래식)을 사용하여 사이트 간 연결 만들기
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)
>
>


S2S(사이트 간) VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통한 연결입니다. 이 연결 유형은 할당된 공용 IP 주소를 가지고 NAT 다음에 위치하지 않는 온-프레미스에 있는 VPN 장치를 필요로 합니다. 사이트간 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

이 문서에서는 클래식 배포 모델 및 Azure Portal을 사용하여 온-프레미스 네트워크에 대한 가상 네트워크와 사이트 간 VPN Gateway 연결을 만드는 과정을 안내합니다. 

![사이트 간 VPN 게이트웨이 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>사이트 간 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 현재 사용할 수 있는 배포 모델 및 사이트 간 구성을 위한 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>추가 구성
VNet을 서로 연결하되 온-프레미스 위치에는 연결하지 않으려는 경우 [VNet간 연결 구성](virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요. 이미 연결되어 있는 VNet에 사이트 간 연결을 추가하려는 경우 [기존 VPN 게이트웨이와 연결된 VNet에 S2S 연결 추가](vpn-gateway-multi-site.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* 호환되는 VPN 장치 및 구성할 수 있는 사람. [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.
* VPN 장치에 대한 외부 연결 공용 IPv4 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](http://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* 현재 공유 키를 지정하고 VPN Gateway 연결을 만드는 데 PowerShell이 필요합니다. 최신 버전의 Azure SM(서비스 관리) PowerShell cmdlet을 설치합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요. 이 구성에 PowerShell을 사용할 때 관리자 권한으로 실행되고 있는지 확인합니다. 

> [!NOTE]
> 사이트 간 연결을 구성할 때 VPN 장치에 공용 IPv4 IP 주소가 필요합니다.                                                                                                                                                                               
>
>

### <a name="values"></a>이 연습에 대한 샘플 구성 값
연습으로 이러한 단계를 사용하는 경우 샘플 구성 값을 사용할 수 있습니다.

* **VNet 이름:** TestVNet1
* **주소 공간:** 
    * 10.11.0.0/16
    * 10.12.0.0/16(이 연습의 선택 사항)
* **서브넷:**
  * 프런트 엔드: 10.11.0.0/24
  * 백 엔드: 10.12.0.0/24(이 연습의 선택 사항)
* **게이트웨이 서브넷:** 10.11.255.0/27
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부
* **DNS 서버:** 8.8.8.8(이 연습의 선택 사항)
* **로컬 사이트 이름:** Site2

## <a name="CreatVNet"></a>1. 가상 네트워크 만들기

S2S 연결에 사용할 가상 네트워크를 만들 때 지정한 주소 공간이 연결하려는 로컬 사이트의 클라이언트 주소 공간과 겹치지 않는지 확인해야 합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다.

* VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다. 

* 가상 네트워크가 아직 없는 경우 만듭니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다.

### <a name="to-create-a-virtual-network"></a>가상 네트워크를 만들려면

1. 브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **새로 만들기**를 클릭합니다. **마켓플레이스 검색** 필드에 "Virtual Network"를 입력합니다. 반환된 목록에서 **Virtual Network**를 찾아서 클릭하여 **Virtual Network** 블레이드를 엽니다.

    ![가상 네트워크 블레이드 검색](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Virtual Network 블레이드의 아래쪽 근처의 **배포 모델 선택** 목록에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.

    ![배포 모델 선택](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. **가상 네트워크 만들기** 블레이드에서 VNet 설정을 구성합니다. 이 블레이드에서 첫 번째 주소 공간과 단일 서브넷 주소 범위를 추가합니다. VNet 만들기를 완료한 후에 다시 돌아와서 추가 서브넷 및 주소 공간을 추가합니다.

    ![가상 네트워크 블레이드 만들기](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Create virtual network blade")
5. **구독** 이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
6. **리소스 그룹** 을 클릭하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹에 대한 이름을 입력하여 새로 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
7. 다음으로 VNet에 대한 **위치** 설정을 선택합니다. 이 위치는 VNet에 배포하는 리소스가 상주할 곳을 결정합니다.
8. 대시보드에서 VNet을 쉽게 찾을 수 있으려면 **대시보드에 고정**을 선택한 다음 **만들기**를 클릭합니다.

    ![대시보드에 고정](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Pin to dashboard")
9. '만들기'를 클릭한 후에 VNet의 진행 상황을 반영하는 대시보드에 타일이 표시됩니다. 타일은 VNet이 생성되면서 변경됩니다.

    ![가상 네트워크 타일 만들기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "가상 네트워크 만들기")

가상 네트워크가 만들어지면 Azure 클래식 포털의 네트워크 페이지에 있는 **상태**에 **생성됨**이 나타납니다.

## <a name="additionaladdress"></a>2. 다른 주소 공간 추가

가상 네트워크를 만든 후에 다른 주소 공간을 추가할 수 있습니다. 다른 주소 공간을 추가하는 작업은 S2S 구성의 일부는 아니지만 여러 개의 주소 공간이 필요한 경우 다음 단계를 사용하세요.

1. 포털에서 가상 네트워크를 찾습니다.
2. 가상 네트워크의 블레이드에서 **설정** 섹션 아래에 있는 **주소 공간**을 클릭합니다.
3. 주소 공간 블레이드에서 **+추가**를 클릭하여 다른 주소 공간을 입력합니다.
 
## <a name="dns"></a>3. DNS 서버 지정
DNS 설정이 S2S 구성의 일부가 아니지만 이름을 확인하려는 경우 DNS가 필요합니다.

가상 네트워크를 만든 후 이름 확인을 처리하기 위해 DNS 서버의 IP 주소를 추가할 수 있습니다. 가상 네트워크에 대한 설정을 열고 DNS 서버를 클릭하고 이름을 확인하는 데 사용할 DNS 서버의 IP 주소를 추가합니다. 이 설정은 DNS 서버를 만들지 않습니다. 설정 예제에서는 공용 DNS 서버를 사용합니다. 일반적으로 개인 DNS 서버를 사용하려고 합니다. 리소스와 통신할 수 있는 DNS 서버를 추가해야 합니다.

1. 포털에서 가상 네트워크를 찾습니다.
2. 가상 네트워크의 블레이드에서 **설정** 섹션 아래에 있는 **DNS 서버**를 클릭합니다.
3. DNS 서버를 추가합니다.
4. 페이지의 맨 위에서 **저장**을 클릭하여 설정을 저장합니다.
 
## <a name="localsite"></a>4. 로컬 사이트 구성

로컬 사이트는 일반적으로 온-프레미스 위치를 가리킵니다. VPN 장치의 IP 주소를 포함하며 여기에서 VPN 장치에 대한 VPN Gateway를 통해 라우팅되는 연결 및 IP 주소 범위를 만듭니다.

1. 포털에서 게이트웨이를 만들려는 가상 네트워크로 이동합니다.
2. 가상 네트워크에 대한 블레이드에 대해, **개요** 블레이드의 VPN 연결 섹션에서 **게이트웨이**를 클릭하여 **새 VPN 연결** 블레이드를 엽니다.

    ![클릭하여 게이트웨이 설정 구성](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "클릭하여 게이트웨이 설정 구성")
3. **새 VPN 연결** 블레이드에서 **사이트 간**을 선택합니다.

    ![사이트 간 클릭](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "사이트 간")
4. **로컬 사이트 - 필수 설정 구성**을 클릭하여 **로컬 사이트** 블레이드를 엽니다. 설정을 구성한 다음 **확인**을 클릭하여 설정을 저장합니다.
    - **이름:** 쉽게 식별할 수 있도록 로컬 사이트의 이름을 만듭니다.
    - **VPN Gateway IP 주소:** 온-프레미스 네트워크에 대한 VPN 장치의 공용 IP 주소입니다. VPN 장치에는 IPv4 공용 IP 주소가 필요합니다. 연결하려는 VPN 장치에 유효한 공용 IP 주소를 지정합니다. NAT 뒤에 있을 수 없고 Azure에서 도달할 수 있어야 합니다.
    - **클라이언트 주소 공간:** 이 게이트웨이 통해 로컬 온-프레미스 네트워크에 라우팅할 IP 주소 범위를 나열합니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 자체 가상 네트워크가 연결된 다른 네트워크의 범위 또는 가상 네트워크 자체의 주소 범위와 겹치지 않도록 합니다.

    ![로컬 사이트](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "로컬 사이트 구성")

## <a name="gatewaysubnet"></a>5. 게이트웨이 서브넷 구성

VPN Gateway의 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 VPN Gateway 서비스가 사용할 IP 주소를 포함합니다.

1. **새 VPN 연결** 블레이드에서 **게이트웨이 즉시 만들기** 확인란을 선택합니다. '선택적 게이트웨이 구성' 블레이드가 표시됩니다. 확인란을 선택하지 않은 경우 게이트웨이 서브넷을 구성하기 위한 블레이드를 볼 수 없습니다.

    ![게이트웨이 구성 - 서브넷, 크기, 라우팅 유형](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "게이트웨이 구성 - 서브넷, 크기, 라우팅 유형")
2. **선택적 게이트웨이 구성 - 서브넷, 크기 및 라우팅 유형**을 클릭하여 **게이트웨이 구성** 블레이드를 엽니다.
3. **게이트웨이 구성** 블레이드에서 **서브넷 - 필수 설정 구성**을 클릭하여 **서브넷 추가** 블레이드를 엽니다.

    ![게이트웨이 구성 - 게이트웨이 서브넷](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "게이트웨이 구성 - 게이트웨이 서브넷")
4. **서브넷 추가** 블레이드에서 게이트웨이 서브넷을 추가합니다. 게이트웨이 서브넷을 추가할 때 가능하면 /28 또는/27의 CIDR 블록을 사용하여 게이트웨이 서브넷을 만드는 것이 좋습니다. 이렇게 하면 앞으로 추가 구성 요구 사항에 맞는 충분한 IP 주소를 갖게 됩니다.  **확인** 을 클릭하여 설정을 저장합니다.

    ![게이트웨이 서브넷 추가](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "게이트웨이 서브넷 추가")

## <a name="sku"></a>6. SKU와 VPN 유형 지정
1. 게이트웨이 **크기**를 선택합니다. 가상 네트워크 게이트웨이를 만드는 데 사용할 게이트웨이 SKU입니다. 포털에서 'SKU 기본값'은 **기본**입니다. 게이트웨이 SKU에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

    ![SKUL 및 VPN 유형 선택](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "SKU 및 VPN 유형 선택")
2. 게이트웨이에 대한 **라우팅 유형**을 선택합니다. VPN 유형이라고도 합니다. 게이트웨이를 한 가지 유형에서 다른 유형으로 변환할 수 없기 때문에 올바른 게이트웨이 유형을 선택해야 합니다. VPN 장치는 선택한 라우팅 유형과 호환되어야 합니다. VPN 유형에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)를 참조하세요. '경로 기반' 및 '정책 기반' VPN 유형을 참조하는 문서를 볼 수 있습니다. '동적'은 '경로 기반'에 해당하고 '고정'은 '정책 기반'에 해당합니다.
3. **확인** 을 클릭하여 설정을 저장합니다.
4. **새 VPN 연결** 블레이드의 하단에 **확인**을 클릭하여 가상 네트워크 게이트웨이 만들기를 시작합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.

## <a name="vpndevice"></a>7. VPN 장치 구성

특정 구성 정보는 장치 제조업체에 문의하고 장치를 구성합니다. Azure와 잘 작동하는 VPN 장치에 대한 자세한 내용은 [VPN 장치](vpn-gateway-about-vpn-devices.md) 를 참조합니다. 또한 사용하려는 VPN 장치에 대한 [알려진 장치 호환성 문제](vpn-gateway-about-vpn-devices.md#known)를 확인합니다. 

VPN 장치를 구성할 때 만든 VPN Gateway의 IP 주소가 필요합니다. 가상 네트워크의 **개요**로 이동하여 IP 주소를 찾을 수 있습니다.

## <a name="CreateConnection"></a>8. 연결 만들기
이 단계에서는 공유 키를 설정하고 연결을 만듭니다. 설정한 키는 VPN 장치 구성에 사용된 동일한 키여야 합니다.

> [!NOTE]
> 현재 이 단계는 Azure Portal에서 지원되지 않습니다. Azure PowerShell cmdlet의 SM(서비스 관리) 버전을 사용해야 합니다.                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>1단계. Azure 계정에 연결

1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

        Login-AzureRmAccount
2. 계정에 대한 구독을 확인합니다.

        Get-AzureRmSubscription
3. 둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. SM 버전의 PowerShell cmdlet를 추가합니다.

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>2단계. 공유 키 설정 및 연결 만들기

PowerShell 및 클래식 배포 모델을 사용하는 경우 포털에 있는 리소스의 이름은 종종 PowerShell을 사용하는 경우에 Azure에서 예상하는 이름이 아닐 수 있습니다. 다음 단계를 통해 네트워크 구성 파일을 내보내서 이름의 정확한 값을 가져올 수 있습니다.

1. 컴퓨터에 디렉터리를 만들고 디렉터리에 네트워크 구성 파일을 내보냅니다. 이 예제에서는 네트워크 구성 파일을 C:\AzureNet으로 내보냅니다.

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. xml 편집기에서 네트워크 구성 파일을 열고 'LocalNetworkSite 이름' 및 'VirtualNetworkSite 이름'의 값을 확인합니다. 값을 반영하도록 예제를 수정합니다. 공백이 포함된 이름을 지정할 때는 값을 작은따옴표로 묶습니다.

3. 공유 키를 설정하고 연결을 만듭니다. '-SharedKey'는 사용자가 생성하고 지정하는 값입니다. 이 예제에서는 'abc123'을 사용했지만 좀 더 복잡한 항목을 생성하여 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 장치를 구성하는 경우 지정한 것과 동일한 값이어야 한다는 것입니다.

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
연결이 생성된 경우 결과는 **상태: 성공**입니다.

## <a name="verify"></a>9. 연결 확인

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.


