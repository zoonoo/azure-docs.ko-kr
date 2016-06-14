<properties
	pageTitle="Azure AD 응용 프로그램 프록시 사용 | Microsoft Azure"
	description="Azure 클래식 포털에서 응용 프로그램 프록시를 설정하고 역방향 프록시에 커넥터를 설치합니다."
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
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="kgremban"/>

# Azure 포털에서 응용 프로그램 프록시 사용

이 문서에서는 Azure AD에서 클라우드 디렉터리에 Microsoft Azure AD 응용 프로그램 프록시를 사용하는 방법을 안내합니다. 개인 네트워크에 응용 프로그램 프록시 커넥터를 설치하는 과정을 포함하여 네트워크에서 프록시 서비스로의 연결을 유지 관리합니다. 또한 Microsoft Azure AD 테넌트 구독을 사용하여 커넥터를 등록합니다. 도움을 받을 수 있는 응용 프로그램 프록시에 익숙하지 않은 경우 [ 온-프레미스 응용 프로그램에 안전한 원격 액세스를 제공하는 방법](active-directory-application-proxy-get-started.md)에 대해 자세히 알아봅니다.

이 연습을 완료하여 Azure AD 응용 프로그램 프록시를 사용하도록 설정하면 원격 액세스를 위해 온-프레미스 앱을 게시할 준비가 됩니다.

> [AZURE.NOTE] 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

## 응용 프로그램 프록시 필수 구성 요소
응용 프로그램 프록시 서비스를 사용하도록 설정하고 사용하려면 다음이 필요합니다.

- 사용자가 전역 관리자인 Microsoft Azure AD [기본 또는 프리미엄 구독](active-directory-editions.md) 및 Azure AD 디렉터리입니다.
- 응용 프로그램 프록시 커넥터를 설치할 수 있는 Windows Server 2012 R2 또는 Windows 8.1 이상을 실행하는 서버. 이 서버는 클라우드의 응용 프로그램 프록시 서비스에 HTTPS 요청을 보낼 수 있어야 하며 게시하려는 응용 프로그램에 대한 HTTPS 연결이 필요합니다.
- 방화벽이 경로에 있는 경우 커넥터가 응용 프로그램 프록시에 HTTPS(TCP) 요청을 할 수 있도록 방화벽이 열려 있는지 확인합니다. 커넥터는 이러한 포트를 상위 수준 도메인(*msappproxy.net* 및 *servicebus.windows.net*)의 일부인 하위 도메인과 함께 사용합니다. **아웃바운드** 트래픽에 대해 다음 포트가 **모두** 열려 있는지 확인합니다.

	| 포트 번호 | 설명 |
	| --- | --- |
	| 80 | 보안 유효성 검사에 아웃바운드 HTTP 트래픽을 사용하도록 설정합니다. |
	| 443 | Azure AD (커넥터 등록 프로세스에만 필요)에 대해 사용자 인증을 사용하도록 설정합니다. |
	| 10100 - 10120 | LOB HTTP 응답을 프록시로 다시 전송하도록 설정합니다. |
	| 9352, 5671 | 수신 요청에 대한 Azure 서비스와 커넥터 간에 통신을 사용하도록 설정합니다. |
	| 9350 | 선택적으로 수신 요청에 더 높은 성능을 발휘하도록 설정합니다. |
	| 8080 | 커넥터 부트스트랩 시퀀스 및 커넥터 자동 업데이트를 사용하도록 설정합니다. |
	| 9090 | 커넥터 등록(커넥터 등록 프로세스에만 필요)을 사용하도록 설정합니다. |
	| 9091 | 커넥터 신뢰 인증서 자동 갱신을 사용하도록 설정합니다. |

방화벽이 원래 사용자에 따라 트래픽에 적용되는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다. 또한 NT Authority\\System에 대해 포트 8080을 사용하도록 설정해야 합니다.


## 1단계: Azure AD에서 응용 프로그램 프록시를 사용하도록 설정
1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 관리자로 로그인합니다.
2. Active Directory로 이동하여 응용 프로그램 프록시를 사용하도록 설정할 디렉터리를 선택합니다.

	![Active Directory - 아이콘](./media/active-directory-application-proxy-enable/ad_icon.png)

3. 디렉터리 페이지에서 **구성**을 선택하고 **응용 프로그램 프록시**까지 아래로 스크롤합니다.
4. **이 디렉터리에 응용 프로그램 프록시 서비스 사용**을 **사용**하도록 설정/해제합니다.

	![응용 프로그램 프록시 사용](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. **지금 다운로드**를 선택합니다. 이렇게 하면 **Azure AD 응용 프로그램 프록시 커넥터 다운로드**로 이동합니다. 사용 조건을 읽어보고 동의한 다음 **다운로드**를 클릭하여 응용 프로그램 프록시 커넥터의 Windows Installer 파일(.exe)을 저장합니다.

## 2단계: 커넥터 설치 및 등록
1. 위의 필수 구성 요소에 따라 준비한 서버에서 *AADApplicationProxyConnectorInstaller.exe*를 실행합니다.
2. 마법사의 지침에 따라 설치합니다.
3. 설치하는 동안 Azure AD 테넌트의 응용 프로그램 프록시를 사용하여 커넥터를 등록하라는 메시지가 표시됩니다.

  - Azure AD 전역 관리자 자격 증명을 제공합니다. 전역 관리자 테넌트는 Microsoft Azure 자격 증명과 다를 수 있습니다.
  - 커넥터를 등록하는 관리자가 응용 프로그램 프록시 서비스를 사용하도록 설정한 디렉터리와 동일한 디렉터리에 있는지 확인합니다. 예를 들어 테넌트 도메인이 contoso.com인 경우 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 별칭이어야 합니다.
  - Azure AD 커넥터를 설치하는 서버에 **IE 보안 강화 구성**이 **켜기**로 설정되어 있으면 등록 화면이 차단될 수 있습니다. 이 문제가 발생하면 오류 메시지의 지침에 따라 액세스를 허용합니다. Internet Explorer 보안 강화가 해제되어 있는지 확인하세요.
  - 커넥터 등록에 실패한 경우 [응용 프로그램 프록시 문제 해결](active-directory-application-proxy-troubleshoot.md)을 참조하세요.  

4. 설치가 완료되면 아래와 같이 두 개의 새 서비스가 서버에 추가됩니다.

 	- **Microsoft AAD 응용 프로그램 프록시 커넥터**에서 연결 사용
	- **Microsoft AAD 응용 프로그램 프록시 커넥터 업데이터**는 정기적으로 커넥터의 새 버전을 확인하고 필요에 따라 커넥터를 업데이트하는 자동화된 업데이트 서비스입니다.

	![앱 프록시 커넥터 서비스 - 스크린샷](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. 설치를 완료하려면 설치 창에서 **마침**을 클릭합니다.

이제 [응용 프로그램 프록시를 사용하여 응용 프로그램을 게시](active-directory-application-proxy-publish.md)할 준비가 되었습니다.

고가용성을 위해 하나 이상의 추가 커넥터를 배포해야 합니다. 추가 커넥터를 배포하려면 위의 2와 3단계를 반복합니다. 각 커넥터는 별도로 등록되어야 합니다.

커넥터를 제거하려면 커넥터 서비스 및 업데이트 프로그램 서비스를 제거한 다음 컴퓨터를 다시 시작하여 서비스를 완전히 제거해야 합니다.


## 다음 단계

- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)를 확인하세요.

<!---HONumber=AcomDC_0608_2016-->