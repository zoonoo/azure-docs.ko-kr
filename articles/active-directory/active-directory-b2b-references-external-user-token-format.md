<properties
   pageTitle="Azure Active Directory B2B 공동 작업 미리 보기에 대한 외부 사용자 토큰 형식 | Microsoft Azure"
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
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure Active Directory(Azure AD) B2B 공동 작업 미리 보기에 대한 외부 사용자 토큰 형식
표준 Azure AD 토큰에 대한 클레임은 azure.microsoft.com의 [지원 토큰 및 클레임 유형](active-directory-token-and-claims.md) 기사에서 설명됩니다.

인증된 B2B 공동 작업 외부 사용자에 대해 다른 클레임은 다음과 같습니다.<br/> - **OID:** 리소스 테넌트의 ID<br/> - **TID**: 리소스 테넌트의 테넌트 ID<br/> - **발급자**: 리소스 테넌트<br/> - **IDP**: 사용자의 홈 테넌트<br/> - **AltSecId**: 대체 보안 ID로, 사용자에게 불투명합니다.<br/>

## 관련된 문서
Azure B2B 공동 작업에 대한 다른 기사 찾아보기:

- [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [작동 방법](active-directory-b2b-how-it-works.md)
- [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
- [CSV 파일 형식 참조](active-directory-b2b-references-csv-file-format.md)
- [외부 사용자 개체 특성 변경](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [현재 미리 보기 제한 사항](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->