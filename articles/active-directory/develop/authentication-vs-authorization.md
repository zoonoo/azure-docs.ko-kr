---
title: 인증 및 권한 부여 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 인증 및 권한 부여의 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581910"
---
# <a name="authentication-vs-authorization"></a>인증과 권한 부여의 차이점

이 문서에서는 인증 및 권한 부여를 정의 합니다. Microsoft id 플랫폼을 사용 하 여 웹 앱, 웹 Api 또는 보호 된 웹 Api를 호출 하는 앱의 사용자를 인증 하 고 권한을 부여 하는 방법에 대해서도 간략하게 설명 합니다. 익숙하지 않은 용어가 표시 되는 경우 [용어집](developer-glossary.md) 또는 Microsoft id 플랫폼 비디오 (기본 개념을 포함 하는 [Microsoft id 플랫폼 비디오](identity-videos.md))를 사용해 보세요.

## <a name="authentication"></a>인증

*인증은* 사용자의 신원을 증명 하는 프로세스입니다. 때로는 *인증* 으로 단축 됩니다. Microsoft id 플랫폼은 [Openid connect Connect](https://openid.net/connect/) 프로토콜을 사용 하 여 인증을 처리 합니다.

## <a name="authorization"></a>권한 부여

*권한 부여* 란 인증된 주체에게 작업을 수행할 수 있는 사용 권한을 부여하는 작업입니다. 액세스가 허용된 데이터 및 해당 데이터로 할 수 있는 작업을 지정합니다. 권한 부여는 *인증에 따라* 줄어듭니다. Microsoft id 플랫폼은 권한 부여를 처리 하는 데 [OAuth 2.0](https://oauth.net/2/) 프로토콜을 사용 합니다.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Microsoft id 플랫폼을 사용한 인증 및 권한 부여

각각 고유한 사용자 이름 및 암호 정보를 유지 관리 하는 앱을 만들면 여러 앱에서 사용자를 추가 하거나 제거 하는 경우 높은 관리 부담이 발생 합니다. 대신 앱에서 중앙 집중식 id 공급자에 게 해당 책임을 위임할 수 있습니다.

Azure AD(Azure Active Directory)는 클라우드의 중앙 집중식 ID 공급자입니다. 인증 및 권한 부여를 위임 하면 다음과 같은 시나리오를 사용할 수 있습니다.

- 사용자를 특정 위치에 배치 해야 하는 조건부 액세스 정책
- [Multi-factor](../authentication/concept-mfa-howitworks.md)authentication을 사용 하는 경우 (2 단계 인증 또는 2fa 라고도 함)
- 사용자가 한 번 로그인 한 다음 동일한 중앙 디렉터리를 공유 하는 모든 웹 앱에 자동으로 로그인 할 수 있도록 합니다. 이 기능을 *SSO (Single Sign-On)* 라고 합니다.

Microsoft id 플랫폼은 id를 서비스로 제공 하 여 응용 프로그램 개발자를 위한 권한 부여 및 인증을 간소화 합니다. 코딩을 신속 하 게 시작 하는 데 도움이 되는 다양 한 플랫폼에 대 한 업계 표준 프로토콜 및 오픈 소스 라이브러리를 지원 합니다. 개발자는이를 통해 모든 Microsoft id에 로그인 하는 응용 프로그램을 작성 하거나, [Microsoft Graph](https://developer.microsoft.com/graph/)를 호출 하는 토큰을 가져오거나, Microsoft api에 액세스 하거나, 개발자가 빌드한 다른 api에 액세스할 수 있습니다.

이 비디오에서는 Microsoft id 플랫폼과 최신 인증의 기본 사항에 대해 설명 합니다. 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Microsoft id 플랫폼에서 사용 하는 프로토콜의 비교는 다음과 같습니다.

* **Oauth 및 Openid connect connect**: 플랫폼은 인증을 위해 oidc (권한 부여 및 openid connect Connect)에 OAuth를 사용 합니다. OpenID Connect는 OAuth 2.0을 기준으로 구축되었으므로 두 제품 간에 용어와 흐름은 비슷합니다. Openid connect Connect를 통해 사용자를 인증 하 고 권한 부여를 사용 하 여 한 요청에서 사용자가 소유한 보호 된 리소스 (OAuth 2.0를 통해)에 액세스할 수도 있습니다. 자세한 내용은 [OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md) 및 [OpenID Connect 프로토콜](v2-protocols-oidc.md)을 참조하세요.
* **Oauth 및 saml**: 플랫폼은 인증에 OAuth 2.0을 사용 하 고 인증에는 saml을 사용 합니다. 이러한 프로토콜을 함께 사용 하 여 사용자를 인증 하 고 보호 된 리소스에 액세스 하기 위한 권한 부여를 가져오는 방법에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름](./scenario-token-exchange-saml-oauth.md)을 참조 하세요.
* **Openid connect connect 및 saml**: 플랫폼은 openid connect CONNECT와 saml을 모두 사용 하 여 사용자를 인증 하 고 Single Sign-On를 사용 하도록 설정 합니다. SAML 인증은 일반적으로 Azure AD에 페더레이션된 AD FS (Active Directory Federation Services)와 같은 id 공급자와 함께 사용 되므로 엔터프라이즈 응용 프로그램에서 자주 사용 됩니다. Openid connect 연결은 일반적으로 클라우드의 응용 프로그램, 웹 사이트, 웹 Api 등 전적으로 사용 되는 앱에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항을 다루는 다른 항목:

* 권한 부여 및 인증에 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용 하는 방법에 대 한 자세한 내용은 [보안 토큰](security-tokens.md)을 참조 하세요.
* Microsoft id 플랫폼과 통합 될 수 있도록 응용 프로그램을 등록 하는 프로세스에 대해 알아보려면 [응용 프로그램 모델](application-model.md)을 참조 하세요.