<properties
	pageTitle="Azure Active Directory 조건부 액세스 | Microsoft Azure"  
    description="조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."  
    services="active-directory" 
	keywords="앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="07/26/2016"
	ms.author="markvi"/>


# Azure Active Directory 조건부 액세스   
  
회사 리소스에 대한 액세스를 보호하는 것은 모든 조직에 중요합니다. 클라우드 서비스 및 모바일 장치가 개발됨에 따라 사용자가 회사 리소스에 액세스하는 방법이 크게 변경되었습니다. 보안에 대해 새로운 방식이 필요합니다.
  
## 조건부 액세스를 사용하는 이유  

Azure Active Directory의 조건부 액세스 제어 기능은 클라우드 및 온-프레미스에서 회사의 보안 리소스를 보호하는 간단한 방법을 제공합니다. 다단계 인증을 요구함으로써 자격 증명의 도난 및 피싱 위험으로부터 보호할 뿐만 아니라, 민감한 서비스에 액세스 권한을 부여하는 Intune 관리 장치를 요구함으로써 회사 데이터를 안전하게 보호하기 위해 조건부 액세스 정책을 사용할 수 있습니다.



## 라이선스 요구 사항

조건부 액세스는 [Azure AD Premium](http://www.microsoft.com/identity)의 기능입니다. 조건부 액세스 정책이 적용된 응용 프로그램에 액세스하는 모든 사용자는 Azure AD Premium 라이선스가 있어야 합니다. [허가되지 않은 사용자 보고서](https://aka.ms/utc5ix)를 통해 사용 현황에 대해 자세히 알아볼 수 있습니다.





## 조건부 액세스 제어는 어떻게 적용되나요?  

조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 액세스 요구 사항이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 조건
  
- **그룹 멤버 자격**: 그룹에 대한 멤버 자격을 기반으로 사용자의 액세스 수준을 제어할 수도 있습니다.

- **위치**: 사용자가 신뢰할 수 있는 네트워크에 없는 경우 MFA를 트리거하고 컨트롤을 차단하는 사용자 위치를 사용할 수 있습니다.

- **장치 플랫폼**: iOS, Android, Windows Mobile 및 Windows와 같은 장치 플랫폼 유형을 정책 적용의 조건으로서 사용할 수 있습니다.

- **장치 사용**: 장치 정책 평가 시 장치 사용/사용 안 함 상태의 유효성을 검사합니다. 디렉터리에서 분실하거나 도난 당한 장치를 사용하지 못하게 하면 정책 요구 사항을 충족하는 데 더 이상 사용할 수 없게 됩니다.

- **로그인 및 사용자 위험**: 조건부 액세스 위험 정책은 Azure AD ID 보호에서 제공되며, 위험 이벤트와 비정상 로그인 활동을 바탕으로 고급 보호 기능을 제공합니다.


## 컨트롤
   
- **MFA(다단계 인증)**: MFA를 사용하는 강력한 인증을 요구할 수 있습니다. ADFS를 사용하여 Azure MFA 또는 온-프레미스 MFA 공급자가 MFA를 제공할 수 있습니다. MFA 인증은 유효한 사용자의 사용자 이름과 암호에 대한 액세스 권한을 얻은 권한이 없는 사용자가 액세스하는 것으로부터 리소스를 보호합니다.

- **차단**: 사용자 위치와 같은 조건 하에서 액세스를 적용할 수 있습니다. 예를 들어, 사용자가 신뢰할 수 있는 네트워크에 있지 않은 경우 액세스를 차단합니다.

- **등록/호환 장치**: 장치 수준에서 모바일 장치 관리(MDM)에 등록 및 호환 장치만 액세스를 허용하는 정책을 설정할 수 있습니다. Microsoft Intune을 사용하여 장치 등록과 호환성을 검사합니다. 그런 다음 장치 수준 조건부 액세스는 MDM 정책을 준수하는 장치만 액세스할 수 있도록 합니다.
 

## 응용 프로그램

- 이러한 정책을 사용하여 설정할 수 있는 액세스 수준은 클라우드 또는 온-프레미스의 응용 프로그램 및 서비스에 적용할 수 있습니다. 정책은 웹 사이트 또는 서비스에 직접 적용됩니다. 그런 다음 브라우저 액세스뿐만 아니라 서비스에 액세스하는 응용 프로그램에 대해 정책이 적용됩니다. 적용할 수 있는 서비스 정책 목록은 여기에서 찾을 수 있습니다.


  
## 조건부 액세스 - 콘텐츠 맵  
다음 콘텐츠 맵에는 현재 배포에서 조건부 액세스를 사용하도록 설정하는 방법을 알아보기 위해 참조해야 하는 문서가 나열되어 있습니다.


### MFA 및 위치 정책

- [그룹, 위치 및 MFA 정책에 따라 Azure AD 연결 앱에 조건부 액세스 시작하기](active-directory-conditional-access-azuread-connected-apps.md)
- [지원되는 응용 프로그램 종류](active-directory-conditional-access-supported-apps.md)


### 장치 정책

[Microsoft Intune을 요구하는 데이터 보호 지원](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### 로그인 위험을 기반으로 리소스 보호

[Azure AD ID 보호](active-directory-identityprotection.md)

### 추가 정보

- [조건부 액세스 FAQ](active-directory-conditional-faqs.md)
- [기술 참조](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->