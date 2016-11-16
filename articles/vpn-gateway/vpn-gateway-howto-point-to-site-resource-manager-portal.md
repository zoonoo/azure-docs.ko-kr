---
title: "Resource Manager 배포 모델 및 Azure 포털을 사용하여 가상 네트워크에 지점 및 사이트 간 VPN Gateway 연결 구성 | Microsoft Docs"
description: "Resource Manager 및 Azure 포털을 사용하여 지점 및 사이트 간 VPN Gateway 연결을 만들어 Azure Virtual Network에 안전하게 연결합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aeebed0c733b9fbac964cdc532ff9d364683609b


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Azure Portal을 사용하여 VNet에 지점 및 사이트 간 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다. 

지점 및 사이트 간 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 연결을 시작하여 VPN 연결을 설정합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 및 [계획 및 설계](vpn-gateway-plan-design.md)를 참조하세요.

이 문서에서는 Azure 포털을 사용하여 Resource Manager 배포 모델에서 지점 및 사이트 간 연결로 VNet을 만드는 방법을 안내합니다.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 P2S 구성에 대한 두 가지 배포 모델과 사용할 수 있는 배포 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>기본 워크플로
![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>예제 값
* **이름: VNet1**
* **주소 공간: 192.168.0.0/16**<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름: FrontEnd**
* **서브넷 주소 범위: 192.168.1.0/24**
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹: TestRG**
* **위치: 미국 동부**
* **GatewaySubnet: 192.168.200.0/24**
* **가상 네트워크 게이트웨이 이름: VNet1GW**
* **게이트웨이 유형: VPN**
* **VPN 유형: 경로 기반**
* **공용 IP 주소: VNet1GWpip**
* **연결 형식: 지점 및 사이트 간**
* **클라이언트 주소 풀: 172.16.201.0/24**<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 클라이언트 주소 풀에서 IP 주소를 받습니다.

## <a name="before-beginning"></a>시작하기 전에
* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

## <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>1부 - 가상 네트워크 만들기
연습으로 이 구성을 만드는 경우 [예제 값](#example)을 참조하세요.

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

### <a name="2-add-additional-address-space-and-subnets"></a>2. 다른 주소 공간 및 서브넷 추가
VNet이 만들어지면 여기에 다른 주소 공간 및 서브넷을 추가할 수 있습니다.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

### <a name="3-create-a-gateway-subnet"></a>3. 게이트웨이 서브넷 만들기
가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 향후 추가적인 구성 요구 사항을 수용하기에 충분한 IP 주소를 제공하기 위하여 가능하면 /28 또는 /27 CIDR 블록을 사용하여 게이트웨이 서브넷을 만드는 것이 가장 좋습니다.

이 섹션의 스크린샷은 참조 예제로 제공됩니다. 구성에 필요한 값에 해당하는 GatewaySubnet 주소 범위를 사용해야 합니다.

#### <a name="to-create-a-gateway-subnet"></a>게이트웨이 서브넷을 만들려면
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="a-namednsa4-specify-a-dns-server-optional"></a><a name="dns"></a>4. DNS 서버 지정(선택 사항)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2부 - 가상 네트워크 게이트웨이 만들기
지점 및 사이트 간 연결에는 다음 설정이 필요합니다.

* 게이트웨이 유형: VPN
* VPN 유형: 경로 기반

### <a name="to-create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이를 만들려면
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-3-generate-certificates"></a><a name="generatecert"></a>3부 - 인증서 생성
인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하기 위해 Azure에 의해 사용됩니다. 엔터프라이즈 인증서 솔루션에 의해 생성된 루트 인증서 또는 자체 서명된 루트 인증서에서 Base-64로 인코딩된 X.509 .cer 파일로 공용 인증서 데이터(개인 키 아님)를 내보냅니다. 그런 다음 루트 인증서에서 Azure로 공용 인증서 데이터를 가져옵니다. 또한 클라이언트에 대한 루트 인증서에서 클라이언트 인증서를 생성해야 합니다. P2S 연결을 사용하여 가상 네트워크에 연결하려는 각 클라이언트에 루트 인증서에서 생성된 클라이언트 인증서가 설치되어 있어야 합니다.

### <a name="a-namegetcera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. 루트 인증서용 .cer 파일 가져오기
엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 엔터프라이즈 CA 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만들 수 있습니다. 자체 서명된 인증서를 만드는 한 가지 메서드는 makecert입니다.

* 엔터프라이즈 인증서 시스템을 사용하는 경우, 사용하려는 루트 인증서용 .cer 파일을 가져옵니다. 
* 엔터프라이즈 인증서 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 생성해야 합니다. Windows 10 단계는 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md)을 참조할 수 있습니다.

1. 인증서에서 .cer 파일을 가져오려면 **certmgr.msc**를 열고 루트 인증서를 찾습니다. 자체 서명된 루트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 후 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다.
2. 마법사에서 **다음**을 클릭하고 **아니요, 개인 키를 내보내지 않습니다.**를 선택한 후 **다음**을 클릭합니다.
3. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)**를 선택합니다. 그런 후 **다음**을 클릭합니다. 
4. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **Next**를 클릭합니다.
5. **마침** 을 클릭하여 인증서를 내보냅니다.

