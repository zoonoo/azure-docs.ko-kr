<properties
   pageTitle="Azure Active Directory B2B 공동 작업에 대한 현재 미리 보기 제한 사항 | Microsoft Azure"
   description="Azure Active Directory B2B는 비즈니스 파트너가 회사 응용 프로그램을 선택적으로 액세스할 수 있도록 하여 사용자 회사 간 관계를 지원합니다."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Azure AD B2B 공동 작업 미리 보기: 현재 미리 보기 제한 사항

- 외부 사용자에서 지원되지 않는 Multi-Factor Authentication(MFA)입니다. 예를 들어, Contoso에 MFA가 있지만 파트너 조직에는 없는 경우, B2B 공동 작업을 통해.파트너 조직 사용자에게 MFA를 부여할 수 없습니다.
- CSV를 통해서만 초대가 가능합니다. 개별 초대 및 API 액세스는 지원되지 않습니다.
- Azure AD 전역 관리자만이 .csv 파일을 업로드할 수 있습니다.
- 소비자 전자 메일 주소로 초대하기(예: Gmail 또는 comcast.net)는 현재 지원되지 않습니다.
- 온-프레미스 응용 프로그램에 대한 외부 사용자 액세스는 테스트되지 않았습니다.
- 실제 사용자의 디렉터리에서 삭제되면 외부 사용자는 자동으로 정리되지 않습니다.
- DL에 대한 초대는 지원되지 않습니다.
- CSV를 통해 최대 2,000개의 레코드를 업로드할 수 있습니다.

## 관련된 문서
Azure B2B 공동 작업에 대한 다른 기사 찾아보기:

- [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [작동 방법](active-directory-b2b-how-it-works.md)
- [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
- [CSV 파일 형식 참조](active-directory-b2b-references-csv-file-format.md)
- [외부 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md)
- [외부 사용자 개체 특성 변경](active-directory-b2b-references-external-user-object-attribute-changes.md)

<!---HONumber=AcomDC_0204_2016-->