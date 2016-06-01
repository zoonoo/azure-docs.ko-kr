<properties
	pageTitle="Azure Active Directory ID 보호 알림 | Microsoft Azure"
	description="알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다."
	services="active-directory"
	keywords="Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="markvi"/>

#Azure Active Directory ID 보호 알림 


Azure AD ID 보호는 두 가지 유형의 자동화된 알림 전자 메일을 보내어 사용자 위험과 위험 이벤트를 관리합니다.

- 사용자 손상된 경고 전자 메일

- 주간 다이제스트 전자 메일

## 사용자 손상된 경고 전자 메일

Azure AD ID 보호가 계정이 손상되었다고 식별하는 경우 사용자 손상된 전자 메일 경고를 생성합니다. 메일은 ID 보호 대시보드에서 위험 보고서에 플래그가 지정된 사용자에 대한 링크를 포함합니다. 손상에 대한 알림을 즉시 조사하는 것이 좋습니다.


## 주간 다이제스트 전자 메일

주 단위 요약 메일은 새로운 위험 이벤트의 요약을 포함합니다.<br> 다음을 포함합니다.

- 위험에 노출된 사용자
- 의심스러운 활동
- 감지된 취약점
- ID 보호에서 관련된 보고서에 대한 링크


<br> ![재구성](./media/active-directory-identityprotection-notifications/400.png "재구성") <br>

주 단위 요약 메일 보내기를 끌 수 있습니다. <br><br> ![사용자 위험](./media/active-directory-identityprotection-notifications/62.png "사용자 위험") <br>
 

**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드에서 **설정**을 클릭합니다. <br><br> ![사용자 위험 정책](./media/active-directory-identityprotection-notifications/401.png "사용자 위험 정책") <br>

2. **일반** 섹션에서 **알림**을 클릭합니다. <br><br> ![사용자 위험 정책](./media/active-directory-identityprotection-notifications/405.png "사용자 위험 정책") <br>




## 참고 항목

- [Azure Active Directory ID 보호](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0518_2016-->