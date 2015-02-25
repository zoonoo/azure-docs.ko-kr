<properties 
	pageTitle="Azure 웹 사이트에서 LOB(기간 업무) 앱 인증 및 권한 부여" 
	description="Azure 웹 사이트에 배포되는 LOB(기간 업무) 응용 프로그램에 대한 다양한 인증 및 권한 부여 옵션을 알아봅니다." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Azure 웹 사이트에서 LOB(기간 업무) 응용 프로그램의 사용자 인증 및 권한 부여 #

[Azure 웹 사이트](http://azure.microsoft.com/ko-kr/services/websites/)는 온-프레미스 환경에서든 공용 인터넷에서든 사용자가 SSO(Single Sign-On)를 통해 응용 프로그램에 액세스할 수 있도록 하여 엔터프라이즈 LOB(기간 업무) 응용 프로그램 시나리오를 지원합니다. AAD([Azure Active Directory](http://azure.microsoft.com/ko-kr/services/active-directory/)) 또는 온-프레미스 STS(보안 토큰 서비스)(예: AD FS(Active Directory Federation Services))와 통합되어 내부 AD(Active Directory) 사용자를 인증하고 적절하게 권한을 부여할 수 있습니다.

## 원활한 인증 및 권한 부여 ##

단추를 몇 번 클릭하는 것만으로 웹 사이트에 대한 인증 및 권한 부여를 설정할 수 있습니다. 모든 Azure 웹 사이트의 확인란 스타일 구성에서 LOB 웹 사이트에 대한 기본 액세스 제어를 제공합니다. 이는 사용자에게 모든 웹 사이트 콘텐츠에 대한 액세스 권한을 부여하기 전에 선택한 Azure AD 테넌트에 HTTPS 및 인증을 적용하는 방식으로 구현됩니다. 자세한 내용은 [Azure 웹 사이트 인증/권한 부여](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/)를 참조하세요.

>[AZURE.NOTE] 이 기능은 현재 미리 보기로 제공됩니다.

## 수동으로 인증 및 권한 부여 구현 ##

많은 시나리오에서 로그인 및 로그아웃 페이지, 사용자 지정 권한 부여 논리, 다중 테넌트 응용 프로그램 동작 등 응용 프로그램의 인증 및 권한 부여 동작을 사용자 지정할 수 있습니다. 이러한 경우 해당 기능의 유연성 향상을 위해 인증 및 권한 부여를 수동으로 구성하는 것이 좋을 수 있습니다. 두 가지 기본 옵션은 다음과 같습니다.  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/)  - Azure AD를 사용하여 웹 사이트에 대한 인증 및 권한 부여를 구현할 수 있습니다. Azure AD를 ID 공급자로 사용할 경우의 특징은 다음과 같습니다.
	-	[OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) 및 [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0)과 같은 일반적인 인증 프로토콜을 지원합니다. 지원되는 전체 프로토콜 목록은 [Azure Active Directory 인증 프로토콜](http://msdn.microsoft.com/ko-kr/library/azure/dn151124.aspx)을 참조하세요.
	-	온-프레미스 인프라 없이 Azure 전용 ID 공급자를 사용할 수 있습니다.
	-	또한 온-프레미스 AD(관리되는 온-프레미스)를 사용하여 디렉터리 동기화를 구성할 수 있습니다.
	-	온-프레미스 AD의 디렉터리 동기화를 사용하는 Azure AD는 AD 사용자가 인트라넷 및 인터넷에서 액세스할 때 웹 사이트에 대한 원활한 SSO 환경을 지원합니다. 인트라넷의 경우 AD 사용자는 통합 인증을 통해 웹 사이트에 자동으로 액세스할 수 있으며, 인터넷의 경우 AD 사용자는 자신의 Windows 자격 증명을 사용하여 웹 사이트에 로그인할 수 있습니다.
	-	Azure, Office 365, Dynamics CRM Online, Windows InTune 및 수천 개의 타사 클라우드 응용 프로그램을 포함하여 [Azure AD에서 지원하는 모든 응용 프로그램](http://azure.microsoft.com/ko-kr/marketplace/active-directory/)에 SSO를 제공합니다. 
	-	Azure AD는 [신뢰 당사자](http://en.wikipedia.org/wiki/Relying_party) 응용 프로그램의 관리를 관리자가 아닌 역할에 위임하지만, 중요한 디렉터리 데이터에 대한 응용 프로그램 액세스는 여전히 전역 관리자가 구성해야 합니다.
	-	모든 신뢰 당사자 응용 프로그램에 대한 범용 클레임 유형 집합을 보냅니다. 클레임 유형 목록은 [지원되는 토큰 및 클레임 유형](http://msdn.microsoft.com/ko-kr/library/azure/dn195587.aspx)을 참조하세요. 클레임은 사용자 지정할 수 없습니다.
	-	[Azure AD Graph API](http://msdn.microsoft.com/ko-kr/library/azure/hh974476.aspx)는 Azure AD의 디렉터리 데이터에 대한 응용 프로그램 액세스를 지원합니다.
-	[AD FS와 같은 온-프레미스 STS(보안 토큰 서비스)](../web-sites-dotnet-lob-application-adfs/) - AD FS와 같은 온-프레미스 STS를 사용하여 웹 사이트에 대한 인증 및 권한 부여를 구현할 수 있습니다. 온-프레미스 AD FS를 사용할 경우의 특징은 다음과 같습니다.
	-	AD FS 토폴로지를 온-프레미스에 배포해야 하므로 비용 및 관리 오버헤드가 발생합니다.
	-	회사 정책에 따라 AD 데이터를 온-프레미스에 저장해야 하는 경우에 가장 적합합니다.
	-	AD FS 관리자만 [신뢰 당사자 트러스트 및 클레임 규칙](http://technet.microsoft.com/ko-kr/library/dd807108.aspx)을 구성할 수 있습니다.
	-	응용 프로그램별로 [클레임](http://technet.microsoft.com/ko-kr/library/ee913571.aspx)을 관리할 수 있습니다.
	-	회사 방화벽을 통해 온-프레미스 AD 데이터에 액세스하기 위한 별도의 솔루션이 있어야 합니다.


<!--HONumber=42-->
