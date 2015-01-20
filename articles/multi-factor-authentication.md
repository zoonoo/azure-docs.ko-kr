<properties urlDisplayName="" pageTitle="Azure Multi-Factor Authentication 정의" metaKeywords=""  metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="billmath" />





<h1 id="whatiswamfa">Azure Multi-Factor Authentication 정의</h1>

다단계 또는 2단계 인증은 두 개 이상의 검증 방법을 사용해야 하며 사용자 로그인 및 트랜잭션에 중요한 제2의 보안 계층을 추가하는 인증 방법입니다. 이러한 인증에서는 다음 중 두 가지 이상의 검증 방법을 요구합니다. 

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 장치(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 장치)
* 사용자의 신원 정보(생체 인식)

다단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 신뢰할 수 있는 장치가 없다면 어찌할 수 없습니다. 한편 사용자가 장치를 분실한 경우에도 습득한 사람이 암호를 모르면 장치를 사용할 수 없습니다.
Azure Multi-Factor Authentication은 모바일 앱, 전화 통화 또는 문자 메시지를 사용하여 사용자에게도 로그인을 검증하도록 요구하는 다단계 인증 서비스입니다. Azure Active Directory와 함께 사용할 수 있으며, 이를 통해 Azure Multi-Factor Authentication 서버에서나 SDK를 사용하는 사용자 지정 응용 프로그램 및 디렉터리에서 온-프레미스 리소스를 보호합니다. 

![Azure Multi-Factor Authentication](./media/multi-factor-authentication/whatismfa.png)

<h3>클라우드 Azure Active Directory 보안</h3>

Azure AD ID에 Multi-Factor Authentication을 사용하도록 설정하면 사용자가 다음에 로그인할 때 추가 유효성 검사를 설정하라는 메시지가 표시됩니다. Azure, Microsoft Online Services(예: Office 365 및 Dynamics CRM Online) 및 추가 설정 없이 Azure AD를 통합하는 타사 클라우드 서비스에 안전하게 액세스할 수 있도록 Multi-Factor Authentication을 사용합니다. 다단계 인증은 다수의 전역 사용자 및 응용 프로그램에 대해 사용하도록 신속하게 설정할 수 있습니다.  [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn249466.aspx)

<h3>온-프레미스 리소스 및 Active Directory 보안</h3>

Azure Multi-Factor Authentication 서버를 사용하여 IIS 및 Active Directory와 같은 온-프레미스 리소스에 대해 Multi-Factor Authentication을 사용하도록 설정합니다.  Azure Multi-Factor Authentication 서버를 통해 관리자는 Microsoft IIS 웹 응용 프로그램을 보호하는 IIS 인증, RADIUS 인증, LDAP 인증 및 Windows 인증과 통합할 수 있습니다.  [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn249467.aspx)
<h3>사용자 지정 응용 프로그램 보안</h3>

SDK를 사용하면 클라우드 서비스와 직접 통합할 수 있습니다. 활성 인증 전화 통화 및 문자 메시지 유효성 검사를 응용 프로그램의 로그인 또는 트랜잭션 프로세스에 구축하여 응용 프로그램의 기존 사용자 데이터베이스를 활용합니다. [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn249464.aspx)

<h3>Office 365용 Multi-Factor Authentication</h3>

Azure Multi-Factor Authentication에서 구동되는 Office 365용 Multi-Factor Authentication은 Office 365 응용 프로그램과 배타적으로 호환되며 Office 365 포털에서 관리됩니다. 따라서 이제 관리자는 다단계 인증을 사용하여 Office 365 리소스의 보안을 유지할 수 있습니다. [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn383636.aspx)

<h3>Azure 관리자를 위한 Multi-Factor Authentication</h3>

모든 Azure 관리자는 Office 365에 적용되는 것과 동일한 Multi-Factor Authentication 기능을 무료로 사용할 수 있습니다. Azure 구독의 모든 관리 계정은 이제 이 핵심 다단계 인증 기능을 사용하여 추가의 보호 기능을 얻을 수 있습니다. 따라서 VM 및 웹 사이트 만들기, 저장소나 모바일 서비스 또는 다른 Azure 서비스 관리를 위해 Azure 포털에 액세스하려는 관리자는 자신의 관리자 계정에 다단계 인증을 추가할 수 있습니다.  [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn249471.aspx)

<h3>Multi-Factor Authentication 기능 비교</h3>

아래 표에서는 사용 가능한 다단계 인증 버전과 버전별로 제공되는 기능을 요약해서 보여 줍니다. 이 정보를 참조하여 적합한 다단계 인증 버전을 결정할 수 있습니다. [자세한 정보](http://msdn.microsoft.com/ko-kr/library/azure/dn249471.aspx)

![Azure Multi-Factor Authentication Feature Comparison](./media/multi-factor-authentication/mfacomparison2.png)


**추가 리소스**

* [조직으로 Azure 등록](/ko-kr/manage/services/identity/organizational-account/)
* [Azure ID](/ko-kr/manage/windows/fundamentals/identity/)
* [Azure Multi-Factor Authentication 라이브러리](http://technet.microsoft.com/ko-kr/library/dn249471.aspx)


<!--HONumber=35.2-->
