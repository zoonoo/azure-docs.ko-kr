<properties
	pageTitle="Azure AD 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램에 대한 조건부 액세스"
	description="Azure AD 응용 프로그램 프록시를 사용하여, 게시하는 응용 프로그램에 대해 조건부 액세스를 설정하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="rkarlin"/>

# 조건부 액세스로 작업하기
> [AZURE.NOTE]응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

이제 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램에 액세스하는 사용자와 그룹에 조건부 액세스 권한을 부여하도록 액세스 규칙을 설정할 수 있습니다. 다음을 수행할 수 있습니다.

- 응용 프로그램 당 Multi-Factor Authentication 요구
- 사용자가 직장이 아닌 경우 Multi-Factor Authentication 요구
- 사용자가 직장이 아닌 경우 응용 프로그램에 액세스를 차단

모든 사용자 및 그룹 또는 특정 사용자 및 그룹에만 이러한 규칙을 적용할 수 있습니다. 기본적으로 규칙은 응용 프로그램에 대한 액세스 권한이 있는 모든 사용자에게 적용됩니다. 그러나 규칙은 지정된 보안 그룹의 구성원인 사용자로만 제한될 수도 있습니다.

사용자가 OAuth 2.0, OpenID Connect, SAML 또는 WS-Federation을 사용하는 페더레이션된 응용 프로그램에 액세스할 때 액세스 규칙이 평가됩니다. 또한 액세스 규칙은 Auth 2.0 및 OpenID Connect가 새로고침 토큰을 사용하여 액세스 토큰을 확보할 때 평가됩니다.

## 조건부 액세스 필수 조건

- Azure Active Directory Premium 구독 
- 페더레이션 또는 관리되는 Azure Active Directory 테넌트 
- 페더레이션된 테넌트가 해당 MFA(Multi-Factor Authentication)의 사용 요구 

![](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## 응용 프로그램당 Multi-Factor Authentication 구성
1. Azure 관리 포털에서 관리자로 로그인합니다.
2. Active Directory로 이동하여 응용 프로그램 프록시를 사용하도록 설정할 디렉터리를 선택합니다.
3. **응용 프로그램**을 클릭하고 아래로 스크롤하여 **액세스 규칙** 섹션을 클릭합니다. 액세스 규칙 섹션은 페더레이션된 인증을 사용하는 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램에 대해서만 표시됩니다.
4. **액세스 규칙 사용**을 **켜기**로 설정하여 규칙을 사용합니다.
5. 규칙을 적용할 사용자 및 그룹을 지정합니다. **그룹 추가** 단추를 사용하여 액세스 규칙이 적용되는 하나 이상의 그룹을 선택합니다. 이 대화 상자는 선택한 그룹을 제거하는 데 사용할 수도 있습니다. 그룹에 적용할 규칙이 선택되면 액세스 규칙이 지정된 보안 그룹 중 하나에 속한 사용자에 대해서만 적용됩니다. <br> 규칙에서 보안 그룹을 명시적으로 제외하려면 **제외**를 선택하고 하나 이상의 그룹을 지정합니다. 제외 목록에 있는 그룹 회원인 사용자는 Multi-Factor Authentication을 수행하지 않아도 됩니다. <br>사용자별 Multi-Factor Authentication 기능을 사용하여 사용자가 구성된 경우 이 설정이 응용 프로그램 Multi-Factor Authentication 규칙보다 우선적으로 적용됩니다. 즉, 사용자별 Multi-Factor Authentication을 위해 구성된 사용자는 응용 프로그램의 Multi-Factor Authentication 규칙에서 제외되더라도 Multi-Factor Authentication을 수행해야 함을 의미합니다. [Multi-Factor Authentication 및 사용자별 설정에 대해 자세히 알아보세요](../multi-factor-authentication/multi-factor-authentication.md). 
6. 설정하려는 액세스 규칙을 선택합니다.
	- **Multi-Factor Authentication 필요**: 액세스 규칙이 적용되는 사용자는 규칙이 적용되는 응용 프로그램에 액세스하기 전에 Multi-Factor Authentication을 완료해야 합니다.
	- **직장이 아닐 때 Multi-Factor Authentication 요구**: 신뢰되는 IP 주소에서 응용 프로그램에 액세스하고자 하는 사용자는 Multi-Factor Authentication을 수행하지 않아도 됩니다. 신뢰되는 IP 주소 범위는 Multi-Factor Authentication 설정 페이지에서 구성할 수 있습니다.
	- **직장이 아닐 때 액세스 차단**: 회사 네트워크 외부에서 응용 프로그램에 액세스하고자 하는 사용자는 응용 프로그램에 액세스할 수 없습니다.


## 페더레이션 서비스에 대한 MFA 구성
페더레이션된 테넌트의 경우 MFA(Multi-Factor Authentication)를 Azure Active Directory 또는 온-프레미스 AD FS 서버에서 수행할 수 있습니다. 기본적으로 MFA는 Azure Active Directory에서 호스팅되는 모든 페이지에 발생합니다. MFA 온-프레미스를 구성하려면 Windows PowerShell을 실행하고 Azure AD 모듈을 설정하려면 – SupportsMFA 속성을 사용합니다. 다음 예제는 contoso.com 테넌트에서 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx)을 사용하여 온-프레미스 MFA를 사용하는 방법을 보여줍니다. `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true ` 이 플래그를 설치하는 것 외에도 페더레이션된 테넌트 AD FS 인스턴스가 Multi-Factor Authentication을 수행하도록 구성해야 합니다. [온-프레미스에 Microsoft Azure Multi-Factor Authentication을 배포](..multi-factor-authentication-get-started-server.md)하기 위한 지침을 따르세요.
## 참고 항목
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)

## 응용 프로그램 프록시에 대해 자세히 알아보기
- [온라인 도움말에서 살펴보기](active-directory-application-proxy-enable.md)
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스

* [조직으로 Azure 등록](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)

<!---HONumber=Oct15_HO4-->