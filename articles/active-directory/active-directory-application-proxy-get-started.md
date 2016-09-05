<properties
	pageTitle="온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법"
	description="Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="kgremban"/>

# 온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법

> [AZURE.NOTE] 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

요즈음 직원은 어디서나 언제든지 어느 장치에서나 생산성을 높이기를 원합니다. 태블릿, 휴대폰 또는 랩톱을 막론하고 자신의 장치에서 일하기를 원합니다. 그리고 해당하는 모든 응용 프로그램인 클라우드의 SaaS 앱 및 회사 앱 온-프레미스 모두에도 액세스할 수 있다고 예상합니다. 온-프레미스 응용 프로그램에 대한 액세스를 제공하려면 일반적으로 가상 사설망(VPN), 완충 영역(DMZ) 또는 온-프레미스 역방향 프록시가 필요했습니다. 이러한 솔루션은 복잡하고 안전하게 만들기도 어려울 뿐만 아니라 설정과 관리에도 비용이 많이 듭니다.

더 나은 방법이 있습니다!

모바일 중심, 클라우드 중심의 최신 인력에게는 최신 원격 액세스 솔루션을 필요합니다. Azure AD 응용 프로그램 프록시는 Azure Active Directory Premium 제품의 기능이며 원격 액세스 서비스를 제공합니다. 즉, 배포, 사용 및 관리하기 쉽습니다.

## Azure Active Directory 응용 프로그램 프록시란?
Azure AD 응용 프로그램 프록시는 온-프레미스에 호스트된 웹 응용 프로그램에 대한 SSO(Single Sign-On) 및 보안된 원격 액세스를 제공합니다. 이는 SharePoint 사이트, Outlook Web Access 또는 사용자가 가지고 있는 다른 LOB 웹 응용 프로그램을 포함할 수 있습니다. 이러한 온-프레미스 웹 응용 프로그램은 Azure AD, 동일한 ID 및 O365에서 사용되는 제어 플랫폼과 통합됩니다. 최종 사용자는 O365 및 Azure AD와 통합된 다른 SaaS 앱에 액세스할 때와 같은 방식으로 온-프레미스 응용 프로그램에 액세스할 수 있습니다. 사용자에게 이 솔루션을 제공하기 위해 네트워크 인프라를 변경하거나 VPN을 요구할 필요가 없습니다.

## 이 솔루션이 더 나은 이유?
Azure AD 응용 프로그램 프록시는 모든 온-프레미스 응용 프로그램에 대해 단순하고 보안되고 비용 효율적인 원격 액세스 솔루션을 제공합니다.

Azure AD 응용 프로그램 프록시:

- 클라우드에서 작업하므로 시간과 비용을 절약할 수 있습니다. 온-프레미스 솔루션을 사용 하려면 DMZ, 에지 서버 또는 기타 복잡한 인프라를 설정하고 유지 관리해야 합니다.

- 방화벽을 통해 인바운드 연결을 열 필요가 없으므로 온-프레미스 솔루션보다 설정과 보안 확보가 더 쉽습니다.

- 또한 훌륭한 보안을 제공합니다. Azure AD 응용 프로그램 프록시를 사용하여 앱을 게시하면 Azure의 다양한 권한 부여 제어 및 보안 분석을 이용할 수 있습니다. 즉, 앱을 변경할 필요 없이 모든 기존 앱에 대해 고급 보안 기능을 얻습니다.

- 사용자에게 일관된 인증 환경을 제공합니다. SSO(Single Sign-On)는 최종 사용자에게 생산성을 높여야 하는 모든 앱에 대해 암호 하나를 사용하는 쉽고 단순한 액세스를 제공합니다.

## 어떤 종류의 응용 프로그램이 Azure AD 응용 프로그램 프록시에서 작동합니까?
Azure AD 응용 프로그램 프록시를 사용하면 다양한 유형의 내부 응용 프로그램에 액세스할 수 있습니다.

- 인증을 위해 Windows 통합 인증을 사용하는 웹 응용 프로그램
- 폼 기반 액세스를 사용하는 웹 응용 프로그램
- 여러 장치에서 다양한 응용 프로그램을 표시하려는 웹 API
- 원격 데스크톱 게이트웨이 뒤에서 호스트되는 응용 프로그램

## 작동 원리
응용 프로그램 프록시는 네트워크 내에서 커넥터라는 간단한 Windows Server 서비스를 설치하여 사용합니다. 커넥터를 사용하면 인바운드 포트를 열거나 DMZ에 항목을 저장할 필요가 없습니다. 앱에 대한 트래픽이 많은 경우 커넥터를 더 추가하면 서비스에서 부하 분산을 처리합니다. 커넥터는 상태를 저장하지 않으며 필요에 따라 클라우드에서 모든 항목을 가져옵니다.

사용자가 응용 프로그램에 원격으로 액세스 하는 경우 게시된 끝점에 연결됩니다. 사용자는 Azure AD에서 인증한 다음 커넥터를 통해 온-프레미스 응용 프로그램에 라우팅됩니다.

 ![Azure Ad 응용 프로그램 프록시 다이어그램](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. 사용자는 응용 프로그램 프록시를 통해 응용 프로그램에 액세스하고 인증을 위해 Azure AD 로그인 페이지로 전달됩니다.
2. 성공적인 로그인 후에 토큰을 생성하고 사용자에게 보냅니다.
3. 사용자는 토큰에서 사용자 주체 이름(UPN) 및 보안 주체 이름(SPN)을 검색하고 커넥터에 요청을 전달하는 응용 프로그램 프록시에 토큰을 보냅니다.
4. 커넥터는 사용자를 대신하여 내부(Windows) 인증에 사용할 수 있는 Kerberos 티켓을 요청합니다. Kerberos 제한 위임 정보라고 합니다.
5. Active Directory는 Kerberos 티켓을 검색합니다.
6. 티켓은 응용 프로그램 서버에 전송되고 확인됩니다.
7. 응답은 응용 프로그램 프록시를 통해 최종 사용자에게 전송됩니다.

### SSO(Single sign-on)
Azure AD 응용 프로그램 프록시는 Windows 통합 인증(IWA) 또는 클레임 인식 응용 프로그램을 사용하는 응용 프로그램에 SSO(Single Sign-On)를 제공합니다. 응용 프로그램에서 IWA를 사용하는 경우 응용 프로그램 프록시는 SSO를 제공하는 Kerberos 제한 위임을 사용하여 사용자를 가장합니다. Azure Active Directory를 신뢰하는 클레임 인식 응용 프로그램이 있는 경우에는 사용자가 이미 Azure AD에 의해 인증되었으므로 SSO가 실현된 것입니다.

## 시작하는 방법
Azure AD 기본 또는 프리미엄 구독이 있고 자신이 전역 관리자인 Azure AD 디렉터리가 있는지 확인합니다. 앱에 액세스하는 디렉터리 관리자 및 사용자용 Azure AD 기본 또는 프리미엄 라이선스도 필요합니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

응용 프로그램 프록시 설치는 다음 두 단계로 수행됩니다.

1. [응용 프로그램 프록시를 사용하도록 설정하고 커넥터 구성](active-directory-application-proxy-enable.md)
2. [응용 프로그램 게시](active-directory-application-proxy-publish.md) - 쉽고 빠른 마법사를 사용하여 온-프레미스 앱을 게시하고 원격으로 액세스할 수 있도록 합니다.

## 다음 작업
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)를 확인하세요.

<!---HONumber=AcomDC_0824_2016-->