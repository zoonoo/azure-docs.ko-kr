<properties
	pageTitle="온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법"
	description="Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="kgremban"/>

# 온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법

> [AZURE.NOTE]응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

장치, 태블릿, 스마트폰, 노트북 등 관리되거나 관리되지 않는 모든 종류의 장치를 소유한 원격 사용자에게 액세스를 제공하려고 합니다. 하지만 다양한 리소스에 대해 보안 액세스를 제공하는 것은 복잡할 수 있습니다. 최근에는 보안된 원격 액세스를 제공하는 데 역방향 프록시가 많이 사용되었으나 역방향 프록시는 보안 및 고가용성을 제공하기가 어려운 방화벽으로 보호되어야 합니다.

## 클라우드의 보안된 원격 액세스
최신 클라우드 환경에서는 Microsoft Azure AD(Active Directory)의 응용 프로그램 프록시를 사용하여 더 높은 수준의 원격 액세스를 제공합니다. 응용 프로그램 프록시는 서비스로 제공되는 Azure AD 기능으로, 배포 및 사용이 용이합니다. Office 365에서 사용하는 것과 동일한 ID 플랫폼인 Azure AD와 통합됩니다.

## Azure Active Directory 응용 프로그램 프록시란?
응용 프로그램 프록시는 SharePoint 사이트 및 Outlook Web Access와 같은 온-프레미스에 호스트된 웹 응용 프로그램에 SSO(Single Sign-On) 및 보안된 원격 액세스를 제공합니다. 이제 VPN을 사용하거나 네트워크 인프라를 변경할 필요 없이 Azure Active Directory의 SaaS 앱과 동일한 방식으로 온-프레미스 웹 응용 프로그램에 액세스할 수 있습니다. 응용 프로그램 프록시를 사용하면 응용 프로그램을 게시할 수 있으며 직원들은 자택이나 개인용 장치를 사용하여 앱에 로그인하고 이 클라우드 기반 프록시를 통해 인증할 수 있습니다.

## 작동 원리
### 액세스 사용
응용 프로그램 프록시는 네트워크 내부에서 커넥터라고 불리는 간단한 Windows Server 서비스를 설치하여 사용합니다. 커넥터는 인바운드 포트를 열 필요가 없으며 DMZ에 항목을 지정할 필요도 없습니다. 앱에 대한 트래픽이 많은 경우 커넥터를 더 추가하면 서비스에서 부하 분산을 처리합니다. 커넥터는 상태를 저장하지 않으며 필요에 따라 클라우드에서 모든 항목을 가져옵니다. 사용자가 장치에서 원격으로 응용 프로그램에 액세스하면 Azure Active Directory에 의해 인증되어 응용 프로그램에 액세스할 수 있습니다.

### SSO(Single sign-on)
Azure AD 응용 프로그램 프록시는 IWA(Windows 통합 인증) 또는 클레임 인식 응용 프로그램을 사용하는 응용 프로그램에 Single Sign-On을 제공합니다. 응용 프로그램에서 IWA를 사용하는 경우 응용 프로그램 프록시는 SSO를 제공하는 Kerberos 제한 위임을 사용하여 사용자를 가장합니다. Azure Active Directory를 신뢰하는 클레임 인식 응용 프로그램이 있는 경우에는 사용자가 이미 Azure AD에 의해 인증되었으므로 SSO가 실현된 것입니다.

## 시작하는 방법
Azure AD 기본 또는 프리미엄 구독이 있고 자신이 전역 관리자인 Azure AD 디렉터리가 있는지 확인합니다. 앱에 액세스하는 디렉터리 관리자 및 사용자용 Azure AD 기본 또는 프리미엄 라이선스도 필요합니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 살펴보세요.

### 온-프레미스 응용 프로그램에 대한 원격 액세스 사용 시작하기
응용 프로그램 프록시 설치는 다음 두 단계로 수행됩니다.

1. [응용 프로그램 프록시를 사용하도록 설정하고 커넥터 구성](active-directory-application-proxy-enable.md)  
2. [응용 프로그램 게시](active-directory-application-proxy-publish.md) - 쉽고 빠른 마법사를 사용하여 온-프레미스 앱을 게시하고 원격으로 액세스할 수 있도록 합니다.

## 다음 작업
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)


### 응용 프로그램 프록시에 대해 자세히 알아보기
- [온라인 도움말에서 살펴보기](active-directory-application-proxy-enable.md)
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스
* [조직으로 Azure 등록](../sign-up-organization.md)
* [Azure ID](../fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->