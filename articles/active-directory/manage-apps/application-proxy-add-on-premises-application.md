---
title: 온-프레미스 앱 추가 - Azure Active Directory의 애플리케이션 프록시 | Microsoft Docs
description: Azure AD(Azure Active Directory)에는 사용자가 해당 Azure AD 계정으로 로그인하여 온-프레미스 애플리케이션에 액세스할 수 있는 애플리케이션 프록시 서비스가 포함됩니다. 이 자습서에서는 애플리케이션 프록시를 사용할 환경을 준비한 다음, Azure Portal을 사용하여 Azure AD 테넌트에 온-프레미스 애플리케이션을 추가하는 방법을 보여줍니다.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 444fb5576ed6886e5919202cf7f22ef14e1255b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321412"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가

Azure AD(Azure Active Directory)에는 사용자가 해당 Azure AD 계정으로 로그인하여 온-프레미스 애플리케이션에 액세스할 수 있는 애플리케이션 프록시 서비스가 포함됩니다. 이 자습서에서는 애플리케이션 프록시에서 사용할 환경을 준비합니다. 환경이 준비되면 Azure Portal을 사용하여 Azure AD 테넌트에 온-프레미스 애플리케이션을 추가합니다.

이 자습서는 다음을 수행합니다.

> [!div class="checklist"]
> * 아웃바운드 트래픽에 대한 포트 열기 및 특정 URL에 대한 액세스 허용
> * Windows Server에서 커넥터 설치 및 애플리케이션 프록시에서 등록
> * 커넥터가 설치되고 올바르게 등록되었는지 확인
> * Azure AD 테넌트에 온-프레미스 애플리케이션 추가
> * 테스트 사용자가 Azure AD 계정을 사용하여 애플리케이션에 로그온할 수 있는지 확인

## <a name="before-you-begin"></a>시작하기 전에

테넌트에 애플리케이션을 추가하려면 다음이 필요합니다.

