---
title: 'P2S VPN 클라이언트 구성 파일 만들기 & 설치: 인증서 인증'
titleSuffix: Azure VPN Gateway
description: P2S 인증서 인증을 위해 Windows, Linux, Linux (strongSwan) 및 macOS X VPN 클라이언트 구성 파일을 만들고 설치 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556167"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Azure 기본 인증서 인증 P2S 구성용 VPN 클라이언트 구성 파일 만들기 및 설치

VPN 클라이언트 구성 파일은 zip 파일에 포함되어 있습니다. 구성 파일은 네이티브 Azure 인증서 인증을 사용 하는 지점 및 사이트 간 연결을 통해 가상 네트워크에 연결 하기 위해 네이티브 Windows, Mac IKEv2 VPN 또는 Linux 클라이언트에 필요한 설정을 제공 합니다.

클라이언트 구성 파일은 가상 네트워크에 대 한 VPN 구성에만 적용 됩니다. VPN 프로토콜 유형 또는 인증 유형과 같은 VPN 클라이언트 구성 파일을 생성한 후에 지점 및 사이트 간 VPN 구성을 변경하면 사용자 디바이스에 대한 새 VPN 클라이언트 구성 파일을 생성해야 합니다.

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
1. 가상 네트워크 게이트웨이 페이지에서 **지점 및 사이트 간 구성을** 선택 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="VPN 클라이언트 다운로드":::
1. 지점 및 사이트 간 구성 페이지의 맨 위에서 **VPN 클라이언트 다운로드** 를 선택 합니다. 클라이언트 구성 패키지를 생성하는 데 몇 분이 소요됩니다.
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

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Azure에 연결할 모든 Mac에서 원시 IKEv2 VPN 클라이언트를 수동으로 구성해야 합니다. Azure는 원시 Azure 인증서 인증을 위해 mobileconfig 파일을 제공하지 않습니다. **일반** 에는 구성에 필요한 모든 정보가 포함됩니다. 다운로드에 Generic 폴더가 보이지 않으면 IKEv2가 터널 유형으로 선택되지 않았을 가능성이 큽니다. VPN gateway 기본 SKU는 IKEv2를 지원 하지 않습니다. IKEv2가 선택되면 zip 파일을 다시 생성하여 Generic 폴더를 검색합니다.<br>Generic 폴더에 포함되는 파일은 다음과 같습니다.

* **VpnSettings.xml** -서버 주소, 터널 유형 등의 중요 한 설정이 포함 되어 있습니다. 
* **VpnServerRoot** 는 P2S 연결을 설정 하는 동안 Azure VPN Gateway의 유효성을 검사 하는 데 필요한 루트 인증서를 포함 합니다.

다음 단계에 따라 인증서 인증을 위한 Mac VPN 기본 클라이언트를 구성합니다. Azure에 연결할 모든 Mac에서 다음 단계를 완료해야 합니다.

1. **VpnServerRoot** 루트 인증서를 Mac으로 가져옵니다. 이렇게 하려면 해당 파일을 Mac으로 복사하고 두 번 클릭하면 됩니다. 가져오기에 **추가** 를 선택 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="인증서 페이지를 보여 주는 스크린샷":::
  
    >[!NOTE]
    >인증서를 두 번 클릭해도 **추가** 대화 상자가 표시되지 않을 수 있지만 인증서는 올바른 저장소에 설치되어 있습니다. 인증서 범주 아래의 로그인 키 집합에서 인증서를 확인할 수 있습니다.
    >
  
1. P2S 설정을 구성할 때 Azure로 업로드한 루트 인증서에 의해 발행된 클라이언트 인증서가 설치되어 있는지 확인합니다. 이 인증서는 이전 단계에서 설치한 VPNServerRoot와 다른 인증서입니다. 클라이언트 인증서는 인증에 사용되므로 반드시 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
1. 네트워크 **기본 설정** 에서 **네트워크** 대화 상자를 열고 **' + '** 를 선택 하 여 AZURE 가상 네트워크에 대 한 P2S 연결에 대 한 새 VPN 클라이언트 연결 프로필을 만듭니다.

   **인터페이스** 값은 'VPN'이고, **VPN 유형** 값은 'IKEv2'입니다. **서비스 이름** 필드에 프로필 이름을 지정 하 고 **만들기** 를 선택 하 여 VPN 클라이언트 연결 프로필을 만듭니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="스크린샷 인터페이스를 선택 하 고 VPN 유형을 선택한 다음 서비스 이름을 입력 하는 옵션이 포함 된 네트워크 창이 표시 됩니다.":::
1. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 필드에 붙여넣습니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="서버 정보를 보여 주는 스크린샷":::
1. **인증 설정** 을 선택 하 고 **인증서** 를 선택 합니다. **Catalina.properties** 에 대해 **없음** 을 선택 하 고 **인증서** 를 선택 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="인증 설정을 보여 주는 스크린샷":::

   Catalina.properties에 대해 **없음** 을 선택 하 고 **인증서** 를 선택 합니다. 올바른 인증서를 **선택** 합니다.
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="선택 된 인증 설정 및 인증서에 대해 선택 하지 않은 네트워크 창이 스크린샷에 표시 됩니다.":::

