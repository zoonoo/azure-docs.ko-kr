---
title: 인증과 권한 부여의 차이점 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼의 인증 및 권한 부여의 기본 사항에 대해 알아봅니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581910"
---
# <a name="authentication-vs-authorization"></a>인증과 권한 부여의 차이점

이 문서에서는 인증 및 권한 부여를 정의합니다. 또한 Microsoft ID 플랫폼을 사용하여 웹앱, 웹 API 또는 보호된 웹 API를 호출하는 앱의 사용자를 인증하고 권한을 부여하는 방법도 간략히 설명합니다. 익숙하지 않은 용어가 나오면 [용어집](developer-glossary.md) 또는 기본 개념을 설명하는 [Microsoft ID 플랫폼 비디오](identity-videos.md)를 참조하세요.

## <a name="authentication"></a>인증

*인증* 은 사용자 자신이 누구인지 증명하는 프로세스입니다. 이 프로세스는 *AuthN* 으로 축약되는 경우가 많습니다. Microsoft ID 플랫폼은 [OpenID Connect](https://openid.net/connect/) 프로토콜을 사용하여 인증을 처리합니다.

## <a name="authorization"></a>권한 부여

*권한 부여* 란 인증된 주체에게 작업을 수행할 수 있는 사용 권한을 부여하는 작업입니다. 액세스가 허용된 데이터 및 해당 데이터로 할 수 있는 작업을 지정합니다. 권한 부여는 *AuthZ* 로 축약되는 경우가 많습니다. Microsoft ID 플랫폼은 [OAuth 2.0](https://oauth.net/2/) 프로토콜을 사용하여 권한 부여를 처리합니다.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼을 사용한 인증 및 권한 부여

각각 고유한 사용자 이름과 암호 정보를 유지 관리하는 앱을 만들면 여러 앱에서 사용자를 추가하거나 제거할 때 관리 부담이 커집니다. 대신 앱에서 해당 책임을 중앙 집중식 ID 공급자에게 위임할 수 있습니다.

Azure AD(Azure Active Directory)는 클라우드의 중앙 집중식 ID 공급자입니다. 인증 및 권한 부여를 위임하면 다음과 같은 시나리오를 사용할 수 있습니다.

- 사용자가 특정 위치에 있어야 하는 조건부 액세스 정책
- [다단계 인증](../authentication/concept-mfa-howitworks.md)(2단계 인증 또는 2FA라고도 함) 사용
- 사용자가 한 번 로그인한 다음, 동일한 중앙 디렉터리를 공유하는 모든 웹앱에 자동으로 로그인할 수 있도록 설정. 이 기능을 *SSO(Single Sign-On)* 라고 합니다.

Microsoft ID 플랫폼은 ID를 서비스로 제공하여 애플리케이션 개발자를 위한 권한 부여 및 인증을 간소화합니다. 코딩 작업을 빠르게 시작하는 데 도움이 되도록 다양한 플랫폼에 대한 업계 표준 프로토콜 및 오픈 소스 라이브러리를 지원합니다. 이를 통해 개발자는 모든 Microsoft ID에 로그인하거나, [Microsoft Graph](https://developer.microsoft.com/graph/)를 호출하기 위한 토큰을 가져오거나, Microsoft API에 액세스하거나, 개발자가 빌드한 다른 API에 액세스하는 애플리케이션을 빌드할 수 있습니다.

다음 비디오에서는 Microsoft ID 플랫폼 및 최신 기본 인증 사항에 대해 설명합니다. 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

다음은 Microsoft ID 플랫폼에서 사용하는 프로토콜을 비교한 것입니다.

* **OAuth 및 OpenID Connect**: 플랫폼에서 권한 부여에는 OAuth를 사용하고, 인증에는 OIDC(OpenID Connect)를 사용합니다. OpenID Connect는 OAuth 2.0을 기준으로 구축되었으므로 두 제품 간에 용어와 흐름은 비슷합니다. 사용자를 인증하고(OpenID Connect를 통해), 사용자가 소유하는 보호된 리소스에 액세스할 수 있는 권한을 부여받는(OAuth 2.0을 통해) 두 가지 작업을 모두 한 번의 요청으로 수행할 수도 있습니다. 자세한 내용은 [OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md) 및 [OpenID Connect 프로토콜](v2-protocols-oidc.md)을 참조하세요.
* **OAuth 및 SAML**: 플랫폼에서 권한 부여에는 OAuth 2.0을 사용하고, 인증에는 SAML을 사용합니다. 이러한 프로토콜을 함께 사용하여 사용자를 인증하고 보호된 리소스에 액세스할 수 있는 권한을 부여받는 방법에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름](./scenario-token-exchange-saml-oauth.md)을 참조하세요.
* **OpenID Connect 및 SAML**: 플랫폼에서 OpenID Connect 및 SAML을 모두 사용하여 사용자를 인증하고 Single Sign-On을 사용하도록 설정합니다. SAML 인증은 일반적으로 Azure AD에 페더레이션된 ID 공급자(예: AD FS(Active Directory Federation Services))와 함께 사용되므로 엔터프라이즈 애플리케이션에서 자주 사용됩니다. OpenID Connect는 일반적으로 모바일 웹, 웹 사이트 및 웹 API와 같이 클라우드에만 있는 앱에서 사용됩니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항을 설명하는 기타 항목:

* 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 권한 부여 및 인증에 사용하는 방법에 대해 알아보려면 [보안 토큰](security-tokens.md)을 참조하세요.
* Microsoft ID 플랫폼과 통합할 수 있도록 애플리케이션을 등록하는 프로세스에 대해 알아보려면 [애플리케이션 모델](application-model.md)을 참조하세요.