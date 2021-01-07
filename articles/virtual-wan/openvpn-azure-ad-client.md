---
title: 'VPN Gateway: OpenVPN 프로토콜 P2S 연결에 대 한 VPN 클라이언트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용 하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8e97a2f077efd4d00eec4a91645dc1b65057ebd9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565010"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>P2S OpenVPN 프로토콜 연결을 위한 VPN 클라이언트 구성: Azure AD 인증

이 문서에서는 지점 및 사이트 간 VPN 및 Azure Active Directory 인증을 사용 하 여 가상 네트워크에 연결 하도록 VPN 클라이언트를 구성 하는 방법을 설명 합니다. Azure AD를 사용 하 여 연결 하 고 인증 하려면 먼저 Azure AD 테 넌 트를 구성 해야 합니다. 자세한 내용은 [AZURE AD 테 넌 트 구성](openvpn-azure-ad-tenant.md)을 참조 하세요.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>클라이언트 프로필 작업

연결 하려면 Azure VPN 클라이언트를 다운로드 하 고 VNet에 연결 하려는 모든 컴퓨터에서 VPN 클라이언트 프로필을 구성 해야 합니다. 컴퓨터에서 클라이언트 프로필을 만들어 내보낸 다음 추가 컴퓨터에 가져올 수 있습니다.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://go.microsoft.com/fwlink/?linkid=2117554)를 사용하여 Azure VPN Client를 다운로드합니다. Azure VPN 클라이언트에 백그라운드에서 실행할 수 있는 권한이 있는지 확인 하세요. 권한을 확인/활성화 하려면 다음 단계를 수행 합니다.