1. **선택** ...을 클릭 합니다. 인증에 사용하려는 클라이언트 인증서를 선택합니다. 이 인증서는 2단계에서 설치한 인증서입니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="인증서를 선택할 수 있는 인증 설정이 포함 된 네트워크 창이 스크린샷에 표시 됩니다.":::
1. **ID 선택** 은 선택할 수 있는 인증서의 목록을 표시합니다. 적절 한 인증서를 선택한 다음 **계속** 을 선택 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="적절 한 인증서를 선택할 수 있는 Id 선택 대화 상자를 보여 주는 스크린샷":::

1. **로컬 ID** 필드에서 인증서 이름(6단계에서 사용한 이름)을 지정합니다. 이 예제에서는 `ikev2Client.com`입니다. 그런 다음 **적용** 을 선택 하 여 변경 내용을 저장 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="적용을 보여 주는 스크린샷":::
1. **네트워크** 대화 상자에서 **적용** 을 선택 하 여 모든 변경 내용을 저장 합니다. 그런 다음, **연결** 을 선택하여 Azure 가상 네트워크에 대한 P2S 연결을 시작합니다.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux(strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>strongSwan 설치

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>인증서 생성

인증서를 아직 생성 하지 않은 경우 다음 단계를 사용 합니다.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>설치 및 구성

Ubuntu 18.0.4에서 다음 지침이 생성 되었습니다. Ubuntu 16.0.10은 strongSwan GUI를 지원하지 않습니다. Ubuntu 16.0.10을 사용하려는 경우 [명령줄](#linuxinstallcli)을 사용해야 합니다. 아래 예제는 Linux 및 strongSwan 버전에 따라 표시되는 화면과 일치하지 않을 수 있습니다.

1. 이 예의 명령을 실행하여 **터미널** 을 열고 **strongSwan** 과 네트워크 관리자를 설치합니다.

   ```
   sudo apt install network-manager-strongswan
   ```
1. **설정** 을 선택한 다음 **네트워크** 를 선택 합니다. 단추를 선택 **+** 하 여 새 연결을 만듭니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="스크린샷 네트워크 연결 페이지를 표시 합니다.":::

1. 메뉴에서 **IPsec/IKEv2 (strongSwan)** 를 선택 하 고를 두 번 클릭 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="VPN 추가 페이지가 표시 됩니다.":::

1. **Vpn 추가** 페이지에서 vpn 연결의 이름을 추가 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="연결 유형 선택을 보여 주는 스크린샷":::
1. 다운로드한 클라이언트 구성 파일에 포함된 **일반** 폴더에서 **VpnSettings.xml** 파일을 엽니다. **VpnServer** 라는 태그를 찾아서 ' azuregateway '로 시작 하 고 '. cloudapp.net '로 끝나는 이름을 복사 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="데이터 복사를 보여 주는 스크린샷":::
1. **게이트웨이** 섹션에서 새 VPN 연결의 **주소** 필드에 이름을 붙여넣습니다. 다음으로, **인증서** 필드 끝에 있는 폴더 아이콘을 클릭하고 **Generic** 폴더로 이동하여 **VpnServerRoot** 파일을 선택합니다.
1. 연결의 **클라이언트** 섹션에서 **인증** 에 대해 **인증서/프라이빗 키** 를 선택합니다. **인증서** 및 **프라이빗 키** 에 대해 이전에 만든 인증서 및 프라이빗 키를 선택합니다. **옵션** 에서 **내부 IP 주소 요청** 을 선택합니다. 그런 다음 **추가** 를 선택 합니다.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="내부 IP 주소 요청을 보여 주는 스크린샷":::

1. **연결을 설정 합니다**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="복사를 보여 주는 스크린샷":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux(strongSwan CLI)

### <a name="install-strongswan"></a>strongSwan 설치

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>인증서 생성

인증서를 아직 생성 하지 않은 경우 다음 단계를 사용 합니다.

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>설치 및 구성

1. Azure Portal에서 VPNClient 패키지를 다운로드합니다.
1. 파일의 압축을 풉니다.
1. **일반** 폴더에서 **VpnServerRoot** 를 **/etc/ipsec.d/cacerts** 로 복사 하거나 이동 합니다.
1. **Cp 클라이언트. p12** 를 **/etc/ipsec.d/private/** 로 복사 하거나 이동 합니다. 이 파일은 VPN gateway에 대 한 클라이언트 인증서입니다.
1. **VpnSettings.xml** 파일을 열고 값을 복사 `<VpnServer>` 합니다. 이 값은 다음 단계에서 사용됩니다.
1. 아래 예제의 값을 조정한 다음 **/etc/ipsec.conf** 구성에 예제를 추가 합니다.
  
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
1. **/Etc/ipsec.secrets** 에 다음 값을 추가 합니다.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 다음 명령을 실행합니다.

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>다음 단계

작업 중인 원래 아티클로 돌아가서 P2S 구성을 완료 합니다.

* [PowerShell 구성 단계](vpn-gateway-howto-point-to-site-rm-ps.md).
* [구성 단계를 Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)합니다.
