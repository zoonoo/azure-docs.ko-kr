---
title: 'P2S VPN 클라이언트 구성 파일 생성 및 설치: 인증서 인증'
titleSuffix: Azure VPN Gateway
description: P2S 인증서 인증용 Windows, Linux, Linux(strongSwan) 및 macOS X VPN 클라이언트 구성 파일을 생성하고 설치하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: c732eb4808b76292fa786306aeaad9cf62358e39
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466445"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Azure 기본 인증서 인증 P2S 구성용 VPN 클라이언트 구성 파일 만들기 및 설치

VPN 클라이언트 구성 파일은 zip 파일에 포함되어 있습니다. 구성 파일은 기본 Windows, Mac IKEv2 VPN 또는 Linux 클라이언트에서 기본 Azure 인증서 인증을 사용하는 지점 및 사이트 간 연결을 통해 가상 네트워크에 연결하는 데 필요한 설정을 제공합니다.

클라이언트 구성 파일은 가상 네트워크에 대한 VPN 구성별로 다릅니다. VPN 프로토콜 유형 또는 인증 유형과 같은 VPN 클라이언트 구성 파일을 생성한 후에 지점 및 사이트 간 VPN 구성을 변경하면 사용자 디바이스에 대한 새 VPN 클라이언트 구성 파일을 생성해야 합니다.

* 지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.
* OpenVPN 지침은 [P2S용 OpenVPN 구성](vpn-gateway-howto-openvpn.md) 및 [OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md)을 참조하세요.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN 클라이언트 구성 파일 생성

시작하기 전에 연결하는 모든 사용자에 대해 유효한 인증서가 해당 사용자의 디바이스에 설치되어 있는지 확인합니다. 클라이언트 인증서 설치에 대한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

PowerShell을 사용하거나 Azure Portal을 사용하여 클라이언트 구성 파일을 생성할 수 있습니다. 두 메서드 모두 동일한 zip 파일을 반환합니다. 파일의 압축을 풀어 다음 폴더를 확인합니다.

* **WindowsAmd64** 및 **WindowsX86** - 각각 Windows 64비트 및 32비트 설치 관리자 패키지가 포함되어 있습니다. **WindowsAmd64** 설치 관리자 패키지는 Amd 외에도 지원되는 모든 64비트 Windows 클라이언트에서 사용됩니다.
* **Generic** - 사용자 고유의 VPN 클라이언트 구성을 만드는 데 사용되는 일반 정보가 포함되어 있습니다. Generic 폴더는 게이트웨이에 IKEv2 또는 SSTP + IKEv2를 구성한 경우에 제공됩니다. SSTP만 구성되면 Generic 폴더가 없습니다.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Azure Portal을 사용하여 파일 생성

1. Azure Portal에서 연결하려는 가상 네트워크의 가상 네트워크 게이트웨이로 이동합니다.
1. 가상 네트워크 게이트웨이 페이지에서 **지점 및 사이트 간 구성** 을 선택합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="VPN 클라이언트 다운로드":::
1. 지점 및 사이트 간 구성 페이지의 맨 위에서 **VPN 클라이언트 다운로드** 를 선택합니다. 클라이언트 구성 패키지를 생성하는 데 몇 분이 소요됩니다.
1. 클라이언트 구성 zip 파일을 사용할 수 있으면 브라우저에 표시됩니다. 게이트웨이와 동일한 이름이 지정됩니다. 파일의 압축을 풀어 폴더를 확인합니다.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>PowerShell을 사용하여 파일 생성

1. VPN 클라이언트 구성 파일을 생성하는 경우 '-AuthenticationMethod' 값은 'EapTls'입니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성합니다.

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. 브라우저에 URL을 복사하여 zip 파일을 다운로드한 다음 파일의 압축을 풀어 폴더를 봅니다.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac(OS X)

 Azure에 연결할 모든 Mac에서 원시 IKEv2 VPN 클라이언트를 수동으로 구성해야 합니다. Azure는 원시 Azure 인증서 인증을 위해 mobileconfig 파일을 제공하지 않습니다. **일반** 폴더는 구성에 필요한 모든 정보를 포함합니다. 다운로드에 Generic 폴더가 보이지 않으면 IKEv2가 터널 유형으로 선택되지 않았을 가능성이 큽니다. VPN Gateway 기본 SKU는 IKEv2를 지원하지 않습니다. IKEv2가 선택되면 zip 파일을 다시 생성하여 Generic 폴더를 검색합니다.<br>Generic 폴더에 포함되는 파일은 다음과 같습니다.

