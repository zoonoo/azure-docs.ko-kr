
<properties
	pageTitle="기술 참조: Azure AD 앱에 대한 조건부 액세스 | Microsoft Azure"
	description="조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."
    services="active-directory"
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

# 기술 참조: Azure AD 앱에 대한 조건부 액세스

## 조건부 액세스로 설정된 서비스
조건부 액세스 규칙은 다양한 Azure AD 응용 프로그램 종류에서 지원됩니다. 이 목록에는 다음이 포함됩니다.

- Azure AD 응용 프로그램 갤러리의 페더레이션 응용 프로그램
- Azure AD 응용 프로그램 갤러리의 암호 SSO 응용 프로그램
- Azure 응용 프로그램 프록시에 등록된 응용 프로그램
- Azure AD에 등록된 개발 LOB(기간 업무) 및 다중 테넌트 응용 프로그램
- Visual Studio Online
- Azure 원격 앱
- 	Dynamics CRM
- Microsoft Office 365 Yammer
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online(비즈니스용 OneDrive 포함)


## 액세스 규칙 사용

각 규칙을 응용 프로그램 단위로 사용하거나 사용하지 않도록 설정할 수 있습니다. **설정**인 경우 규칙을 사용할 수 있으며 응용 프로그램에 액세스하는 사용자에게 적용됩니다. **해제**인 경우 규칙을 사용할 수 없으며 사용자 로그인 환경에 영향을 주지 않습니다.

## 특정 사용자에게 규칙 적용
규칙은 **적용 대상**을 설정하여 보안 그룹을 기반으로 특정 사용자 집합에 적용할 수 있습니다. **모든 사용자** 또는 **그룹**을 **적용 대상**으로 설정할 수 있습니다. **모든 사용자**로 설정하면 규칙은 응용 프로그램에 액세스하는 모든 사용자에게 적용됩니다. **그룹** 옵션을 통해 특정 보안 및 배포 그룹을 선택할 수 있으며 이러한 그룹에 대해서만 규칙이 적용됩니다.

규칙을 배포하는 경우 파일럿 그룹의 멤버인 제한된 사용자 집합에 먼저 적용하는 것이 일반적입니다. 완료되면 규칙을 **모든 사용자**에 적용할 수 있습니다. 이렇게 하면 조직의 모든 사용자에게 규칙이 적용됩니다.

**제외** 옵션을 사용하여 정책에서 제외되도록 그룹을 선택할 수 있습니다. 제외 옵션으로 선택한 그룹의 모든 멤버는 포함되는 그룹에 표시되더라도 제외됩니다.

## "회사" 네트워크


"회사" 네트워크를 사용하는 조건부 액세스 규칙은 Azure AD에서 구성된 신뢰할 수 있는 IP 주소 범위를 사용하거나 AD FS의 “Corpnet 내부" 클레임을 사용합니다. 이러한 규칙에는 다음이 포함됩니다.

- 회사에 있지 않을 때 다단계 인증 필요
- 회사에 있지 않을 때 액세스 차단

“회사” 네트워크 지정 옵션

1. 신뢰할 수 있는 IP 주소 범위는 [다단계 인증 구성 페이지](../multi-factor-authentication/multi-factor-authentication-whats-next.md)에서 구성합니다. 조건부 액세스 정책은 각 인증 요청 및 토큰 발급의 구성 범위를 사용하여 규칙을 평가합니다. 
2. Corpnet 내부 클레임을 사용하여 구성합니다. 이 옵션은 AD FS를 이용하여 페더레이트된 디렉터리와 함께 사용할 수 있습니다. [Coronet 내부 클레임에 대해 자세히 알아보기](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. 공용 IP 주소 범위를 구성합니다. 구성 탭에서 디렉터리에 대한 공용 IP 주소를 설정할 수 있습니다. 조건부 액세스는 ‘회사' IP 주소에 이 주소를 사용합니다. MFA 설정 페이지에서 강제하는 IP 주소 50개 제한을 넘는 추가 범위를 구성할 수 있습니다.



## 응용 프로그램 단위 규칙
다른 서비스에 대한 액세스에 영향을 주지 않고 중요한 서비스의 보안이 유지되도록 허용하는 응용 프로그램 단위로 규칙이 구성됩니다. 응용 프로그램의 **구성** 탭에서 조건부 액세스 규칙을 구성할 수 있습니다.

현재 제공되는 규칙은 다음과 같습니다.

- **다단계 인증 필요**
 - 정책이 적용되는 모든 사용자는 다단계 인증을 한 번 이상 인증해야 합니다.
 
- **회사에 있지 않을 때 다단계 인증 필요**
 - 이 정책이 적용되는 경우 모든 사용자는 회사 외부의 원격 위치에서 서비스를 액세스하는 경우 다단계 인증을 한 번 이상 수행해야 합니다. 회사에서 원격 위치로 이동하는 경우 서비스에 액세스할 때 다단계 인증을 수행해야 합니다.
 
- **회사에 있지 않을 때 액세스 차단**
 - 사용자가 회사에서 원격 위치로 이동할 때 "회사에 있지 않을 때 액세스 차단" 정책이 적용되는 경우 차단됩니다. 회사에 있을 때에는 액세스가 다시 허용됩니다.


## 관련된 항목

- [Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호](active-directory-conditional-access.md)
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!---HONumber=AcomDC_0615_2016-->