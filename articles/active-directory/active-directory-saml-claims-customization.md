<properties
	pageTitle="Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임 사용자 지정 | Microsoft Azure"
	description="Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="asmalser"/>

#Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임 사용자 지정

현재 Azure Active Directory에서는 SAML 2.0 프로토콜을 사용하여 Single Sign-On을 지원하는 150개 이상의 응용 프로그램을 포함하여 Azure AD 응용 프로그램 갤러리에서 사전 통합된 수천 개의 응용 프로그램을 지원합니다. 사용자가 SAML을 사용하는 Azure AD를 통해 응용 프로그램을 인증하는 경우 Azure AD가 토큰을 응용 프로그램에 보내면(HTTP 302 리디렉션을 통해) 응용 프로그램이 이 토큰을 유효성 검사하고 사용자 이름 및 암호 입력에 대한 메시지를 표시하는 대신 사용자를 로그인하는 데 사용합니다. 이러한 SAML 토큰에는 "클레임"이라고 알려진 사용자에 대한 정보가 포함되어 있습니다.

ID에서 "클레임"은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. [SAML 토큰](http://en.wikipedia.org/wiki/SAML_2.0)에서 이 데이터는 일반적으로 SAML Attribute Statement에 포함되고 사용자의 고유 ID는 일반적으로 SAML Subject에 표시됩니다.

기본적으로 Azure AD는 Azure AD에 있는 사용자의 사용자 이름 값(이 값이 사용자를 고유하게 식별)과 함께 NameIdentifier 클레임이 포함된 SAML 토큰을 응용 프로그램에 발급합니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰에서 응용 프로그램에 발급된 클레임을 보거나 편집하려면 Azure 관리 포털에서 응용 프로그램 레코드를 열고 응용 프로그램 아래의 **특성** 탭을 선택합니다.

![][1]

SAML 토큰에서 발급된 클레임을 편집해야 할 수도 있는 두 가지 가능한 이유는 다음과 같습니다. •응용 프로그램이 다른 클레임 URI 또는 클레임 값의 집합이 필요하도록 작성되었습니다. •NameIdentifier 클레임이 Azure Active Directory에 저장된 사용자 이름(즉, 사용자 계정 이름)이 아닌 다른 항목이어야 하는 방식으로 응용 프로그램이 배포되었습니다.

SAML 토큰 특성 테이블의 행 중 하나에 마우스를 가져갈 때마다 오른쪽에 표시되는 연필 모양의 아이콘을 선택하여 모든 기본 클레임 값을 편집할 수 있습니다. 또한 **X** 아이콘을 사용하여 클레임(NameIdentifier 외 기타)을 제거할 수 있고 **사용자 특성 추가** 단추를 사용하여 새 클레임을 추가할 수 있습니다.

##NameIdentifier 클레임 편집

응용 프로그램이 다른 사용자 이름을 사용하여 배포된 경우의 문제를 해결하려면 NameIdentifier 클레임 옆에 있는 연필 아이콘을 클릭합니다. 이렇게 하면 다양한 옵션을 포함한 대화 상자가 제공됩니다.

![][2]

**특성 값** 메뉴에서 **user.mail**을 선택하여 NameIdentifier 클레임이 디렉터리의 사용자 메일 주소가 되도록 설정하거나 **user.onpremisessamaccountname**을 선택하여 온-프레미스 Azure AD에서 동기화된 사용자의 SAM 계정 이름으로 설정합니다.

또한 특수 ExtractMailPrefix() 함수를 사용하여 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거함으로써 사용자 이름의 첫 부분만 전달되도록 할 수 있습니다(예: joesmith@contoso.com) 대신 "joesmith").

![][3]

##클레임 추가

새 클레임을 추가할 때 값을 디렉터리에 저장된 모든 사용자 특성으로 설정할 뿐만 아니라 특성 이름(SAML 사양에 따라 URI 패턴을 엄격히 따르지 않아도 됨)도 지정할 수 있습니다.

![][4]

예를 들어 조직에서 사용자가 속한 부서를 클레임(예: 영업)으로 보내야 하는 경우 응용 프로그램에서 예상하는 모든 클레임 값을 입력한 다음 **user.department**를 값으로 선택할 수 있습니다.

지정된 사용자에 대해 선택한 특성에 대한 값이 저장되어 있지 않으면 해당 클레임이 토큰에서 발급되지 않아야 합니다.

**참고:** **user.onpremisesecurityidentifier** 및 **user.onpremisesamaccountname**은 [Azure AD Connect 도구](active-directory-aadconnect.md)를 사용하여 온-프레미스 Active Directory의 사용자 데이터를 동기화할 때만 지원됩니다.

## 관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](active-directory-saas-custom-apps.md)
- [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)
	
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png

<!---HONumber=AcomDC_0302_2016-->