* [Microsoft Azure AD 기본 또는 Premium 구독](https://azure.microsoft.com/pricing/details/active-directory). 
* 애플리케이션 관리자 계정

### <a name="windows-server"></a>Windows Server
추가 중인 애플리케이션이 온-프레미스이므로 애플리케이션 프록시 커넥터를 설치할 수 있는 Windows Server 2012 R2 이상을 실행하는 Windows Server가 필요합니다. 이 커넥터 서버는 Azure의 애플리케이션 프록시 서비스 및 게시할 예정인 온-프레미스 애플리케이션에 연결해야 합니다.

프로덕션 환경의 고가용성을 위해 둘 이상의 Windows Server를 사용하는 것이 좋습니다.  이 자습서에서는 하나의 Windows Server로 충분합니다.

**커넥터 서버에 대한 권장 사항**

1. 실제로 애플리케이션 서버에 가깝게 커넥터 서버를 배치하여 커넥터와 애플리케이션 간 성능을 최적화합니다. 자세한 내용은 [네트워크 토폴로지 고려 사항](application-proxy-network-topology.md)을 참조하세요.

2. 커넥터 서버와 웹 애플리케이션 서버는 동일한 Active Directory 도메인에 속해야 합니다. 동일한 도메인에 서버를 배치하는 것은 IWA(Windows 통합 인증) 및 KCD(Kerberos 제한된 위임)에서 SSO(Single Sign-On)을 사용하기 위한 요구 사항입니다. 커넥터 서버 및 웹 애플리케이션 서버가 다른 Active Directory 도메인에 위치한 경우 Single Sign-On에 대해 리소스 기반 위임을 사용해야 합니다. 자세한 내용은 [애플리케이션 프록시를 사용하는 Single Sign-On용 KCD](application-proxy-configure-single-sign-on-with-kcd.md)를 참조하세요.

**소프트웨어 요구 사항**

애플리케이션 프록시 커넥터를 설치한 후에 Windows 커넥터 서버가 TLS 1.2를 사용하도록 설정해야 합니다. 버전 1.5.612.0 이하의 기존 커넥터는 추가 공지가 있을 때까지 이전 버전의 TLS에서 계속 작동됩니다. 

TLS 1.2를 사용하도록 설정하려면:

1. 다음 레지스트리 키를 설정합니다.
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. 서버 다시 시작

  
## <a name="prepare-your-on-premises-environment"></a>온-프레미스 환경 준비
Azure AD 애플리케이션 프록시를 위한 환경을 준비하려면 먼저 Azure 데이터 센터와 통신할 수 있어야 합니다. 방화벽이 경로에 있는 경우 커넥터가 애플리케이션 프록시에 대한 HTTPS(TCP) 요청을 수행할 수 있도록 방화벽이 열려 있는지 확인합니다.

### <a name="open-ports"></a>포트 열기

**아웃바운드** 트래픽에 대한 다음 포트를 엽니다. 

   | 포트 번호 | 사용 방법 |
   | --- | --- |
   | 80 | SSL 인증서의 유효성을 검사하는 동안 CRL(인증서 해지 목록) 다운로드 |
   | 443 | 애플리케이션 프록시 서비스와의 모든 아웃바운드 통신 |

방화벽이 원래 사용자에 따라 트래픽에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 80 및 443 포트를 엽니다.

애플리케이션 프록시를 이미 사용 중인 경우 이전 버전의 커넥터가 설치되었을 수 있습니다.  이 자습서에 따라 최신 버전의 커넥터를 설치합니다. 1.5.132.0 이전 버전에는 다음 포트가 열려있어야 합니다. 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>URL에 대한 액세스 허용

다음 URL에 대한 액세스를 허용합니다.

| URL | 사용 방법 |
| --- | --- |
| \*.msappproxy.net<br>servicebus.windows.net | 커넥터와 애플리케이션 프록시 클라우드 서비스 간의 통신 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure에서는 다음과 같은 URL을 사용하여 인증서를 확인합니다. |
| login.windows.net<br>login.microsoftonline.com | 커넥터는 등록 프로세스 동안 다음과 같은 URL을 사용합니다. |

방화벽이나 프록시에서 DNS 허용 목록을 허용하면 msappproxy.net 및 servicebus.windows.net에 대한 연결을 허용 목록에 추가할 수 있습니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용해야 합니다.

## <a name="install-and-register-a-connector"></a>커넥터 설치 및 등록
애플리케이션 프록시를 사용하려면 애플리케이션 프록시 서비스에서 사용하려는 각 Windows Server에 커넥터를 설치해야 합니다. 커넥터는 온-프레미스 애플리케이션 서버에서 Azure AD의 애플리케이션 프록시에 대한 아웃바운드 연결을 관리하는 에이전트입니다. 또한 Azure AD Connect와 같은 다른 인증 에이전트가 설치되어 있는 서버에 커넥터를 설치할 수 있습니다.

커넥터를 설치하려면:

1. 애플리케이션 프록시를 사용하는 디렉터리의 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 예를 들어, 테넌트 도메인이 contoso.com이면 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 관리자 별칭이어야 합니다.
2. 현재 디렉터리가 오른쪽 위 모서리에 있는 사용자 이름 아래에 나타납니다. 애플리케이션 프록시를 사용하는 디렉터리에 로그인했는지 확인합니다. 디렉터리를 변경해야 하는 경우 해당 아이콘을 선택합니다.
3. 왼쪽 블레이드에서 **Azure Active Directory**를 클릭한 다음, **애플리케이션 프록시**를 클릭합니다.
4. **커넥터 서비스 다운로드**를 클릭합니다.
5. 서비스 약관을 참고하세요.  준비되면 **사용 약관 동의 및 다운로드**를 클릭합니다.
6. 창의 맨 아래에서 **AADApplicationProxyConnectorInstaller.exe**를 다운로드하라는 메시지가 표시됩니다. **실행**을 클릭하여 커넥터를 설치합니다. 설치 마법사가 열립니다. 
7. 마법사의 지침에 따라 설치합니다. Azure AD 테넌트에 대한 애플리케이션 프록시에서 커넥터를 등록하라는 메시지가 나타나면 애플리케이션 관리자 자격 증명을 제공합니다.
    - IE(Internet Explorer)에서 **IE 보안 강화 구성**이 **켜기**로 설정되어 있으면 등록 화면이 표지되지 않을 수 있습니다. 액세스하려면 오류 메시지의 지침에 따릅니다. Internet Explorer 보안 강화가 **끄기**로 설정되어 있는지 확인하세요.

### <a name="general-remarks"></a>일반적인 설명

이전에 커넥터를 설치한 경우 최신 버전을 다시 설치합니다.

온-프레미스 애플리케이션에서 둘 이상의 Windows Server가 설치되도록 선택하려는 경우 각 서버에서 커넥터를 설치하고 등록해야 합니다. 커넥터를 커넥터 그룹으로 구성할 수 있습니다. 자세한 내용은 [커넥터 그룹](application-proxy-connector-groups.md)을 참조하세요. 

조직에서 프록시 서버를 사용하여 인터넷에 연결하는 경우에는 애플리케이션 프록시에 맞게 프록시 서버를 구성해야 합니다.  자세한 내용은 [기존 온-프레미스 프록시 서버로 작업](application-proxy-configure-connectors-with-proxy-servers.md)을 참조하세요. 

커넥터, 용량 계획 및 최신 상태로 유지하는 방법에 대한 정보는 [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)를 참조하세요. 

애플리케이션에서 연결에 Websocket을 사용하는 경우 최신 커넥터를 설치했는지 확인합니다.  Websocket은 커넥터 버전 1.5.612.0 이상에서 지원됩니다.


## <a name="verify-the-connector-installed-and-registered-correctly"></a>커넥터가 설치되고 올바르게 등록되었는지 확인

Azure Portal 또는 Windows Server를 사용하여 새 커넥터가 올바르게 설치되었는지 확인할 수 있습니다.

### <a name="verify---azure-portal"></a>확인 - Azure Portal
커넥터가 설치되고 올바르게 등록되었는지 확인하려면:

1. [Azure Portal](https://portal.azure.com)에서 테넌트 디렉터리에 로그인합니다.
2. **Azure Active Directory**를 클릭한 다음, **애플리케이션 프록시**를 클릭합니다. 모든 커넥터 및 커넥터 그룹이 이 페이지에 나타납니다. 
3. 커넥터를 선택하여 세부 정보를 확인합니다. 활성 녹색 레이블은 커넥터 서비스에 연결할 수 있음을 나타냅니다. 그러나 레이블이 녹색인 경우에도 네트워크 문제로 인해 커넥터에서 메시지를 수신하지 않도록 차단할 수 있습니다. 

    ![AzureAD 애플리케이션 프록시 커넥터](./media/application-proxy-connectors/app-proxy-connectors.png)

커넥터를 설치하는 자세한 도움말은 [애플리케이션 프록시 커넥터를 설치하는 문제](application-proxy-connector-installation-problem.md)를 참조하세요.

### <a name="verify---windows-server"></a>확인 - Windows Server
커넥터가 설치되고 올바르게 등록되었는지 확인하려면:

1. **Windows** 로고 키를 클릭하고 *services.msc*를 입력하여 Windows 서비스 관리자를 엽니다.
2. 다음 두 서비스의 상태가 **실행 중**인지 확인합니다.
   - **Microsoft AAD 응용 프로그램 프록시 커넥터** 에서 연결 사용
   - **Microsoft AAD 응용 프로그램 프록시 커넥터 업데이터**는 자동화된 업데이트 서비스입니다. 업데이터에서 새 버전의 커넥터가 있는지 확인하고 필요에 따라 커넥터를 업데이트합니다.

    ![앱 프록시 커넥터 서비스 - 스크린샷](./media/application-proxy-enable/app_proxy_services.png)

3. 서비스의 상태가 **실행 중**이 아닌 경우 각 서비스를 마우스 오른쪽 단추로 클릭하고 **시작**을 선택합니다. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Azure AD에 온-프레미스 앱 추가

이제 환경을 준비하고 커넥터를 설치했으므로 Azure AD에 온-프레미스 애플리케이션을 추가할 준비가 되었습니다.  

1. [Azure Portal](https://portal.azure.com/)에서 관리자로 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **새로운 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 추가](./media/application-proxy-publish-azure-portal/add-app.png)

3. **모두**를 선택한 다음 **온-프레미스 응용 프로그램**을 선택합니다.  

    ![애플리케이션 직접 추가](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 애플리케이션에 대한 다음과 같은 정보를 제공합니다.

    ![애플리케이션 구성](./media/application-proxy-publish-azure-portal/configure-app.png)

    | 필드 | 설명 |
    | :---- | :---------- |
    | **Name** | 액세스 패널 및 Azure Portal에 표시될 애플리케이션의 이름입니다. |
    | **내부 URL** | 개인 네트워크 내부에서 애플리케이션에 액세스하기 위한 URL입니다. 나머지 서버는 게시되지 않은 반면 게시할 백 앤드 서버에 특정 경로를 제공할 수 있습니다. 이렇게 하면 다른 앱과 동일한 서버에 여러 사이트를 게시하고 각 사이트에 고유한 이름과 액세스 규칙을 부여할 수 있습니다.<br><br>경로를 게시하는 경우 애플리케이션에 필요한 이미지, 스크립트 및 스타일 시트를 모두 포함하는지 확인합니다. 예를 들어 앱이 https://yourapp/app에 있고 https://yourapp/media에 있는 이미지를 사용하는 경우 경로로 https://yourapp/을 게시해야 합니다. 이 내부 URL은 사용자에게 표시되는 방문 페이지일 필요가 없습니다. 자세한 내용은 [게시된 앱에 대해 사용자 지정 홈페이지 설정](application-proxy-configure-custom-home-page.md)을 참조하세요. |
    | **외부 URL** | 사용자가 네트워크 외부에서 앱에 액세스하기 위한 주소입니다. 기본 애플리케이션 프록시 도메인을 사용하지 않으려면 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조하세요.|
    | **사전 인증** | 애플리케이션 프록시가 사용자에게 애플리케이션에 대한 액세스 권한을 부여하기 전에 사용자를 확인하는 방법입니다.<br><br>**Azure Active Directory** - 애플리케이션 프록시는 Azure AD를 사용하여 로그인하도록 사용자를 리디렉션하여 디렉터리와 애플리케이션에 대한 사용 권한을 인증합니다. 조건부 액세스 및 Multi-Factor Authentication과 같은 Azure AD 보안 기능을 활용할 수 있도록 이 옵션을 기본값으로 유지하는 것이 좋습니다.<br><br>**통과** - 사용자는 애플리케이션에 액세스하기 위해 Azure Active Directory에 대해 인증할 필요가 없습니다. 백 엔드에 대한 인증 요구 사항은 여전히 설정할 수 있습니다. |
    | **커넥터 그룹** | 커넥터는 애플리케이션에 대한 원격 액세스를 처리하고, 커넥터 그룹은 지역, 네트워크 또는 용도별로 커넥터와 앱을 구성하는 데 도움을 줍니다. 아직 만든 커넥터 그룹이 없는 경우 앱이 **Default**(기본값)로 할당됩니다.<br><br>애플리케이션에서 연결에 Websocket을 사용하는 경우 그룹의 모든 커넥터는 버전 1.5.612.0 이상이어야 합니다.|

5. 필요한 경우 추가 설정을 구성합니다. 대부분의 애플리케이션에서는 다음과 같은 설정을 기본 상태로 유지해야 합니다. 

    ![애플리케이션 구성](./media/application-proxy-publish-azure-portal/additional-settings.png)

    | 필드 | 설명 |
    | :---- | :---------- |
    | **백 엔드 애플리케이션 시간 제한** | 애플리케이션의 인증 및 연결 속도가 느린 경우에만 이 값을 **Long**으로 설정합니다. |
    | **HTTP 전용 쿠키 사용** | Application Proxy 쿠키가 HTTP 응답 헤더에 HTTPOnly 플래그를 포함하도록 하려면 이 값을 **예**로 설정합니다. 원격 데스크톱 서비스를 사용하면 이 항목을 **아니요**로 설정합니다.|
    | **보안 쿠키 사용**| 이 값을 **예**로 설정하면 쿠키가 암호화된 HTTPS 요청과 같이 보안 채널을 통해 전송되도록 합니다.
    | **헤더의 URL 변환** | 애플리케이션이 인증 요청에서 원래 호스트 헤더를 요구하지 않는 한 이 값을 **예**로 유지합니다. |
    | **애플리케이션 본문의 URL 변환** | 다른 온-프레미스 애플리케이션에 HTML 링크를 하드 코드하지 않고 사용자 지정 도메인을 사용하지 않는 한 이 값을 **아니요**로 유지합니다. 자세한 내용은 [애플리케이션 프록시를 사용한 링크 변환](application-proxy-configure-hard-coded-link-translation.md)을 참조하세요. |
   


6. **추가**를 선택합니다.

## <a name="test-the-application"></a>애플리케이션 테스트

앱이 올바르게 추가되었는지 테스트하려면 애플리케이션에 사용자 계정을 추가하고 로그인을 시도합니다. 

### <a name="add-a-user-for-testing"></a>테스트할 사용자 추가
애플리케이션에 사용자를 추가하기 전에 사용자 계정에 이미 회사 네트워크 내부에서 애플리케이션에 액세스할 사용 권한이 있는지 확인합니다.

테스트 사용자를 추가히려면:

1. **빠른 시작** 블레이드로 돌아가서 **테스트할 사용자 지정**을 선택합니다.

    ![테스트할 사용자 지정](./media/application-proxy-publish-azure-portal/assign-user.png)

2. **사용자 및 그룹** 블레이드에서 **사용자 추가**를 선택합니다.

    ![사용자 또는 그룹 추가](./media/application-proxy-publish-azure-portal/add-user.png)

3. **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택한 다음, 추가할 계정을 선택합니다. 
4. **할당**을 선택합니다.

### <a name="test-the-sign-on"></a>로그온 테스트

애플리케이션에 대한 로그온을 테스트하려면:

1. 브라우저에서 게시 단계에서 구성한 외부 URL로 이동합니다. 
2. 시작 화면이 표시되고 설정한 테스트 계정으로 로그인할 수 있습니다.

    ![게시된 앱 테스트](./media/application-proxy-publish-azure-portal/test-app.png)

문제 해결은 [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 온-프레미스 환경에서 애플리케이션 프록시를 사용한 다음, 애플리케이션 프록시 커넥터를 설치하고 등록하도록 준비합니다. 다음으로, Azure AD 테넌트에 애플리케이션을 추가하고 Azure AD 계정으로 애플리케이션에 로그온하여 작동되는지 확인합니다.

다음 작업을 수행했습니다.
> [!div class="checklist"]
> * 아웃바운드 트래픽에 대한 포트 열기 및 특정 URL에 대한 액세스 허용
> * Windows Server에서 커넥터 설치 및 애플리케이션 프록시에서 등록
> * 커넥터가 설치되고 올바르게 등록되었는지 확인
> * Azure AD 테넌트에 온-프레미스 애플리케이션 추가
> * 테스트 사용자가 Azure AD 계정을 사용하여 애플리케이션에 로그온할 수 있는지 확인

이제 애플리케이션에 Single Sign-On을 구성할 준비가 완료되었습니다. 여러 가지 Single Sign-On 방법이 있으며, 최상의 방법은 애플리케이션에서 인증하는 방법에 따라 달라집니다. 다음 링크를 통해 애플리케이션에 적절한 Single Sign-On 자습서를 찾을 수 있습니다.

> [!div class="nextstepaction"]
>[Single Sign-on 구성](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





