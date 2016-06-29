<properties
	pageTitle="Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호 | Microsoft Azure"  
    description="조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."  
    services="active-directory" 
	keywords="앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/15/2016"
	ms.author="femila"/>


# Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호  
  
회사 리소스에 대한 액세스를 보호하는 것은 모든 조직에 중요합니다. 클라우드 서비스 및 모바일 장치가 개발됨에 따라 사용자가 회사 리소스에 액세스하는 방법이 크게 변경되었습니다. 따라서 회사 리소스를 보호하는 방법에 대한 전략도 변경해야 합니다.
  
## 조건부 액세스를 사용하는 이유  
 Azure Active Directory의 조건부 액세스 제어 기능은 클라우드 및 온-프레미스에서 회사 리소스를 보호하는 간단한 방법을 제공합니다. Azure Active Directory는 "도용한 암호를 사용하여 내 리소스에 액세스 방지" 또는 "엔터프라이즈 콘텐츠에 액세스하기 위해 정상적인 관리 장치 필요" 등의 요구 사항을 충족합니다.

## 조건부 액세스 제어는 어떻게 적용되나요?  
 조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 액세스 요구 사항이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 리소스에 대한 사용자 기반 액세스
  
- **사용자 특성**: 사용자 특성 수준에서 권한이 있는 사용자만 회사 리소스에 액세스할 수 있도록 하는 정책을 적용할 수 있습니다.   
- **사용자의 그룹 멤버 자격**: 그룹에 대한 멤버 자격을 기반으로 사용자에게 제공되는 액세스 수준을 제어할 수도 있습니다.   
- **MFA(다단계 인증)**: 다단계 인증 시스템을 사용하여 사용자 자신의 ID를 인증하는 정책을 적용할 수 있습니다. 예를 들어 추가 보안 계층을 위해 사용자 개인 휴대폰의 PIN을 확인할 수 있습니다. MFA 인증은 유효한 사용자의 사용자 이름과 암호에 대한 액세스 권한을 얻은 권한이 없는 사용자가 액세스하는 것으로부터 리소스를 보호합니다. 
- **로그인 및 사용자 위험**: 조건부 액세스 위험 정책은 Azure AD ID 보호에서 제공되며, 위험 이벤트와 비정상 로그인 활동을 바탕으로 고급 보호 기능을 제공합니다. 
 

## 장치 기반 조건부 액세스 

**등록된 장치**: 장치 수준에서 모바일 장치 관리(MDM)에 등록된 장치만 액세스를 허용하는 정책을 설정할 수 있습니다. Microsoft Intune을 사용하여 장치 등록과 호환성을 검사합니다. 그런 다음 장치 수준 조건부 액세스는 파일 암호화를 적용하는 등의 정책을 준수하는 장치만 액세스할 수 있도록 합니다. 또한 MDM 솔루션을 사용하여 분실 또는 도난당한 장치의 회사 데이터를 원격으로 초기화할 수 있습니다.
  

이러한 정책을 사용하여 설정할 수 있는 액세스 수준은 클라우드 또는 온-프레미스 리소스에 적용할 수 있습니다. 클라우드 리소스는 Office 365 및 타사 SaaS 앱과 같은 앱이 될 수 있습니다. 또한 클라우드에서 호스트한 LOB(기간 업무) 앱이 될 수도 있습니다.
  
## 조건부 액세스 - 콘텐츠 맵  
다음 콘텐츠 맵에는 현재 배포에서 조건부 액세스를 사용하도록 설정하는 방법을 알아보기 위해 참조해야 하는 문서가 나열되어 있습니다.


| 시나리오 | 문서 |
|------------------------------------------------------|----------|
| 인증 강도 또는 사용자에 따른 리소스 보호 |[MFA와 익스트라넷으로 Azure AD SaaS 앱 조건부 액세스 적용하기](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[지원되는 응용 프로그램](active-directory-conditional-access-supported-apps.md)|
| 분실 또는 도난당한 장치의 회사 데이터 보호 |[Microsoft Intune을 사용하여 전체 또는 선택적 초기화로 데이터 보호](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|로그인 위험을 기반으로 리소스 보호 |[Azure AD ID 보호](active-directory-identityprotection.md) |
| 추가 정보 |[조건부 액세스 FAQ](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0615_2016-->