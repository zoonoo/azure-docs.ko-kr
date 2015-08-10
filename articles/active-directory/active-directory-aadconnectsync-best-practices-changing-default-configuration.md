<properties
	pageTitle="기본 구성 변경에 대한 모범 사례"
	description="Azure AD Connect 동기화의 기본 구성 변경에 대한 모범 사례를 제공합니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 동기화: 기본 구성 변경에 대한 모범 사례

Azure AD Connect에 의해 만들어진 구성은 온-프레미스 Active Directory와 Azure AD를 동기화하는 환경의 대부분을 위해 “있는 그대로” 작동합니다.<br> 그러나 일부 경우에는, 특정 필요 또는 요구 사항을 충족시키기 위해 구성에 일부 변경 내용을 적용할 필요가 있습니다.

Azure AD 구성에 변경 내용을 적용하기 위해 지원되는 동안, Azure AD가 어플라이언스에 최대한 가깝게 있도록 주의를 기울여 적용해야 합니다.

다음은 예상되는 동작의 목록입니다.

- Azure AD Connect를 최신 버전으로 업그레이드한 후에는 대부분의 설정이 기본값으로 다시 설정됩니다.
- 업그레이드를 적용한 후에는 "기본" 동기화 규칙 변경 사항이 손실됩니다.
- 삭제된 "기본" 동기화 규칙은 최신 버전으로 업그레이드하는 동안 다시 만들어집니다.
- 직접 만든 사용자 지정 동기화 규칙은 최신 버전으로 업그레이드가 적용된 후에도 수정 없이 그대로 남아 있습니다.



기본 구성을 변경해야 하는 경우 다음을 수행하세요.

- "기본" 동기화 규칙의 특성 흐름을 수정해야 하는 경우 이를 변경하지 않습니다. 대신, 더 높은 우선 순위(숫자 값이 더 낮음)를 사용하여 필수 특성 흐름이 포함된 새 동기화 규칙을 만듭니다.
- 동기화 규칙 편집기를 사용하여 사용자 지정 동기화 규칙을 내보냅니다. 이렇게 하면 재해 복구 시나리오의 경우에 쉽게 다시 만드는 데 사용할 수 있는 PowerShell 스크립트를 얻게 됩니다.
- "기본" 동기화 규칙에서 범위 또는 조인 설정을 변경해야 하는 경우 이를 문서화하고 최신 버전의 Azure AD 동기화로 업그레이드한 후에 변경 사항을 다시 적용합니다.



**기타 중요 사항:**

- 특성 기반 필터링 및 암호 동기화를 구성한 경우 Azure AD로 동기화되는 개체만 암호 동기화의 범위에 있는지 확인합니다. 





## 추가 리소스

* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->