### <a name="a-namegenerateclientcerta2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. 클라이언트 인증서 생성
연결할 각 클라이언트의 고유한 인증서를 생성할 수도 있고 여러 클라이언트에서 동일한 인증서를 사용할 수도 있습니다. 고유한 클라이언트 인증서를 생성하면 필요할 경우 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않은 경우 즉, 모든 구성원이 동일한 클라이언트 인증서를 사용하고 한 클라이언트에 대한 인증서를 해지해야 하는 경우, 인증서를 사용하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다.

* 엔터프라이즈 인증서 솔루션을 사용하는 경우, 'domain name\username' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com',으로 클라이언트 인증서를 생성합니다. 
* 자체 서명된 인증서를 사용하는 경우 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md) 을 참조하여 클라이언트 인증서를 생성합니다.

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. 클라이언트 인증서 내보내기
인증하려면 클라이언트 인증서가 필요합니다. 클라이언트 인증서를 생성한 후 내보냅니다. 내보내는 클라이언트 인증서는 각 클라이언트 컴퓨터에 나중에 설치됩니다.

1. 클라이언트 인증서를 내보내려면 *certmgr.msc*를 사용할 수 있습니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭합니다.
2. 개인 키로 클라이언트 인증서를 내보냅니다. 이는 *.pfx* 파일입니다. 이 인증서에 대해 설정한 암호(키)를 기억 하거나 기록해야 합니다.

## <a name="a-nameaddresspoolapart-4-add-the-client-address-pool"></a><a name="addresspool"></a>4부 - 클라이언트 주소 풀 추가
1. 가상 네트워크 게이트웨이가 생성된 후에는 가상 네트워크 게이트웨이 블레이드의 **설정** 섹션으로 이동합니다. **설정** 섹션에서 **지점 및 사이트 간 구성**을 클릭하여 **구성** 블레이드를 엽니다.
   
    ![지점 및 사이트 간 블레이드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "point to site blade")
2. **주소 풀**은 연결된 클라이언트가 IP 주소를 수신할 IP 주소 풀입니다. 주소 풀을 추가한 후 **저장**을 클릭합니다.
   
    ![클라이언트 주소 풀](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "client address pool")

## <a name="a-nameuploadfileapart-5-upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>5부 - 루트 인증서 .cer 파일 업로드
게이트웨이가 생성되었으면 신뢰할 수 있는 루트 인증서에 대한 .cer 파일을 Azure에 업로드할 수 있습니다. 최대 20개의 루트 인증서에 대한 파일을 업로드할 수 있습니다. 루트 인증서에 대한 개인 키를 Azure에 업로드하지 않습니다. .cer 파일이 업로드되었으면 Azure에서는 이 파일을 사용하여 가상 네트워크에 연결할 클라이언트를 인증합니다.

1. **지점 및 사이트 간 구성** 블레이드로 이동합니다. 이 블레이드의 **루트 인증서** 섹션에 .cer 파일을 추가합니다.
   
    ![지점 및 사이트 간 블레이드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "point to site blade")
2. 루트 인증서를 Base-64 인코딩된 X.509(.cer) 파일로 내보내야 합니다. 이 형식으로 내보내야 텍스트 편집기에서 인증서를 열 수 있습니다.
3. 메모장과 같은 텍스트 편집기에서 인증서를 엽니다. 다음 섹션만 복사합니다.
   
    ![인증서 데이터](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "certificate data")
