---
title: "지점 및 사이트 간을 사용하여 Azure Virtual Network에 연결: Azure Portal: Classic | Microsoft Docs"
description: "Azure Portal을 사용하여 지점 및 사이트 간 VPN Gateway 연결을 만들어 클래식 Azure Virtual Network에 안전하게 연결합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bc542cfbca3a27aec98b75e2b7ed05472419c3a7
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Azure Portal(클래식)을 사용하여 VNet에 지점 및 사이트 간 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S는 SSTP를 통한 VPN 연결입니다(보안 소켓 터널링 프로토콜). 지점 및 사이트 간 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다. P2S 연결에는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 VPN 연결을 설정합니다.

이 문서에서는 Azure 포털을 사용하여 클래식 배포 모델에서 지점 및 사이트 간 연결로 VNet을 만드는 방법을 안내합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [지점 및 사이트 간 FAQ](#faq)를 참조하세요.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 P2S 구성에 대한 두 가지 배포 모델과 사용할 수 있는 배포 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>기본 워크플로
![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

다음 섹션은 가상 네트워크에 보안 지점 및 사이트 간 연결을 만드는 단계를 안내합니다.

1. 가상 네트워크 및 VPN Gateway 만들기
2. 인증서 생성
3. .cer 파일 업로드
4. VPN 클라이언트 구성 패키지 생성
5. 클라이언트 컴퓨터 구성
6. Azure에 연결

### <a name="example-settings"></a>예제 설정
다음 예제 설정을 사용할 수 있습니다.

* **이름: VNet1**
* **주소 공간: 192.168.0.0/16**<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름: FrontEnd**
* **서브넷 주소 범위: 192.168.1.0/24**
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **연결 형식: 지점 및 사이트 간**
* **클라이언트 주소 공간: 172.16.201.0/24**. 이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 지정된 풀에서 IP 주소를 받습니다.
* **GatewaySubnet: 192.168.200.0/24**. 게이트웨이 서브넷의 이름으로 "GatewaySubnet"을 사용해야 합니다.
* **크기:** 사용할 게이트웨이 SKU를 선택합니다.
* **라우팅 유형: 동적**



## <a name="vnetvpn"></a>섹션 1 - 가상 네트워크 및 VPN 게이트웨이 만들기

시작하기 전에 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
### <a name="createvnet"></a>1부: 가상 네트워크 만들기
가상 네트워크가 아직 없는 경우 만듭니다. 스크린샷은 예제로 제공됩니다. 사용자 고유의 값으로 대체해야 합니다. Azure 포털을 사용하여 VNet을 만들려면 다음 단계를 사용하세요.

1. 브라우저에서 [Azure 포털](http://portal.azure.com) 로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **새로 만들기**를 클릭합니다. **마켓플레이스 검색** 필드에 "Virtual Network"를 입력합니다. 반환된 목록에서 **Virtual Network**를 찾아서 클릭하여 **Virtual Network** 블레이드를 엽니다.

    ![가상 네트워크 블레이드 검색](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Virtual Network 블레이드의 아래쪽 근처의 **배포 모델 선택** 목록에서 **클래식**을 선택한 다음 **만들기**를 클릭합니다.

    ![배포 모델 선택](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. **가상 네트워크 만들기** 블레이드에서 VNet 설정을 구성합니다. 이 블레이드에서 첫 번째 주소 공간과 단일 서브넷 주소 범위를 추가합니다. VNet 만들기를 완료한 후에 다시 돌아와서 추가 서브넷 및 주소 공간을 추가합니다.

    ![가상 네트워크 만들기 블레이드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. **구독** 이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
6. **리소스 그룹** 을 클릭하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹에 대한 이름을 입력하여 새로 만듭니다. 새 그룹을 만드는 경우 계획된 구성 값에 따라 리소스 그룹의 이름을 지정합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
7. 다음으로 VNet에 대한 **위치** 설정을 선택합니다. 이 위치는 이 VNet에 배포할 리소스가 배치될 곳을 결정합니다.
8. 대시보드에서 VNet을 쉽게 찾을 수 있으려면 **대시보드에 고정**을 선택한 다음 **만들기**를 클릭합니다.

    ![대시보드에 고정](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. 만들기를 클릭한 후에 VNet의 진행 상황을 반영하는 대시보드에 타일이 표시됩니다. 타일은 VNet이 생성되면서 변경됩니다.

    ![가상 네트워크 만들기 타일](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. 가상 네트워크를 만든 후 이름 확인을 처리하기 위해 DNS 서버의 IP 주소를 추가할 수 있습니다. 가상 네트워크에 대한 설정을 열고 DNS 서버를 클릭하고 사용할 DNS 서버의 IP 주소를 추가합니다. 이 설정은 새 DNS 서버를 만들지 않습니다. 리소스와 통신할 수 있는 DNS 서버를 추가해야 합니다.

가상 네트워크가 만들어지면 Azure 클래식 포털의 네트워크 페이지에 있는 **상태**에 **생성됨**이 나타납니다.

### <a name="gateway"></a>2부: 게이트웨이 서브넷 및 동적 라우팅 게이트웨이 만들기
이 단계에서는 게이트웨이 서브넷 및 동적 라우팅 게이트웨이를 만듭니다. 클래식 배포 모델을 위한 Azure 포털에서 게이트웨이 서브넷 및 게이트웨이 만들기는 동일한 구성 블레이드를 통해 수행할 수 있습니다.

1. 포털에서 게이트웨이를 만들려는 가상 네트워크로 이동합니다.
2. 가상 네트워크에 대한 블레이드에 대해, **개요** 블레이드의 VPN 연결 섹션에서 **게이트웨이**를 클릭합니다.

    ![클릭하여 게이트웨이 서브넷 만들기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. **새 VPN 연결** 블레이드에서 **지점 및 사이트 간**을 선택합니다.

    ![지점 및 사이트 간 연결 형식](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. **클라이언트 주소 공간**에 대해 IP 주소 범위를 추가합니다. VPN 클라이언트가 연결할 때 IP 주소를 수신할 범위입니다. 자동으로 채워진 범위를 삭제하고 직접 추가합니다.

    ![클라이언트 주소 공간](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. **게이트웨이 즉시 만들기** 확인란을 선택합니다.

    ![게이트웨이 즉시 만들기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. **선택적 게이트웨이 구성**을 클릭하여 **게이트웨이 구성** 블레이드를 엽니다.

    ![선택적 게이트웨이 구성 클릭](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. **서브넷 필수 설정 구성**을 클릭하여 **게이트웨이 서브넷**을 클릭합니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 적어도 /28 또는 /27을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 이렇게 하면 나중에 필요할 수도 있는 추가 구성에 맞게 충분히 주소를 사용할 수 있습니다.

   > [!IMPORTANT]
   > 게이트웨이 서브넷에서 작업할 때는 게이트웨이 서브넷에 NSG(네트워크 보안 그룹)를 연결하지 않습니다. 이 서브넷에 네트워크 보안 그룹을 연결하면 VPN Gateway가 정상적으로 작동하지 않을 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹이란?](../virtual-network/virtual-networks-nsg.md)을 참조하세요.
   >
   >

    ![GatewaySubnet 추가](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. 게이트웨이 **크기**를 선택합니다. 가상 네트워크 게이트웨이를 만드는 데 사용할 게이트웨이 SKU입니다. 포털에서 SKU 기본값은 **기본**입니다. 게이트웨이 SKU에 대한 자세한 내용은 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

    ![게이트웨이 크기](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. 게이트웨이에 대한 **라우팅 유형**을 선택합니다. P2S 구성에는 **동적** 라우팅 유형이 필요합니다. 이 블레이드 구성을 완료했으면 **확인**을 클릭합니다.

    ![라우팅 유형 구성](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. **새 VPN 연결** 블레이드의 하단에 **확인**을 클릭하여 가상 네트워크 게이트웨이 만들기를 시작합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.

## <a name="generatecerts"></a>섹션 2 - 인증서 만들기
인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. 루트 인증서를 만든 후 Base-64 인코딩된 X.509 .cer 파일로 공용 인증서 데이터(개인 키 아님)를 내보냅니다. 그런 다음 루트 인증서에서 Azure로 공용 인증서 데이터를 업로드합니다.

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 클라이언트 인증서는 루트 인증서에서 생성되고 각 클라이언트 컴퓨터에 설치됩니다. 유효한 클라이언트 인증서가 설치되어 있지 않고 클라이언트가 VNet에 연결하려고 하는 경우 인증이 실패합니다.

### <a name="cer"></a>1부: 루트 인증서용 공개 키(.cer) 가져오기

####<a name="enterprise-certificate"></a>엔터프라이즈 인증서
 
엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 사용할 루트 인증서용 .cer 파일을 가져옵니다.

####<a name="self-signed-root-certificate"></a>자체 서명된 루트 인증서

엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만들어야 합니다. P2S 인증에 필요한 필드가 포함된 자체 서명된 인증서를 만들기 위해 PowerShell을 사용할 수 있습니다. [PowerShell을 사용하여 지점 및 사이트 간 연결을 위해 자체 서명된 인증서 만들기](vpn-gateway-certificates-point-to-site.md)에 자체 서명된 루트 인증서를 만드는 단계가 안내되어 있습니다.

> [!NOTE]
> 이전에 makecert는 자체 서명된 루트 인증서를 만들고 지점 및 사이트 간 연결에 대한 클라이언트 인증서를 생성하는 데 권장된 메서드였습니다. 이제 이러한 인증서를 만드는 데 PowerShell을 사용할 수 있습니다. PowerShell을 사용하는 이점 중 하나는 SHA-2 인증서를 만드는 기능입니다. 필요한 값은 [PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 자체 서명된 인증서 만들기](vpn-gateway-certificates-point-to-site.md)를 참조하세요.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>자체 서명된 루트 인증서에 대한 공개 키를 내보내려면

지점 및 사이트 간 연결에는 Azure에 업로드될 공개 키(.cer)가 필요합니다. 다음 단계는 자체 서명된 루트 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 자체 서명된 루트 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭합니다. **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)**를 선택한 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다. **내보내기에 성공했습니다**라는 메시지가 표시됩니다. **확인**을 클릭하여 마법사를 닫습니다.

### <a name="genclientcert"></a>2부: 클라이언트 인증서 생성

연결할 각 클라이언트의 고유한 인증서를 생성할 수도 있고 여러 클라이언트에서 동일한 인증서를 사용할 수도 있습니다. 고유한 클라이언트 인증서를 생성하면 필요할 경우 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않은 경우 즉, 모든 구성원이 동일한 클라이언트 인증서를 사용하고 한 클라이언트에 대한 인증서를 해지해야 하는 경우, 인증서를 사용하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다.

####<a name="enterprise-certificate"></a>엔터프라이즈 인증서
- 엔터프라이즈 인증서 솔루션을 사용하는 경우, 'domain name\username' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com'으로 클라이언트 인증서를 생성합니다.
- 발급한 클라이언트 인증서가 사용 목록의 첫 번째 항목으로 스마트 카드 로그온 등이 아닌 '클라이언트 인증'을 가지는 ‘사용자' 인증서 템플릿을 기준으로 하는지 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**을 확인하여 인증서를 확인할 수 있습니다.

####<a name="self-signed-root-certificate"></a>자체 서명된 루트 인증서 
자체 서명된 루트 인증서를 사용하는 경우 지점 및 사이트 간 연결과 호환되는 클라이언트 인증서를 생성하는 단계는 [PowerShell을 사용하여 클라이언트 인증서 생성](vpn-gateway-certificates-point-to-site.md#clientcert)을 참조하세요.

### <a name="exportclientcert"></a>3부 - 클라이언트 인증서 내보내기
[PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) 지침을 사용하여 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 경우 생성하는 데 사용한 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 내보내야 합니다.

1. 클라이언트 인증서를 내보내려면 **certmgr.msc**를 엽니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 클릭합니다.
3. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지합니다. **가능하면 인증 경로에 있는 인증서 모두 포함**을 선택했는지 확인합니다. 그런 후 **Next**를 클릭합니다.
4. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억합니다. 그런 후 **Next**를 클릭합니다.
5. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
6. **마침** 을 클릭하여 인증서를 내보냅니다.

## <a name="upload"></a>섹션 3 - 루트 인증서 .cer 파일 업로드
게이트웨이가 생성되었으면 신뢰할 수 있는 루트 인증서에 대한 .cer 파일을 Azure에 업로드할 수 있습니다. 최대 20개의 루트 인증서에 대한 파일을 업로드할 수 있습니다. 루트 인증서에 대한 개인 키를 Azure에 업로드하지 않습니다. .cer 파일이 업로드되었으면 Azure에서는 이 파일을 사용하여 가상 네트워크에 연결할 클라이언트를 인증합니다.

1. VNet에 대한 블레이드의 **VPN 연결** 섹션에서 **클라이언트** 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 블레이드를 엽니다.

    ![클라이언트](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **지점 및 사이트 간 연결** 블레이드에서 **인증서 관리**를 클릭하여 **인증서** 블레이드를 엽니다.<br>

    ![인증서 블레이드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **인증서** 블레이드에서 **업로드**를 클릭하여 **인증서 업로드** 블레이드를 엽니다.<br>

    ![인증서 업로드 블레이드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. 폴더 그래픽을 클릭하여 .cer 파일을 찾아봅니다. 파일을 선택한 후 **확인**을 클릭합니다. 페이지를 새로 고쳐 **인증서** 블레이드에서 업로드된 인증서를 확인합니다.

    ![인증서 업로드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>섹션 4 - VPN 클라이언트 구성 패키지 생성
가상 네트워크에 연결하려면 VPN 클라이언트도 구성해야 합니다. 클라이언트 컴퓨터에 연결하기 위해 클라이언트 인증서와 적절한 VPN 클라이언트 구성 패키지가 모두 필요합니다.

VPN 클라이언트 패키지는 Windows에 기본 제공되는 VPN 클라이언트 소프트웨어를 구성할 구성 정보를 포함합니다. 패키지는 추가 소프트웨어를 설치하지 않습니다. 설정은 연결하려는 가상 네트워크에 따라 다릅니다. 지원되는 클라이언트 운영 체제의 목록은 이 문서 끝의 [지점 및 사이트 간 연결 FAQ](#faq)를 참조하세요.

### <a name="to-generate-the-vpn-client-configuration-package"></a>VPN 클라이언트 구성 패키지를 생성하려면
1. Azure 포털에서 VNet에 대한 **개요** 블레이드의 **VPN 연결**에서 클라이언트 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 블레이드를 엽니다.
2. **지점 및 사이트 간 VPN 연결** 블레이드 맨 위에서 설치할 클라이언트 운영 체제에 해당하는 다운로드 패키지를 선택합니다.

   * 64비트 클라이언트인 경우 **VPN 클라이언트(64비트)**를 선택합니다.
   * 32비트 클라이언트인 경우 **VPN 클라이언트(32비트)**를 선택합니다.

     ![VPN 클라이언트 구성 패키지 다운로드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Azure에서 가상 네트워크에 대한 VPN 클라이언트 구성 패키지를 생성한다는 메시지가 표시됩니다. 몇 분 후 패키지가 생성되고 로컬 컴퓨터에 패키지가 다운로드되었다는 메시지가 표시됩니다. 구성 패키지 파일을 저장합니다. P2S를 사용하여 가상 네트워크에 연결하려는 각 클라이언트 컴퓨터에 이 파일을 설치합니다.

## <a name="clientconfiguration"></a>섹션 5 - 클라이언트 컴퓨터 구성
### <a name="part-1-install-an-exported-client-certificate"></a>1부: 내보낸 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치할 때는 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. *.pfx* 파일을 찾아 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. **저장소 위치**를 **현재 사용자**로 유지한 후 **다음**을 클릭합니다.
2. 가져올 **파일** 페이지에서 아무 것도 변경하지 않습니다. **다음**을 클릭합니다.
3. **개인 키 보호** 페이지에서 인증서를 사용한 경우 인증서에 대한 암호를 입력하거나 인증서를 설치하는 보안 주체가 올바른지 확인한 후 **다음**을 클릭합니다.
4. **인증서 저장소** 페이지에서 기본 위치를 유지한 후 **다음**을 클릭합니다.
5. **Finish**를 클릭합니다. 인증서 설치에 대한 **보안 경고**에서 **예**를 클릭합니다. 인증서를 생성했으므로 '예'를 클릭하면 됩니다. 이제 인증서를 성공적으로 가져왔습니다.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>2부: VPN 클라이언트 구성 패키지 설치
버전이 클라이언트에 대한 아키텍처와 일치하는 경우 각 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다.

1. 구성 파일을 가상 네트워크에 연결할 컴퓨터에 로컬로 복사합니다. 
2. .exe 파일을 두 번 클릭하여 클라이언트 컴퓨터에 패키지를 설치합니다. 구성 패키지를 직접 만들었으므로 서명되지 않았습니다. 다시 말해 경고가 표시될 수 있습니다. Windows SmartScreen 팝업이 나타나면 **자세한 정보**(왼쪽), **실행**을 차례로 클릭하여 패키지를 설치합니다.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. 나열된 연결이 표시됩니다. 연결할 가상 네트워크의 이름이 다음과 비슷하게 표시됩니다.

    ![VPN 클라이언트](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>섹션 6 - Azure에 연결
### <a name="connect-to-your-vnet"></a>VNet에 연결
1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속** 을 클릭하여 상승된 권한을 사용합니다.
2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

    ![VPN 클라이언트 연결](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. 이제 연결이 설정됩니다.

    ![설정된 연결](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> 엔터프라이즈 CA 솔루션을 사용하여 발급된 인증서를 사용하고 인증에 문제가 발생하는 경우 클라이언트 인증서에서 인증 순서를 확인합니다. 클라이언트 인증서를 두 번 클릭하고 **세부 정보 > 확장된 키 사용**으로 이동하여 인증 목록 순서를 확인할 수 있습니다. 목록의 첫 번째 항목으로 '클라이언트 인증'이 표시되는지 확인합니다. 그렇지 않으면 목록에서 첫 번째 항목으로 클라이언트 인증을 가진 사용자 템플릿을 기반으로 하는 클라이언트 인증서를 발급해야 합니다. 
>
>

### <a name="verify-the-vpn-connection"></a>VPN 연결 확인
1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 VNet을 만들 때 지정한 지점 및 사이트 간 연결 주소 범위 내의 주소 중 하나인지 확인합니다. 결과는 다음과 같아야 합니다.

예제:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>신뢰할 수 있는 루트 인증서 추가 또는 제거

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

### <a name="to-add-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 추가하려면

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 지침은 [섹션 3 - 루트 인증서 .cer 파일 업로드](#upload)를 참조하세요.

### <a name="to-remove-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 제거하려면


1. VNet에 대한 블레이드의 **VPN 연결** 섹션에서 **클라이언트** 그래픽을 클릭하여 **지점 및 사이트 간 VPN 연결** 블레이드를 엽니다.

    ![클라이언트](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **지점 및 사이트 간 연결** 블레이드에서 **인증서 관리**를 클릭하여 **인증서** 블레이드를 엽니다.<br>

    ![인증서 블레이드](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **인증서** 블레이드에서 제거할 인증서 옆에 있는 줄임표를 클릭한 다음 **삭제**를 클릭합니다.

     ![루트 인증서 삭제](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>클라이언트 인증서 해지
클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지해야 루트 인증서로부터 생성된 다른 인증서를 지점 및 사이트 간 연결을 위한 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="to-revoke-a-client-certificate"></a>클라이언트 인증서를 해지하려면

해지 목록에 지문을 추가하여 클라이언트 인증서를 해지할 수 있습니다.

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [방법: 인증서의 지문 검색](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
2. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다.
3. **'클래식 가상 네트워크 이름' > 지점 및 사이트 간 VPN 연결 > 인증서** 블레이드로 이동한 다음 **해지 목록**을 클릭하여 해지 목록 블레이드를 엽니다. 
4. **해지 목록** 블레이드에서 **+인증서 추가**를 클릭하여 **해지 목록에 인증서 추가** 블레이드를 엽니다.
5. **해지 목록에 인증서 추가** 블레이드에서 인증서 지문을 공백 없이 연속적인 텍스트 줄로 붙여 넣습니다. 블레이드 아래쪽에서 **확인**을 클릭합니다.
6. 업데이트가 완료된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에게는 인증서가 더 이상 유효하지 않다는 메시지가 표시됩니다.


## <a name="faq"></a>지점 및 사이트 간 FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요. 네트워킹 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.