* **VpnSettings.xml** - 서버 주소 및 터널 종류와 같은 중요한 설정이 포함되어 있습니다. 
* **VpnServerRoot.cer** - P2S 연결 설정에서 Azure VPN Gateway의 유효성을 검사하는 데 필요한 루트 인증서가 포함되어 있습니다.

다음 단계에 따라 인증서 인증을 위한 Mac VPN 기본 클라이언트를 구성합니다. Azure에 연결할 모든 Mac에서 다음 단계를 완료해야 합니다.

1. **VpnServerRoot** 루트 인증서를 Mac으로 가져옵니다. 이렇게 하려면 해당 파일을 Mac으로 복사하고 두 번 클릭하면 됩니다. **추가** 를 선택하여 가져옵니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="스크린샷은 인증서 페이지를 보여줍니다.":::
  
    >[!NOTE]
    >인증서를 두 번 클릭해도 **추가** 대화 상자가 표시되지 않을 수 있지만 인증서는 올바른 저장소에 설치되어 있습니다. 인증서 범주 아래의 로그인 키 집합에서 인증서를 확인할 수 있습니다.
    >
  
1. P2S 설정을 구성할 때 Azure로 업로드한 루트 인증서에 의해 발행된 클라이언트 인증서가 설치되어 있는지 확인합니다. 이 인증서는 이전 단계에서 설치한 VPNServerRoot와 다른 인증서입니다. 클라이언트 인증서는 인증에 사용되므로 반드시 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
1. **네트워크** 대화 상자를 **네트워크 기본 설정에서** 열고 Azure 가상 네트워크에 대한 P2S 연결용 새 VPN 클라이언트 연결 프로필을 생성하기 위해 **'+'** 를 선택합니다.

   **인터페이스** 값은 'VPN'이고, **VPN 유형** 값은 'IKEv2'입니다. **서비스 이름** 필드에서 프로필의 새 이름을 지정한 후 **생성** 을 선택하여 VPN 클라이언트 연결 프로필을 생성합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="스크린샷은 인터페이스 선택, VPN 유형 선택, 서비스 이름 입력 옵션이 있는 네트워크 창을 보여줍니다.":::
1. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 필드에 붙여넣습니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="스크린샷은 서버 정보를 보여줍니다.":::
1. **인증 설정** 을 선택하고 **인증서** 를 선택합니다. **Catalina** 의 경우, **없음** 을 선택한 후 **인증서** 를 선택합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="스크린샷은 인증 설정을 보여줍니다.":::

   Catalina의 경우, **없음** 을 선택한 후 **인증서** 를 선택합니다. 올바른 인증서를 **선택** 합니다.
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="스크린샷은 인증 설정이 없음으로 선택되고 인증서가 선택된 네트워크 창을 보여줍니다.":::

1. **선택...** 을 클릭합니다. 인증에 사용하려는 클라이언트 인증서를 선택합니다. 이 인증서는 2단계에서 설치한 인증서입니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="스크린샷은 인증 설정이 있는 네트워크 창을 보여주며, 여기에서 인증서를 선택할 수 있습니다.":::
1. **ID 선택** 은 선택할 수 있는 인증서의 목록을 표시합니다. 적절한 인증서를 선택한 후 **계속** 을 선택합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="스크린샷은 적절한 인증서를 선택할 수 있는 ID 선택 대화 상자를 보여줍니다.":::