4. 인증서 데이터를 포털의 **공용 인증서 데이터** 섹션에 붙여 넣습니다. 인증서 이름을 **이름** 공간에 입력한 후 **저장**을 클릭합니다. 최대 20개의 신뢰할 수 있는 루트 인증서를 추가할 수 있습니다.
   
    ![인증서 업로드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "certificate upload")

## <a name="a-nameclientconfigapart-6-download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>6부 - VPN 클라이언트 구성 패키지 다운로드 및 설치
P2S를 사용하여 Azure에 연결된 클라이언트에는 클라이언트 인증서와 VPN 클라이언트 구성 패키지가 설치되어야 합니다. VPN 클라이언트 구성 패키지는 Windows 클라이언트에 사용할 수 있습니다. 

VPN 클라이언트 패키지는 Windows에 기본 제공되는 VPN 클라이언트 소프트웨어를 구성할 정보를 포함합니다. 구성은 연결하려는 VPN에 따라 다릅니다. 패키지는 추가 소프트웨어를 설치하지 않습니다. 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 를 참조하세요.

1. **지점 및 사이트 간 구성** 블레이드에서 **VPN 클라이언트 다운로드**를 클릭하여 **VPN 클라이언트 다운로드** 블레이드를 엽니다.
   
    ![VPN 클라이언트 다운로드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "VPN client download")
2. 클라이언트에 대한 올바른 패키지를 선택한 후 **다운로드**를 클릭합니다. 64비트 클라이언트인 경우 **AMD64**를 선택합니다. 32비트 클라이언트인 경우 **x86**을 선택합니다.
3. 클라이언트 컴퓨터에 패키지를 설치합니다. SmartScreen 팝업이 나타나면 **자세한 정보**, **실행**을 차례로 클릭하여 패키지를 설치합니다.
4. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. 나열된 연결이 표시됩니다. 연결된 가상 네트워크의 이름이 다음 예제와 비슷하게 표시됩니다. 
   
    ![VPN 클라이언트](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "VPN client")

## <a name="a-nameinstallclientcertapart-7-install-the-client-certificate"></a><a name="installclientcert"></a>7부 - 클라이언트 인증서 설치
인증을 위해 각 클라이언트 컴퓨터에 클라이언트 인증서가 있어야 합니다. 클라이언트 인증서를 설치할 때는 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

1. .pfx 파일을 클라이언트 컴퓨터에 복사합니다.
2. .pfx 파일을 두 번 클릭하여 설치합니다. 설치 위치를 수정하지 마세요.

## <a name="a-nameconnectapart-8-connect-to-azure"></a><a name="connect"></a>8부 - Azure에 연결
1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속** 을 클릭하여 상승된 권한을 사용합니다. 
2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.
   
    ![VPN 클라이언트 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. 이제 연결이 설정됩니다.
   
    ![VPN 클라이언트 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## <a name="a-nameverifyapart-9-verify-your-connection"></a><a name="verify"></a>9부 - 연결 확인
1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음과 같아야 합니다.
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="a-nameaddato-add-or-remove-trusted-root-certificates"></a><a name="add"></a>신뢰할 수 있는 루트 인증서를 추가 또는 제거하려면
Azure에서 신뢰할 수 있는 루트 인증서를 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

**지점 및 사이트 간 구성** 블레이드에서 해지된 클라이언트 인증서 목록을 관리할 수 있습니다. [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile)하는 데 사용한 블레이드입니다.

## <a name="a-namerevokeclientato-manage-the-list-of-revoked-client-certificates"></a><a name="revokeclient"></a>해지된 클라이언트 인증서 목록을 관리하려면
클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. Azure에서 루트 인증서 .cer를 제거하면, 해지된 루트 인증서에 의해 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트가 아닌 특정 클라이언트 인증서를 해지해도 이를 수행할 수 있습니다. 이런 방식은 루트 인증서에서 생성된 다른 인증서에도 여전히 유효합니다. 

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

**지점 및 사이트 간 구성** 블레이드에서 해지된 클라이언트 인증서 목록을 관리할 수 있습니다. [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile)하는 데 사용한 블레이드입니다.

## <a name="next-steps"></a>다음 단계
가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 를 참조하세요.




<!--HONumber=Nov16_HO2-->


