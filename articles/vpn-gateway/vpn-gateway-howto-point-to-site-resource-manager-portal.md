---
title: "지점 및 사이트 간 및 네이티브 Azure 인증서 인증을 사용하여 Azure Virtual Network에 컴퓨터 연결: Azure Portal| Microsoft Docs"
description: "인증서 인증을 사용하여 지점 및 사이트 간 VPN 게이트웨이 연결을 만들어 Azure Virtual Network에 안전하게 컴퓨터를 연결합니다. 이 문서는 Resource Manager 배포 모델에 적용되며 Azure Portal을 사용합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 35c9da37a3e21a0b98928f6c1a7c6df1630e69c1
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2017
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>네이티브 Azure 인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결 구성: Azure portal

이 문서에서는 리소스 관리자 배포 모델에서 Azure Portal을 사용하여 지점 및 사이트 간 연결로 VNet을 만드는 방법을 보여줍니다. 이 구성에서는 인증에 인증서를 사용합니다. 이 구성에서 Azure VPN Gateway는 RADIUS 서버 대신 인증서의 유효성 검사를 수행합니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [쉬운 테이블](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

지점 및 사이트 간(P2S) VPN Gateway를 통해 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신하는 경우와 같이 원격 위치에서 VNet에 연결하려는 경우에 유용합니다. VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우에 사이트 간 VPN 대신 P2S VPN을 사용하는 것도 유용한 솔루션입니다. P2S VPN 연결은 Windows 및 Mac 장치에서 시작됩니다.

다음 인증 방법은 클라이언트를 연결하는 데 사용할 수 있습니다.

* RADIUS 서버 - 현재 미리 보기 상태
* VPN Gateway 네이티브 Azure 인증서 인증

이 문서에서는 네이티브 Azure 인증서 인증을 사용하는 인증으로 P2S 구성을 구성하도록 합니다. RADIUS를 사용하여 사용자를 연결하도록 인증하려는 경우 [RADIUS 인증을 사용하는 P2S](point-to-site-how-to-radius-ps.md)를 참조하세요.

![Azure VNet-지점 및 사이트 간 연결 다이어그램에 컴퓨터 연결](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeps.png)

P2S 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. P2S는 SSTP(Secure Socket Tunneling Protocol) 또는 IKEv2를 통한 VPN 연결을 만듭니다.

* SSTP는 Windows 클라이언트 플랫폼에서만 지원되는 SSL 기반 VPN 터널입니다. 이를 통해 방화벽을 통과할 수 있으므로 어디서나 Azure에 연결할 수 있는 이상적인 옵션입니다. 서버 쪽에서 SSTP 버전 1.0, 1.1 및 1.2를 지원하며, 클라이언트에서 사용할 버전을 결정합니다. Windows 8.1 이상에서는 기본적으로 SSTP 버전 1.2를 사용합니다.

* IKEv2 VPN - 표준 기반 IPsec VPN 솔루션입니다. IKEv2 VPN은 Mac 장치(OSX 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다. IKEv2는 현재 미리 보기로 제공되고 있습니다.

지점 및 사이트 간 네이티브 Azure 인증서 인증 연결을 사용하려면 다음 항목이 필요합니다.

* RouteBased VPN 게이트웨이입니다.
* Azure에 업로드된 루트 인증서에 대한 공개 키(.cer 파일)입니다. 인증서가 업로드되면 신뢰할 수 있는 인증서로 간주되며 인증에 사용됩니다.
* 루트 인증서에서 생성되고 VNet에 연결할 각 클라이언트 컴퓨터에 설치된 클라이언트 인증서. 클라이언트 인증에 사용됩니다.
* VPN 클라이언트 구성 VPN 클라이언트 구성 파일에는 클라이언트를 VNet에 연결하는 데 필요한 정보가 포함됩니다. 파일은 운영 체제에 기본적으로 제공된 기존의 VPN 클라이언트를 구성합니다. 연결되는 각 클라이언트는 구성 파일에서 설정을 사용하여 구성해야 합니다.

지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 연결 정보](point-to-site-about.md)를 참조하세요.

#### <a name="example"></a>예제 값

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

* **VNet 이름:** VNet1
* **주소 공간:** 192.168.0.0/16<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름:** FrontEnd
* **서브넷 주소 범위:** 192.168.1.0/24
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹:** TestRG
* **위치:** 미국 동부
* **GatewaySubnet:** 192.168.200.0/24<br>
* **DNS 서버:**(선택 사항) 이름 확인에 사용할 DNS 서버의 IP 주소.
* **가상 네트워크 게이트웨이 이름:** VNet1GW
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **공용 IP 주소 이름:** VNet1GWpip
* **연결 형식:** 지점 및 사이트 간
* **클라이언트 주소 풀:** 172.16.201.0/24<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 클라이언트 주소 풀에서 IP 주소를 받습니다.

## <a name="createvnet"></a>1. 가상 네트워크 만들기

시작하기 전에 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. 게이트웨이 서브넷 추가

가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서비스는 게이트웨이 서브넷에 지정된 IP 주소를 사용합니다. 향후 추가적인 구성 요구 사항을 수용하기에 충분한 IP 주소를 제공하도록 가능하면 /28 또는 /27 CIDR 블록을 사용하여 게이트웨이 서브넷을 만듭니다.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. DNS 서버 지정(선택 사항)

가상 네트워크를 만든 후에 DNS 서버의 IP 주소를 추가하여 이름 확인을 처리할 수 있습니다. DNS 서버는 이 구성에 대해 선택 사항이지만 이름 확인이 필요한 경우 필수 항목입니다. 값을 지정하더라도 새 DNS 서버를 만들지 않습니다. 지정한 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버 IP 주소여야 합니다. 이 예에서는 개인 IP 주소를 사용하지만 DNS 서버의 IP 주소가 아닐 가능성이 높습니다. 고유한 값을 사용해야 합니다. 지정한 값은 P2S 연결 또는 VPN 클라이언트가 아니라 VNet에 배포한 리소스에서 사용됩니다.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. 가상 네트워크 게이트웨이 만들기

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

## <a name="generatecert"></a>5. 인증서 생성

지점 및 사이트 간 VPN 연결을 통해 VNet에 연결되는 클라이언트를 인증하기 위해 Azure에 의해 인증서가 사용됩니다. 루트 인증서를 얻었으면 Azure에 공개 키 정보를 [업로드](#uploadfile)합니다. 그러면 루트 인증서는 P2S를 통한 가상 네트워크 연결을 위해 Azure에서 '신뢰할 수 있는' 것으로 간주됩니다. 또한 신뢰할 수 있는 루트 인증서에서 클라이언트 인증서를 생성한 후 각 클라이언트 컴퓨터에 인증서를 설치합니다. 클라이언트 인증서는 VNet에 대한 연결을 시작할 때 해당 클라이언트를 인증하는 데 사용됩니다. 

### <a name="getcer"></a>1. 루트 인증서용 .cer 파일 가져오기

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. 클라이언트 인증서 생성

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. 클라이언트 주소 풀 추가

클라이언트 주소 풀은 사용자가 지정한 개인 IP 주소 범위입니다. 지점 및 사이트 간 VPN을 통해 연결하는 클라이언트는 이 범위의 IP 주소를 받습니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다.

1. 가상 네트워크 게이트웨이가 생성된 후에는 가상 네트워크 게이트웨이 페이지의 **설정** 섹션으로 이동합니다. **설정** 섹션에서 **지점 및 사이트 간 구성**을 클릭하여 **지점 및 사이트 간 구성** 페이지를 엽니다.

  ![지점 및 사이트 간 페이지](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png)
2. **지점 및 사이트 간 구성** 페이지에서 자동으로 채워진 범위를 삭제한 다음 사용하려는 개인 IP 주소 범위를 추가할 수 있습니다. **저장**을 클릭하여 설정을 확인하고 저장합니다.

  ![클라이언트 주소 풀](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>7. 루트 인증서 공용 인증서 데이터 업로드

게이트웨이를 만든 후에 루트 인증서의 공개 키 정보를 Azure로 업로드합니다. 공용 인증서 데이터가 업로드되면 Azure는 이 데이터를 사용하여 신뢰할 수 있는 루트 인증서에서 생성된 클라이언트 인증서를 설치한 클라이언트를 인증합니다. 신뢰할 수 있는 루트 인증서를 최대 20개까지 추가로 업로드할 수 있습니다.

1. **루트 인증서** 섹션의 **지점 및 사이트 간 구성** 페이지에 인증서가 추가됩니다.  
2. 루트 인증서를 Base-64 인코딩된 X.509(.cer) 파일로 내보내야 합니다. 이 형식으로 내보내야 텍스트 편집기에서 인증서를 열 수 있습니다.
3. 메모장과 같은 텍스트 편집기에서 인증서를 엽니다. 인증서 데이터를 복사하는 경우 캐리지 리턴 또는 줄 바꿈 없이 하나의 연속 줄로 텍스트를 복사합니다. 캐리지 리턴 및 줄 바꿈을 보려면 '기호 표시/모든 문자 표시'에 대한 텍스트 편집기의 보기를 수정해야 할 수도 있습니다. 하나의 연속 줄로만 다음 섹션을 복사합니다.

  ![인증서 데이터](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. 인증서 데이터를 **공용 인증서 데이터** 필드에 붙여 넣습니다. 인증서의 **이름을 지정**한 다음 **저장**을 클릭합니다. 최대 20개의 신뢰할 수 있는 루트 인증서를 추가할 수 있습니다.

  ![인증서 업로드](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="installclientcert"></a>8. 내보낸 클라이언트 인증서 설치

클라이언트 인증서를 생성하는 데 사용한 것 외의 클라이언트 컴퓨터에서 P2S 연결을 만들려는 경우 클라이언트 인증서를 설치해야 합니다. 클라이언트 인증서를 설치하는 경우 클라이언트 인증서를 내보낼 때 만든 암호가 필요합니다.

전체 인증서 체인(즉, 기본값)과 함께.pfx로 클라이언트 인증서를 내보냈는지 확인합니다. 그렇지 않은 경우, 루트 인증서 정보가 클라이언트 컴퓨터에 존재하지 않으며 클라이언트를 제대로 인증할 수 없습니다. 

설치 단계는 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="clientconfig"></a>9. VPN 클라이언트 구성 패키지 생성 및 설치

VPN 클라이언트 구성 파일에는 P2S 연결을 통해 VNet에 연결하도록 장치를 구성하는 설정이 포함되어 있습니다. VPN 클라이언트 구성 파일을 생성하고 설치하는 지침은 [네이티브 Azure 인증서 인증 P2S 구성에 VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)를 참조하세요.

## <a name="connect"></a>10. Azure에 연결

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN 클라이언트에서 연결

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. **계속**을 클릭하여 상승된 권한을 사용합니다.

2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

  ![VPN 클라이언트에서 Azure에 연결](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. 연결이 설정되었습니다.

  ![설정된 연결](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Windows P2S 연결 문제 해결

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Mac VPN 클라이언트에서 연결하려면

네트워크 대화 상자에서 사용하려는 클라이언트 프로필을 찾고 **연결**을 클릭합니다.

  ![Mac 연결](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>연결을 확인하려면

이러한 지침은 Windows 클라이언트에 적용됩니다.

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.
2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음 예제와 비슷합니다.

  ```
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
  ```

## <a name="connectVM"></a>가상 컴퓨터에 연결하려면

이러한 지침은 Windows 클라이언트에 적용됩니다.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>신뢰할 수 있는 루트 인증서를 추가 또는 제거하려면

Azure에서 신뢰할 수 있는 루트 인증서를 추가 및 제거할 수 있습니다. 루트 인증서를 제거하면 해당 루트에서 생성된 인증서가 있는 클라이언트를 인증하지 못하게 됩니다. 따라서 연결할 수도 없습니다. 클라이언트를 인증하고 연결하려는 경우 Azure에 (업로드된)신뢰할 수 있는 루트 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

### <a name="to-add-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 추가하려면

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 자세한 내용은 이 문서에서 [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile) 섹션을 참조하세요.

### <a name="to-remove-a-trusted-root-certificate"></a>신뢰할 수 있는 루트 인증서를 제거하려면

1. 신뢰할 수 있는 루트 인증서를 제거하려면 가상 네트워크 게이트웨이에 대한 **지점 및 사이트 간 구성** 페이지로 이동합니다.
2. 페이지의 **루트 인증서** 섹션에서 제거할 인증서를 찾습니다.
3. 인증서 옆의 줄임표를 클릭한 다음 '제거'를 클릭합니다.

## <a name="revokeclient"></a>클라이언트 인증서를 해지하려면

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. 이것은 신뢰할 수 있는 루트 인증서를 제거하는 것과 다릅니다. Azure에서 신뢰할 수 있는 루트 인증서 .cer를 제거하면, 해지된 루트 인증서로 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트 인증서가 아닌 클라이언트 인증서를 해지하면 루트 인증서에서 생성된 다른 인증서를 인증에 계속 사용할 수 있습니다.

해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### <a name="revoke-a-client-certificate"></a>클라이언트 인증서 해지

해지 목록에 지문을 추가하여 클라이언트 인증서를 해지할 수 있습니다.

1. 클라이언트 인증서 지문을 검색합니다. 자세한 내용은 [인증서의 지문을 검색하는 방법](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
2. 텍스트 편집기에 정보를 복사하고 연속 문자열이 되도록 공백을 모두 제거합니다.
3. 가상 네트워크 게이트웨이 **지점 및 사이트 간 구성** 페이지로 이동합니다. [신뢰할 수 있는 루트 인증서를 업로드](#uploadfile)하는 데 사용한 것과 동일한 페이지입니다.
4. **해지된 인증서** 섹션에서 인증서에 대한 이름(인증서 CN이 아니어도 됨)을 입력합니다.
5. 지문 문자열을 **지문** 필드에 복사하여 붙여 넣습니다.
6. 지문의 유효성이 검사되고 해당 지문이 해지 목록에 자동으로 추가됩니다. 목록이 업데이트되고 있음을 알리는 메시지가 화면에 표시됩니다. 
7. 업데이트가 완료된 후에는 인증서를 더 이상 연결에 사용할 수 없습니다. 이 인증서를 사용하여 연결하려는 클라이언트에서 인증서가 더 이상 유효하지 않다고 하는 메시지를 받습니다.

## <a name="faq"></a>지점 및 사이트 간 FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요. 네트워킹 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/linux/azure-vm-network-overview.md)를 참조하세요.