1. 시작으로 이동한 다음 설정 > 개인 정보 > 백그라운드 앱을 선택 합니다.
2. 백그라운드 앱에서 **백그라운드에서 앱 실행** 이 설정 되어 있는지 확인 합니다.
3. 백그라운드에서 실행할 수 있는 앱을 선택 하십시오 .에서 Azure VPN 클라이언트의 **설정을 켜기로** 설정 합니다.

  ![사용 권한(permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>인증서 기반 클라이언트 프로필을 만들려면

인증서 기반 프로필을 사용 하는 경우 적절 한 인증서가 클라이언트 컴퓨터에 설치 되어 있는지 확인 합니다. 인증서에 대 한 자세한 내용은 [클라이언트 인증서 설치](certificates-point-to-site.md)를 참조 하세요.

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS 클라이언트 프로필을 만들려면

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> P2S VPN 클라이언트 프로필에서 서버 암호를 내보낼 수 있습니다.  클라이언트 프로필을 내보내는 방법에 대 한 지침은 [여기](about-vpn-profile-download.md)에서 찾을 수 있습니다.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>클라이언트 프로필을 내보내고 배포 하려면

작업 프로필을 사용 하 고 다른 사용자에 게 배포 해야 하는 경우 다음 단계를 사용 하 여 내보낼 수 있습니다.

1. 내보내려는 VPN 클라이언트 프로필을 강조 표시 하 고 **...** 를 선택한 다음 **내보내기** 를 선택 합니다.

    ![메뉴에서 선택한 내보내기를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 이 프로필을 저장 하려는 위치를 선택 하 고 파일 이름을 그대로 두고 **저장** 을 선택 하 여 xml 파일을 저장 합니다.

    ![스크린샷 파일 이름을 입력할 수 있는 다른 이름으로 저장 대화 상자가 표시 됩니다.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기** 를 선택합니다.

    ![더하기 메뉴에서 선택한 가져오기가 스크린샷 화면에 표시 됩니다.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기** 를 선택합니다.

    ![스크린샷 파일을 선택할 수 있는 열기 대화 상자를 표시 합니다.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장** 을 선택합니다.

    ![추가 된 연결 이름 및 저장 단추가 선택 된 스크린샷을 보여 주는 스크린샷](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **연결** 을 선택하여 VPN에 연결합니다.

    ![스크린샷는 방금 만든 연결에 대 한의 연결 단추를 표시 합니다.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨** 으로 표시됩니다.

    ![스크린샷 연결 된 상태에서 연결을 끊을 수 있는 옵션을 보여 줍니다.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제할 클라이언트 프로필 옆에 있는 줄임표를 선택 합니다. 그런 다음, **제거** 를 선택합니다.

    ![메뉴에서 선택한 제거를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **제거** 를 선택하여 삭제합니다.

    ![스크린샷 제거 하거나 취소 하는 옵션을 포함 하는 확인 대화 상자를 표시 합니다.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>연결 만들기

1. 페이지에서 **+** 를 선택 하 고 **+ 추가** 를 선택 합니다.

    ![더하기 메뉴에서 선택한 추가를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 연결 정보를 입력 합니다. 값이 확실 하지 않은 경우 관리자에 게 문의 하십시오. 값을 입력 한 후 **저장** 을 선택 합니다.

    ![스크린 샷에서는 필요한 값을 입력할 수 있는 창을 보여 줍니다.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **연결** 을 선택하여 VPN에 연결합니다.

    ![연결에 대 한 연결 단추를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 적절 한 자격 증명을 선택한 다음 **계속** 을 선택 합니다.

    ![스크린샷 로그인 대화 상자를 표시 합니다.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 성공적으로 연결 되 면 아이콘은 녹색으로 바뀌고 **연결 된** 것을 말합니다.

    ![연결 된 상태의 연결을 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>자동으로 연결 하려면

이러한 단계를 통해 연결을 자동으로 on으로 연결 하도록 구성할 수 있습니다.

1. VPN 클라이언트의 홈 페이지에서 **Vpn 설정** 을 선택 합니다.

    ![V P n 설정을 선택할 수 있는 V P N 연결을 보여 주는 스크린샷](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 앱 전환 대화 상자에서 **예** 를 선택 합니다.

    ![스크린샷 앱 전환에 대 한 확인 메시지를 표시 합니다.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 설정 하려는 연결이 아직 연결 되지 않았는지 확인 한 다음 프로필을 강조 표시 하 고 **자동으로 연결** 확인란을 선택 합니다.

    ![스크린샷 자동으로 연결을 선택할 수 있는 설정 대화 상자를 보여 줍니다.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **연결** 을 선택 하 여 VPN 연결을 시작 합니다.

    ![스크린샷 연결 단추를 표시 합니다.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단 하려는 VPN 연결 옆 **에 있는 ...** 를 선택 하 여 메뉴를 표시 합니다. 그런 다음, **진단** 을 선택합니다.

    ![메뉴에서 선택한 진단을 보여 주는 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행** 을 선택합니다.

    ![스크린샷에는 연결에 대 한 진단 실행 단추가 표시 됩니다.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![스크린샷이 작업에 대 한 로그인 대화 상자를 표시 합니다.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![스크린샷에는 진단 결과가 표시 됩니다.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN 클라이언트에 DNS 접미사를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** 추가할 수 있습니다.

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 DNS 서버를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** 추가할 수 있습니다.

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
> OpenVPN Azure AD 클라이언트는 DNS 서버 NRPT (이름 확인 정책 테이블) 항목을 사용 합니다. 즉, DNS 서버는의 출력에 나열 되지 않습니다 `ipconfig /all` . 사용 중인 DNS 설정을 확인 하려면 PowerShell에서 [get-dnsclientnrptpolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) 를 참조 하세요.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 경로를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** 추가할 수 있습니다.

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>모든 트래픽을 VPN 터널 (강제 터널)로 직접 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** 추가할 수 있습니다.

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN 클라이언트에서 경로를 차단 (제외) 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** 추가할 수 있습니다.

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

다운로드 한 **azurevpnconfig.xml** 파일을 **%Userprofile%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** 폴더에 배치 하 고 다음 명령을 실행 하 여 명령줄 프롬프트에서 프로필을 가져올 수 있습니다.

```
azurevpn -i azurevpnconfig.xml 
```
가져오기를 강제로 수행 하려면 **-f** 스위치도 사용 합니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 인증을 사용 하는 P2S OPEN VPN 연결에 대 한 Azure Active Directory 테 넌 트 만들기](openvpn-azure-ad-tenant.md)를 참조 하세요.