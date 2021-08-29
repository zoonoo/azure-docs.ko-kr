---
title: 'P2S OpenVPN 프로토콜 연결을 위한 VPN Client 구성: Azure AD 인증'
description: VPN Gateway 지점 및 사이트 간 VPN 및 Azure Active Directory 인증을 사용하여 Virtual Network에 연결하도록 Azure VPN 클라이언트를 구성하는 방법을 알아봅니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/20/2021
ms.author: cherylmc
ms.openlocfilehash: a004e8d7fd87cc7abdf881b355b40b59ac874ba1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779739"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory 인증: P2S OpenVPN 프로토콜 연결을 위한 VPN 클라이언트 구성

이 문서는 지점 및 사이트 간 VPN과 Azure Active Directory 인증을 사용하여 가상 네트워크에 연결하기 위해 Azure VPN 클라이언트를 구성하는 데 도움이 됩니다. Azure AD를 사용하여 연결 및 인증하기 위해서는 Azure AD 테넌트를 구성해야 합니다. 자세한 내용은 [Azure AD 테넌트 구성](openvpn-azure-ad-tenant.md)을 참조하세요. 지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>클라이언트 프로필 사용

VPN 클라이언트를 통해 VNet에 연결하려는 모든 컴퓨터에 대해, 컴퓨터에 대해 Azure VPN 클라이언트를 다운로드하고 VPN 클라이언트 프로필도 구성해야 합니다. 여러 컴퓨터를 구성하려는 경우 한 컴퓨터에서 클라이언트 프로필을 만들고 내보낸 다음, 다른 컴퓨터로 가져올 수 있습니다.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>인증서 기반 클라이언트 프로필 만들기

인증서 기반 프로필을 사용하는 경우 적절한 인증서가 클라이언트 컴퓨터에 설치되어 있는지 확인합니다. 인증서에 대한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

