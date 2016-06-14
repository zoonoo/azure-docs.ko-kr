<properties
	pageTitle="Active Directory 인증 프로토콜 | Microsoft Azure"
	description="이 문서에서는 Azure Active Directory에서 지원하는 다양한 인증 및 권한 부여 프로토콜에 대한 개요를 제공합니다."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Active Directory 인증 프로토콜

Azure AD(Azure Active Directory)는 가장 널리 사용되는 다수의 인증 및 권한 부여 프로토콜을 지원합니다.

이 문서 집합에서는 Azure AD에서 지원되는 프로토콜 및 이 프로토콜의 구현에 대해 살펴봅니다. 샘플 요청 및 응답을 포함하고 프로토콜과 직접 통합하므로 이러한 문서는 주로 언어 독립적입니다.

- [Azure AD의 OAuth 2.0](active-directory-protocols-oauth-code.md): OAuth2.0 권한 부여 흐름 및 Azure AD에서 해당 구현을 알아봅니다.
- [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md): Azure AD에서 OpenID Connect 인증 프로토콜을 사용하는 방법을 알아봅니다.
- [SAML 프로토콜 참조](active-directory-saml-protocol-reference.md): Azure AD에서 [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) 및 [Single Sign-Out](active-directory-single-sign-out-protocol-reference.md)을 지원하는 SAML 프로토콜을 사용하는 방법에 대해 알아봅니다.


## 참조 및 문제 해결

이 문서 집합은 Azure AD 응용 프로그램 문제를 해결하고 Azure AD를 보다 자세히 이해하는 데 도움이 되는 추가 정보를 제공합니다.

- [페더레이션 메타데이터](active-directory-federation-metadata.md): Azure AD에서 생성되는 메타데이터 문서를 찾고 해석하는 방법을 알아봅니다.
- [지원되는 토큰 및 클레임 유형](active-directory-token-and-claims.md): Azure AD에서 발급되는 토큰의 다양한 클레임에 대해 알아봅니다.
- [Azure AD에서 서명 키 롤오버](active-directory-signing-key-rollover.md): Azure AD의 서명 키 롤오버 주기 및 가장 일반적인 응용 프로그램 시나리오에 대한 키를 업데이트하는 방법에 대해 알아봅니다.
- [인증 프로토콜 문제 해결](active-directory-error-handling.md): OAuth 2.0 및 Azure AD를 사용할 때 가장 일반적인 오류를 해석 및 해결하는 방법을 알아봅니다.
- [Azure AD에서 OAuth 2.0에 대한 모범 사례](active-directory-oauth-best-practices.md): Azure AD에서 OAuth 2.0을 사용하는 모범 사례와 일반적인 실수를 피하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0601_2016-->