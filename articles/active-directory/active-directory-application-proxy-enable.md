<properties
	pageTitle="Azure AD 응용 프로그램 프록시 사용 | Microsoft Azure"
	description="Azure AD 응용 프로그램 프록시를 작동시키는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Azure AD 응용 프로그램 프록시 사용
> [AZURE.NOTE] 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

Microsoft Azure Active Directory 응용 프로그램 프록시를 사용하면 SharePoint 사이트, Outlook Web Access, IIS 기반 앱과 같은 응용 프로그램을 개인 네트워크 내에 게시할 수 있으며 네트워크 외부 사용자가 안전하게 액세스할 수 있게 합니다. 직원들은 자택에서 개인용 장치를 사용하여 앱에 로그인하고 이 클라우드 기반 프록시를 통해 인증할 수 있습니다.

응용 프로그램 프록시는 네트워크 내에서 커넥터라고 불리는 간단한 Windows 서비스를 설치하여 사용합니다. 커넥터는 네트워크 내에서 프록시 서비스로 아웃 바운드 연결을 유지 관리합니다. 사용자가 게시된 응용 프로그램에 액세스할 때 프록시가 이 연결을 사용하여 응용 프로그램에 액세스를 제공합니다.

이 문서에서는 Azure AD에서 클라우드 디렉터리에 대해 Microsoft Azure AD 응용 프로그램 프록시를 사용하도록 설정하고, 개인 네트워크에 응용 프로그램 프록시 커넥터를 설치하며, Microsoft Azure AD 테넌트 구독을 사용하여 커넥터를 등록하는 방법에 대해 살펴봅니다.

## 응용 프로그램 프록시 필수 구성 요소
응용 프로그램 프록시 서비스를 사용하도록 설정하고 사용하려면 다음이 필요합니다.

- 사용자가 전역 관리자인 Microsoft Azure AD [기본 또는 프리미엄 구독](active-directory-editions.md) 및 Azure AD 디렉터리입니다.
- 응용 프로그램 프록시 커넥터를 설치할 수 있는 Windows Server 2012 R2 또는 Windows 8.1 이상을 실행하는 서버. 이 서버는 클라우드의 응용 프로그램 프록시 서비스에 HTTPS 요청을 보낼 수 있어야 하며 게시하려는 응용 프로그램에 대한 HTTPS 연결이 있어야 합니다.
- 경로에 방화벽이 있는 경우 방화벽이 커넥터에서 응용 프로그램 프록시로 발생하는 HTTPS(TCP) 요청을 허용하도록 열려 있는지 확인합니다. 커넥터는 이러한 포트를 상위 수준 도메인(msappproxy.net 및 servicebus.windows.net)의 일부인 하위 도메인과 함께 사용합니다. **아웃바운드** 트래픽에 대해 다음 포트가 **모두** 열려 있는지 확인합니다.

포트 번호 | 설명
--- | ---
80 | 보안 유효성 검사에 아웃바운드 HTTP 트래픽을 사용하도록 설정합니다.
443 | Azure AD (커넥터 등록 프로세스에만 필요)에 대해 사용자 인증을 사용하도록 설정합니다.
10100 - 10120 | LOB HTTP 응답을 프록시로 다시 전송하도록 설정합니다.
9352, 5671 | 수신 요청에 대한 Azure 서비스와 커넥터 간에 통신을 사용하도록 설정합니다.
9350 | 선택적으로 수신 요청에 더 높은 성능을 발휘하도록 설정합니다.
8080 | 커넥터 부트스트랩 시퀀스 및 커넥터 자동 업데이트를 사용하도록 설정합니다.
9090 | 커넥터 등록(커넥터 등록 프로세스에만 필요)을 사용하도록 설정합니다.
9091 | 커넥터 신뢰 인증서 자동 갱신을 사용하도록 설정합니다.

방화벽이 원래 사용자에 따라 트래픽에 적용되는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다. 또한 NT Authority\\System에 대해 포트 8080을 사용하도록 설정해야 합니다.


## 1단계: Azure AD에서 응용 프로그램 프록시를 사용하도록 설정
1. Azure 클래식 포털에서 관리자로 로그인합니다.
2. Active Directory로 이동하여 응용 프로그램 프록시를 사용하도록 설정할 디렉터리를 선택합니다.
3. **구성**을 클릭하고 응용 프로그램 프록시로 스크롤하여 이 디렉터리에 대해 응용 프로그램 프록시 서비스 사용을 **사용**으로 설정합니다.

	![응용 프로그램 프록시 사용](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

4. 화면 아래쪽에서 **지금 다운로드**를 클릭합니다. 이렇게 하면 다운로드 페이지로 이동합니다. 사용 조건을 읽어보고 동의한 다음 **다운로드**를 클릭하여 응용 프로그램 프록시 커넥터의 Windows Installer 파일(.exe)을 저장합니다.

## 2단계: 커넥터 설치 및 등록
1. 준비한 서버에서 `AADApplicationProxyConnectorInstaller.exe`을 실행합니다.(위의 응용 프로그램 프록시 필수 구성 요소 참조)
2. 마법사의 지침에 따라 설치합니다.
3. 설치하는 동안 Azure AD 테넌트의 응용 프로그램 프록시를 사용하여 커넥터를 등록하라는 메시지가 표시됩니다.

  - Azure AD 전역 관리자 자격 증명을 제공합니다. 전역 관리자 테넌트는 Microsoft Azure 자격 증명과 다를 수 있습니다.
  - 커넥터를 등록하는 관리자가 응용 프로그램 프록시 서비스를 사용하도록 설정한 디렉터리와 동일한 디렉터리에 있는지 확인합니다. 예를 들어 테넌트 도메인이 contoso.com인 경우 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 별칭이어야 합니다.
  - Azure AD 커넥터를 설치하는 서버에 IE 보안 강화 구성이 **설정**으로 지정되어 있으면 등록 화면이 차단될 수 있습니다. 이 문제가 발생하면 오류 메시지의 지침에 따라 액세스를 허용합니다. Internet Explorer 보안 강화가 해제되어 있는지 확인하세요.
  - 커넥터 등록에 실패한 경우 [응용 프로그램 프록시 문제 해결](active-directory-application-proxy-troubleshoot.md)을 참조하세요.  

4. 설치가 완료되면 아래와 같이 두 개의 새 서비스가 서버에 추가됩니다. 하나는 연결 기능을 사용할 수 있는 커넥터 서비스이며, 다른 하나는 정기적으로 커넥터의 새 버전을 확인하고 필요에 따라 커넥터를 업데이트하는 자동화된 업데이트 서비스입니다. 설치를 완료하려면 설치 창에서 **마침**을 클릭합니다.

	![응용 프로그램 프록시 커넥터 서비스](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. 이제 [응용 프로그램 프록시를 사용하여 응용 프로그램을 게시](active-directory-application-proxy-publish.md)할 준비가 되었습니다.

고가용성을 위해 하나 이상의 추가 커넥터를 배포해야 합니다. 추가 커넥터를 배포하려면 위의 2단계와 3단계를 반복합니다. 각 커넥터는 별도로 등록되어야 합니다.

커넥터를 제거하려면 커넥터 서비스 및 업데이트 프로그램 서비스를 제거한 다음 컴퓨터를 다시 시작하여 서비스를 완전히 제거해야 합니다.


## 참고 항목
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)

## 응용 프로그램 프록시에 대해 자세히 알아보기
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [조직으로 Azure에 등록](sign-up-organization.md)
- [Azure ID](fundamentals-identity.md)
- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0211_2016-->