![인증서 인증의 스크린샷입니다.](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS 클라이언트 프로필 만들기

![RADIUS 인증의 스크린샷입니다.](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> P2S VPN 클라이언트 프로필에서 서버 비밀을 내보낼 수 있습니다.  클라이언트 프로필 내보내기 방법은 [여기](about-vpn-profile-download.md)에서 확인할 수 있습니다.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>클라이언트 프로필 내보내기 및 배포하기

작업 프로필을 다른 사용자에게 배포해야 하는 경우 다음 단계를 통해 내보낼 수 있습니다.

1. 내보내기 하려는 VPN 클라이언트 프로필을 강조 표시하고 **...** 메뉴에서 **내보내기** 를 선택합니다.

    ![줄임표를 선택하고 "내보내기"를 강조 표시된 "Azure VPN Client" 페이지를 보여주는 스크린샷](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 이 프로필을 저장하려는 위치를 선택한 후 파일 이름을 그대로 두고 **저장** 을 선택하여 xml 파일을 저장합니다.

    ![내보내기](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기** 를 선택합니다.

    ![“추가” 단추가 선택되고 창의 왼쪽 하단에 “가져오기” 작업이 강조 표시된 스크린샷](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기** 를 선택합니다.

    ![프로필 xml 파일이 선택된 스크린샷](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장** 을 선택합니다.

    ![“연결 이름”이 강조 표시되고 “저장” 단추가 선택된 스크린샷](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **연결** 을 선택하여 VPN에 연결합니다.

    ![VPN 및 "연결" 단추가 선택된 스크린샷](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨** 으로 표시됩니다.

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제하려는 클라이언트 프로필 옆의 줄임표 아이콘을 선택합니다. 그런 다음, **제거** 를 선택합니다.

    ![줄임표 및 "제거" 옵션이 선택된 스크린샷](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **제거** 를 선택하여 삭제합니다.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>연결 만들기

1. 페이지에서 **+** 를 선택한 다음, **+ 추가** 를 선택합니다.

    ![선택한 추가 단추를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 연결 정보를 입력합니다. 값이 확실하지 않은 경우 관리자에게 문의하세요. 값을 입력한 후 **저장** 을 선택합니다.

    ![VPN 연결 속성이 강조 표시되고 “저장” 단추가 선택된 스크린샷](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **연결** 을 선택하여 VPN에 연결합니다.

    !["편집" 단추가 선택된 스크린샷](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 적절한 인증서를 선택한 다음, **계속** 을 선택합니다.

    ![예제 자격 증명이 강조 표시되고 "계속" 단추가 선택된 스크린샷](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨** 으로 표시됩니다.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>자동으로 연결

이러한 단계를 통해 항상 자동으로 연결할 수 있도록 구성할 수 있습니다.

1. VPN 클라이언트의 홈페이지에서 **VPN 설정** 을 선택합니다.

    !["VPN 설정"이 선택된 VPN 홈페이지의 스크린샷](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 앱 전환 대화 상자에서 **예** 를 선택합니다.

    !["앱을 전환하시겠습니까?" 대화 상자가 “예”로 선택된 스크린샷](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 설정하려는 연결이 아직 연결되지 않았음을 확인한 다음, 프로필을 강조 표시하고 **자동으로 연결** 확인란에 체크합니다.

    !["자동으로 연결" 상자가 선택된 "설정" 창의 스크린샷](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **연결** 을 선택하여 VPN 연결을 시작합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단하려는 VPN 연결 옆에 있는 **...** 메뉴를 선택하여 메뉴를 표시합니다. 그런 다음, **진단** 을 선택합니다.

    ![줄임표 및 "진단이 선택된"의 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행** 을 선택합니다.

    !["진단 실행"이 선택된 "연결 속성" 페이지를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    !["회사 또는 학교 계정"이 선택된 "이제 로그인해 보겠습니다" 대화 상자를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Azure VPN 클라이언트가 Windows FIPS 모드에서 지원되나요?

예, [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) 핫픽스를 사용합니다.

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN 클라이언트에 DNS 접미사를 어떻게 추가하나요?

다운로드한 프로필 XML 파일을 수정하고 **\<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** 태그를 추가합니다.

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 DNS 서버를 어떻게 추가하나요?

다운로드한 프로필 XML 파일을 수정하고 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** 태그를 추가합니다.

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
> OpenVPN Azure AD 클라이언트는 DNS NRPT(이름 확인 정책 테이블) 항목을 사용합니다. 즉, DNS 서버는 `ipconfig /all`의 출력에 나열되지 않습니다. 사용 중인 DNS 설정을 확인하려면 PowerShell에서 [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy)를 참조하세요.
>

### <a name="can-i-configure-split-tunneling-for-the-vpn-client"></a><a name="split"></a>VPN 클라이언트에 대해 분할 터널링을 구성할 수 있나요?

분할 터널링은 VPN 클라이언트에 대해 기본적으로 구성됩니다.

### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-forced-tunneling"></a><a name="forced-tunnel"></a>모든 트래픽을 VPN 터널(강제 터널링)로 보내려면 어떻게 해야 하나요?

두 가지 방법을 사용하여 강제 터널링을 구성할 수 있습니다. 사용자 지정 경로를 보급하거나 프로필 XML 파일을 수정합니다.    

> [!NOTE]
> 인터넷 연결은 VPN 게이트웨이를 통해 제공되지 않습니다. 따라서 인터넷에 바인딩된 모든 트래픽이 삭제됩니다.
>

* **사용자 지정 경로 보급:** 0.0.0.0/1 및 128.0.0.0/1 사용자 지정 경로를 보급할 수 있습니다. 자세한 내용은 [P2S VPN 클라이언트에 대한 사용자 지정 경로 보급](vpn-gateway-p2s-advertise-custom-routes.md)을 참조하세요.

* **프로필 XML:** 다운로드한 프로필 XML 파일을 수정하고 **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** 태그를 추가합니다.


    ```
    <azvpnprofile>
    <clientconfig>
          
        <includeroutes>
            <route>
                <destination>0.0.0.0</destination><mask>1</mask>
            </route>
            <route>
                <destination>128.0.0.0</destination><mask>1</mask>
            </route>
        </includeroutes>
           
    </clientconfig>
    </azvpnprofile>
    ```


### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 경로를 어떻게 추가하나요?

다운로드한 프로필 XML 파일을 수정하고 **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** 태그를 추가합니다.

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN 클라이언트에서 경로를 차단(제외)하려면 어떻게 해야 하나요?

다운로드한 프로필 XML 파일을 수정하고 **\<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>** 태그를 추가합니다.

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

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>명령줄 프롬프트에서 프로필을 가져올 수 있나요?

다운로드한 **azurevpnconfig.xml** 파일을 **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** 폴더에 배치하고 다음 명령어를 수행하여 명령줄 프롬프트에서 프로필을 가져올 수 있습니다.

```
azurevpn -i azurevpnconfig.xml 
```
가져오기를 강제로 수행하려면 **-f** 스위치를 사용합니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure AD 인증을 사용하는 P2S Open VPN 연결에 대한 Azure Active Directory 테넌트 만들기](openvpn-azure-ad-tenant.md)를 참조하세요.