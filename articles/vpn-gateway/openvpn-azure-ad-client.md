---
title: 'VPN 게이트웨이: OpenVPN 프로토콜 P2S 연결을 위한 VPN 클라이언트: Azure AD 인증'
description: Azure AD 인증을 사용하여 P2S VPN을 사용하여 VNet에 연결할 수 있습니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 7bc28a03476e773325d14808e1c7ac99103b2d5d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879448"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>P2S OpenVPN 프로토콜 연결을 위한 VPN 클라이언트 구성: Azure AD 인증

이 문서에서는 POINT-To-Site VPN 및 Azure Active Directory 인증을 사용하여 가상 네트워크에 연결하도록 VPN 클라이언트를 구성하는 데 도움이 됩니다. Azure AD를 사용하여 연결하고 인증하려면 먼저 Azure AD 테넌트를 구성해야 합니다. 자세한 내용은 [Azure AD 테넌트 구성을](openvpn-azure-ad-tenant.md)참조하십시오.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>클라이언트 프로필 작업

연결하려면 Azure VPN 클라이언트를 다운로드하고 VNet에 연결하려는 모든 컴퓨터에서 VPN 클라이언트 프로필을 구성해야 합니다. 컴퓨터에서 클라이언트 프로필을 만든 다음 내보낸 다음 추가 컴퓨터로 가져올 수 있습니다.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://go.microsoft.com/fwlink/?linkid=2117554)를 사용하여 Azure VPN Client를 다운로드합니다. Azure VPN 클라이언트에 백그라운드에서 실행할 수 있는 권한이 있는지 확인하십시오. 권한을 확인/활성화하려면 아래 단계를 따르십시오.

1. 시작으로 이동한 다음 개인 정보 > > 백그라운드 앱 설정을 선택합니다.
2. 백그라운드 앱에서 **백그라운드에서 앱을 실행하도록** 설정했는지 확인합니다.
3. 백그라운드에서 실행할 수 있는 앱을 선택에서 Azure VPN 클라이언트에 대한 설정을 **켜십시오.**

  ![사용 권한(permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>인증서 기반 클라이언트 프로필을 만들려면

인증서 기반 프로필로 작업할 때는 클라이언트 컴퓨터에 적절한 인증서가 설치되어 있는지 확인합니다. 인증서에 대한 자세한 내용은 [클라이언트 인증서 설치를](point-to-site-how-to-vpn-client-install-azure-cert.md)참조하십시오.

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS 클라이언트 프로필을 만들려면

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> 서버 보안 은 P2S VPN 클라이언트 프로필에서 내보낼 수 있습니다.  클라이언트 프로필을 내보내는 방법에 대한 지침은 [여기에서](about-vpn-profile-download.md)찾을 수 있습니다.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>클라이언트 프로필을 내보내고 배포하려면

작업 프로필을 가지고 있고 다른 사용자에게 배포해야 하면 다음 단계를 사용하여 내보낼 수 있습니다.

1. 내보낼 VPN 클라이언트 프로필을 강조 표시하고 **...** 을 선택한 다음 **내보내기를**선택합니다.

    ![내보내기](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 이 프로파일을 저장할 위치를 선택하고 파일 이름을 있는 그대로 두은 다음 **저장을** 선택하여 xml 파일을 저장합니다.

    ![내보내기](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기**를 선택합니다.

    ![수입](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기**를 선택합니다.

    ![수입](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장**을 선택합니다.

    ![수입](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **연결**을 선택하여 VPN에 연결합니다.

    ![수입](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**으로 표시됩니다.

    ![수입](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제할 클라이언트 프로필 옆에 있는 타원을 선택합니다. 그런 다음, **제거**를 선택합니다.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **제거**를 선택하여 삭제합니다.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>연결 만들기

1. 페이지에서 을 선택한 **+** 다음 **+ 추가**를 선택합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 연결 정보를 입력합니다. 값을 잘 모르는 경우 관리자에게 문의하십시오. 값을 입력한 후 **저장을**선택합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **연결**을 선택하여 VPN에 연결합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 적절한 자격 증명을 선택한 다음 **계속을**선택합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 성공적으로 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**.

    ![연결](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>자동으로 연결하려면

이러한 단계를 통해 항상 켜기로 자동으로 연결하도록 연결을 구성하는 데 도움이 됩니다.

1. VPN 클라이언트의 홈 페이지에서 **VPN 설정을**선택합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 스위치 앱 대화 상자에서 **예를** 선택합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 설정하려는 연결이 아직 연결되어 있지 않은지 확인한 다음 프로파일을 강조 표시하고 **자동으로 연결** 확인란을 선택합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. VPN 연결을 시작하려면 **연결을** 선택합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단할 VPN 연결 옆에서 메뉴를 표시하기 위해 **선택합니다.** 그런 다음, **진단**을 선택합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행**을 선택합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN 클라이언트에 DNS 접미사를 추가하려면 어떻게 해야 합니까?

다운로드 한 프로필 XML 파일을 수정하고 ** \<dnssufix \<> /dnssufix>\< \</dnssuffixes>태그에>dnssuffixes를** 추가 할 수 있습니다.

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 DNS 서버를 추가하려면 어떻게 해야 합니까?

다운로드 한 프로필 XML 파일을 수정하고 ** \<dnsserver \<> / \<dnsserver \<>/ dnsserver>태그를>dnsserver를** 추가 할 수 있습니다.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD 클라이언트는 DNS 이름 확인 정책 테이블(NRPT) 항목을 사용합니다. `ipconfig /all` 사용 중인 DNS 설정을 확인하려면 PowerShell의 [Get-DnsClientNrptPolicy를](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) 참조하십시오.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 경로를 추가하려면 어떻게 해야 합니까?

다운로드 한 프로필 XML 파일을 수정하고 ** \<포함 \<경로를 \< \< \< \< \< \<** 추가 할 수 있습니다>경로>대상>마스크> / 대상>/ 대상>/ 경로>/>태그 포함

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN 클라이언트에서 경로를 차단(제외)하려면 어떻게 해야 합니까?

다운로드한 프로필 XML 파일을 수정하고 ** \< \< \< \<대상>대상>마스크> \< \< \< \</대상>/마스크>/경로>/excluderoutes>** 태그에 제외 경로>추가 할 수 있습니다.

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>명령줄 프롬프트에서 프로파일을 가져올 수 있습니까?

다운로드한 **azurevpnconfig.xml** 파일을 **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** 폴더에 배치하고 다음 명령을 실행하여 명령줄 프롬프트에서 프로필을 가져올 수 있습니다.

```
azurevpn -i azurevpnconfig.xml 
```
가져오기를 강제로 사용 하 여 **-f** 스위치도


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 인증을 사용하는 P2S Open VPN 연결에 대한 Azure Active Directory 테넌트 만들기를](openvpn-azure-ad-tenant.md)참조하십시오.
