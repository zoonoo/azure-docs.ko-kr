---
title: 'VPN Gateway: P2S OpenVPN 프로토콜 연결용 VPN 클라이언트: Azure AD 인증'
description: 지점 및 사이트 간 VPN 및 Azure Active Directory 인증을 사용 하 여 가상 네트워크에 연결 하도록 VPN 클라이언트를 구성 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 51004005e10416f3138f69f91b93b9cd19ed3944
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819767"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory 인증: P2S OpenVPN 프로토콜 연결에 대 한 VPN 클라이언트 구성

이 문서에서는 지점 및 사이트 간 VPN 및 Azure Active Directory 인증을 사용 하 여 가상 네트워크에 연결 하도록 VPN 클라이언트를 구성 하는 방법을 설명 합니다. Azure AD를 사용 하 여 연결 하 고 인증 하려면 먼저 Azure AD 테 넌 트를 구성 해야 합니다. 자세한 내용은 [AZURE AD 테 넌 트 구성](openvpn-azure-ad-tenant.md)을 참조 하세요.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>클라이언트 프로필 작업

연결 하려면 Azure VPN 클라이언트를 다운로드 하 고 VNet에 연결 하려는 모든 컴퓨터에서 VPN 클라이언트 프로필을 구성 해야 합니다. 컴퓨터에서 클라이언트 프로필을 만들어 내보낸 다음 추가 컴퓨터에 가져올 수 있습니다.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://go.microsoft.com/fwlink/?linkid=2117554)를 사용하여 Azure VPN Client를 다운로드합니다. Azure VPN 클라이언트에 백그라운드에서 실행할 수 있는 권한이 있는지 확인 하세요. 권한을 확인/활성화 하려면 다음 단계를 수행 합니다.

1. 시작으로 이동한 다음 설정 > 개인 정보 > 백그라운드 앱을 선택 합니다.
2. 백그라운드 앱에서 **백그라운드에서 앱 실행** 이 설정 되어 있는지 확인 합니다.
3. 백그라운드에서 실행할 수 있는 앱을 선택 하십시오 .에서 Azure VPN 클라이언트의 **설정을 켜기로**설정 합니다.

  ![사용 권한(permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>인증서 기반 클라이언트 프로필을 만들려면

인증서 기반 프로필을 사용 하는 경우 적절 한 인증서가 클라이언트 컴퓨터에 설치 되어 있는지 확인 합니다. 인증서에 대 한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조 하세요.

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS 클라이언트 프로필을 만들려면

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> P2S VPN 클라이언트 프로필에서 서버 암호를 내보낼 수 있습니다.  클라이언트 프로필을 내보내는 방법에 대 한 지침은 [여기](about-vpn-profile-download.md)에서 찾을 수 있습니다.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>클라이언트 프로필을 내보내고 배포 하려면

작업 프로필을 사용 하 고 다른 사용자에 게 배포 해야 하는 경우 다음 단계를 사용 하 여 내보낼 수 있습니다.

1. 내보내려는 VPN 클라이언트 프로필을 강조 표시 하 고 **...** 를 선택한 다음 **내보내기**를 선택 합니다.

    ![줄임표를 선택 하 고 "내보내기"가 강조 표시 된 "Azure VPN 클라이언트" 페이지를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 이 프로필을 저장 하려는 위치를 선택 하 고 파일 이름을 그대로 두고 **저장** 을 선택 하 여 xml 파일을 저장 합니다.

    ![내보내기](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기**를 선택합니다.

    !["추가" 단추를 선택 하 고 창의 왼쪽 아래에서 "가져오기" 작업을 강조 표시 하는 스크린샷](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기**를 선택합니다.

    ![선택한 프로필 x m l 파일을 보여 주는 스크린샷](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장**을 선택합니다.

    !["연결 이름"이 강조 표시 되 고 "저장" 단추가 선택 된 스크린 샷](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **연결**을 선택하여 VPN에 연결합니다.

    ![VPN 및 "연결" 단추를 선택 하 여 보여 주는 스크린샷](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**으로 표시됩니다.

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제할 클라이언트 프로필 옆에 있는 줄임표를 선택 합니다. 그런 다음, **제거**를 선택합니다.

    ![줄임표 및 "제거" 옵션을 선택 하는 스크린샷](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **제거**를 선택하여 삭제합니다.

    ![삭제](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>연결 만들기

1. 페이지에서 **+** 를 선택 하 고 **+ 추가**를 선택 합니다.

    !["추가" 단추를 선택 하 여 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 연결 정보를 입력 합니다. 값이 확실 하지 않은 경우 관리자에 게 문의 하십시오. 값을 입력 한 후 **저장**을 선택 합니다.

    ![강조 표시 된 VPN 연결 속성과 "저장" 단추가 선택 된 상태를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **연결**을 선택하여 VPN에 연결합니다.

    !["연결" 단추를 선택 하 여 보여 주는 스크린샷](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 적절 한 자격 증명을 선택한 다음 **계속**을 선택 합니다.

    ![예제 자격 증명을 강조 표시 하 고 "계속" 단추를 선택 하는 스크린샷](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 성공적으로 연결 되 면 아이콘은 녹색으로 바뀌고 **연결 된**것을 말합니다.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>자동으로 연결 하려면

이러한 단계를 통해 연결을 자동으로 on으로 연결 하도록 구성할 수 있습니다.

1. VPN 클라이언트의 홈 페이지에서 **Vpn 설정**을 선택 합니다.

    !["VPN 설정"이 선택 된 VPN 홈 페이지의 스크린샷](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 앱 전환 대화 상자에서 **예** 를 선택 합니다.

    !["앱을 전환 하 시겠습니까?"의 스크린샷 "예" 단추가 선택 된 대화 상자](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 설정 하려는 연결이 아직 연결 되지 않았는지 확인 한 다음 프로필을 강조 표시 하 고 **자동으로 연결** 확인란을 선택 합니다.

    !["자동으로 연결" 확인란이 선택 된 "설정" 창의 스크린샷](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **연결** 을 선택 하 여 VPN 연결을 시작 합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단 하려는 VPN 연결 옆 **에 있는 ...** 를 선택 하 여 메뉴를 표시 합니다. 그런 다음, **진단**을 선택합니다.

    ![줄임표 및 "진단 선택"의 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행**을 선택합니다.

    !["진단 실행"이 선택 된 "연결 속성" 페이지를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    !["회사 또는 학교 계정"을 선택 하 여 "로그인 해 보기" 대화 상자를 보여 주는 스크린샷](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN 클라이언트에 DNS 접미사를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 ** \<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes> ** 추가할 수 있습니다.

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

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** 추가할 수 있습니다.

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
> OpenVPN Azure AD 클라이언트는 DNS 서버 NRPT (이름 확인 정책 테이블) 항목을 사용 합니다. 즉, DNS 서버는의 출력에 나열 되지 않습니다 `ipconfig /all` . 사용 중인 DNS 설정을 확인 하려면 PowerShell에서 [get-dnsclientnrptpolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps&preserve-view=true) 를 참조 하세요.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 경로를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 ** \<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes> ** 추가할 수 있습니다.

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN 클라이언트에서 경로를 차단 (제외) 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 태그를 ** \<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes> ** 추가할 수 있습니다.

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
