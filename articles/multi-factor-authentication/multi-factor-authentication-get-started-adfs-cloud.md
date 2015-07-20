<properties 
	pageTitle="Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안 유지" 
	description="클라우드에서 Azure MFA 및 AD FS 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안 유지

조직이 Azure Active Directory와 페더레이션되어 있고 Azure AD에서 액세스되는 리소스가 있는 경우 Azure Multi-Factor Authentication 또는 Active Directory Federation Services를 사용하여 이러한 리소스를 보호할 수 있습니다. Azure Multi-Factor Authentication 또는 Active Directory Federation Services를 사용하여 Azure Active Directory 리소스를 보호하려면 아래 절차를 따르세요.

## AD FS를 사용하여 Azure AD 리소스 보안을 유지하려면 다음을 수행합니다. 



1. [다단계 인증 설정](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users)에 설명된 단계에 따라 사용자가 계정을 사용하도록 설정합니다.
2. 다음 절차에 따라 클레임 규칙을 설정합니다.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	AD FS 관리 콘솔을 시작합니다.
- 	신뢰 당사자 트러스트로 이동한 후 신뢰 당사자 트러스트를 마우스 오른쪽 단추로 클릭합니다. 클레임 규칙 편집...을 선택합니다.
- 	규칙 추가...를 클릭합니다.
- 	드롭다운에서 사용자 지정 규칙을 사용하여 클레임 보내기를 선택하고 다음을 클릭합니다.
- 	클레임 규칙의 이름을 입력합니다.
- 	사용자 지정 규칙:에 다음을 추가합니다.


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	해당 클레임:

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- 확인을 클릭합니다. Finish를 클릭합니다. AD FS 관리 콘솔을 닫습니다.

그러면 사용자는 온-프레미스 방법(예: 스마트 카드)을 사용하여 로그인을 완료할 수 있습니다.


 

<!---HONumber=July15_HO2-->