1. **로컬 ID** 필드에서 인증서 이름(6단계에서 사용한 이름)을 지정합니다. 이 예제에서는 `ikev2Client.com`입니다. 그 후 **적용** 을 선택하여 변경 내용을 저장합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="스크린샷은 적용을 보여줍니다.":::
1. **네트워크** 대화 상자에서 **적용** 을 선택하여 변경 내용을 모두 저장합니다. 그런 다음, **연결** 을 선택하여 Azure 가상 네트워크에 대한 P2S 연결을 시작합니다.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux(strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>strongSwan 설치

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>인증서 생성

인증서를 아직 생성하지 않은 경우 다음 단계를 따릅니다.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>설치 및 구성

다음 지침은 Ubuntu 18.0.4. 기준으로 작성되었습니다. Ubuntu 16.0.10은 strongSwan GUI를 지원하지 않습니다. Ubuntu 16.0.10을 사용하려는 경우 [명령줄](#linuxinstallcli)을 사용해야 합니다. 아래 예제는 Linux 및 strongSwan 버전에 따라 표시되는 화면과 일치하지 않을 수 있습니다.

1. 이 예의 명령을 실행하여 **터미널** 을 열고 **strongSwan** 과 네트워크 관리자를 설치합니다.

   ```
   sudo apt install network-manager-strongswan
   ```
1. **설정** 을 선택한 후 **네트워크** 를 선택합니다. **+** 버튼을 선택해 새로운 연결을 만듭니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="스크린샷은 네트워크 연결 페이지를 보여줍니다.":::

1. 메뉴에서 **IPsec/IKEv2(strongSwan)를** 선택하고 두 번 클릭합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="스크린샷은 VPN 추가 페이지를 보여줍니다.":::

1. **VPN 추가** 페이지에서 VPN 연결의 이름을 추가합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="스크린샷은 연결 형식 선택을 보여줍니다.":::
1. 다운로드한 클라이언트 구성 파일에 포함된 **일반** 폴더에서 **VpnSettings.xml** 파일을 엽니다. **VpnServer** 라는 태그를 찾아 'azuregateway'로 시작하고 '.cloudapp.net'으로 끝나는 이름을 복사합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="스크린샷은 데이터 복사를 보여줍니다.":::
1. **게이트웨이** 섹션에서 새 VPN 연결의 **주소** 필드에 이름을 붙여넣습니다. 다음으로, **인증서** 필드 끝에 있는 폴더 아이콘을 클릭하고 **Generic** 폴더로 이동하여 **VpnServerRoot** 파일을 선택합니다.
1. 연결의 **클라이언트** 섹션에서 **인증** 에 대해 **인증서/프라이빗 키** 를 선택합니다. **인증서** 및 **프라이빗 키** 에 대해 이전에 만든 인증서 및 프라이빗 키를 선택합니다. **옵션** 에서 **내부 IP 주소 요청** 을 선택합니다. 그런 후 **추가** 를 선택합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="스크린샷은 내부 IP 주소 요청을 보여줍니다.":::

1. 연결을 **On** 으로 전환합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="스크린샷은 복사를 보여줍니다.":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux(strongSwan CLI)

### <a name="install-strongswan"></a>strongSwan 설치

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>인증서 생성

인증서를 아직 생성하지 않은 경우 다음 단계를 따릅니다.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>설치 및 구성

1. Azure Portal에서 VPNClient 패키지를 다운로드합니다.
1. 파일의 압축을 풉니다.
1. **일반** 폴더에서 **VpnServerRoot.cer** 를 **/etc/ipsec.d/cacerts** 로 복사하거나 이동시킵니다.
1. **cp client.p12** 를 **/etc/ipsec.d/private/** 로 복사하거나 이동시킵니다. 이 파일은 VPN Gateway에 대한 클라이언트 인증서입니다.
1. **VpnSettings.xml** 파일을 열고 `<VpnServer>` 값을 복사합니다. 이 값은 다음 단계에서 사용됩니다.
1. 아래 예제에서 값을 조정한 다음 예제를 **/etc/ipsec.conf** 구성에 추가합니다.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. 다음 값을 **/etc/ipsec.secrets** 에 추가합니다.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 다음 명령을 실행합니다.

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>다음 단계

작업 중인 원래 아티클로 돌아가서 P2S 구성을 완료합니다.

* [PowerShell 구성 단계](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal 구성 단계](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
