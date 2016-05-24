<properties
   pageTitle="Azure Active Directory B2B 공동 작업 미리 보기에 대한 외부 사용자 개체 특성 변경 | Microsoft Azure"
   description="Azure Active Directory B2B는 비즈니스 파트너가 회사 응용 프로그램을 선택적으로 액세스할 수 있도록 하여 사용자 회사 간 관계를 지원합니다."
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B 공동 작업 미리 보기: 외부 사용자 개체 특성 변경

Azure AD 디렉터리의 각 사용자는 사용자 개체로 표시됩니다. Azure AD의 사용자 개체는 B2B 공동 작업 초대-사용 흐름의 여러 단계에서 특성이 변경됩니다. 디렉터리의 파트너 사용자를 나타내는 사용자 개체에는 파트너 사용자가 초대 전자 메일의 링크를 클릭하면 사용 시간을 변경하는 특성이 있습니다. 구체적으로 살펴보면 다음과 같습니다.

- **SignInName** 및 **AltSecId** 특성이 채워집니다.
- **DisplayName** 특성은 사용자 계정 이름(user\_fabrikam.com#EXT#@contoso.com)에서 로그인 이름(user@fabrikam.com)으로 변경됩니다.

Azure AD에서 이러한 특성을 추적하면 파트너 사용자가 자신의 B2B 공동 작업 초대를 사용할 지 여부와 상관 없이 문제를 해결하는 데 도움이 됩니다.

## 관련 문서
Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

- [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [작동 방법](active-directory-b2b-how-it-works.md)
- [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
- [CSV 파일 형식 참조](active-directory-b2b-references-csv-file-format.md)
- [외부 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md)
- [현재 미리 보기 제한 사항](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->