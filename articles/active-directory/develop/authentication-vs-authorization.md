---
title: 인증과 권한 부여의 차이점 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼 V2.0의 인증 및 권한 부여의 기본 사항에 대해 알아봅니다.
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
ms.openlocfilehash: 320debcf2b744e949faf7b29dd72ed334fe0feea
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503728"
---
# <a name="authentication-vs-authorization"></a>인증 및 권한 부여

이 문서에서는 인증 및 권한 부여를 정의하고 Microsoft ID 플랫폼을 사용하여 웹앱, Web API 또는 보호된 Web API를 호출하는 앱에서 사용자를 인증하고 권한을 부여하는 방법을 간략하게 설명합니다. 익숙하지 않은 용어가 나오면 [용어집](developer-glossary.md) 또는 기본 개념을 다루는 [Microsoft ID 플랫폼 비디오](identity-videos.md)를 참조하세요.

## <a name="authentication"></a>인증

**인증**은 사용자 자신이 본인임을 증명하게 하는 프로세스입니다. Authentication(인증)은 종종 AuthN으로 축약됩니다. Microsoft ID 플랫폼은 인증을 처리하기 위해 [OpenID Connect](https://openid.net/connect/) 프로토콜을 구현합니다.

## <a name="authorization"></a>권한 부여

**권한 부여**란 인증된 주체에게 작업을 수행할 수 있는 사용 권한을 부여하는 작업입니다. 액세스가 허용된 데이터 및 해당 데이터로 할 수 있는 작업을 지정합니다. Authorization(권한 부여)는 종종 AuthZ로 축약됩니다. Microsoft ID 플랫폼은 권한 부여 처리를 위한 [OAuth 2.0](https://oauth.net/2/) 프로토콜을 구현합니다.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Microsoft ID 플랫폼을 사용한 인증 및 권한 부여

여러 앱에서 사용자를 추가하거나 제거해야 하는 경우 각각 자체 사용자 이름 및 암호 정보를 유지 관리하여 높은 관리 부담을 초래하는 앱을 만드는 대신, 앱에서 중앙 집중식 ID 공급자에게 해당 책임을 위임할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Azure AD(Azure Active Directory)는 클라우드의 중앙 집중식 ID 공급자입니다. 인증 및 권한 부여를 위임하면 사용자가 특정 위치에 있어야 하는 조건부 액세스 정책, [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)(2단계 인증 또는 2FA라고도함) 사용, 사용자가 한 번 로그인한 다음, 동일한 중앙 디렉터리를 공유하는 모든 웹앱에 자동으로 로그인되도록 하는 경우와 같은 시나리오를 지원할 수 있습니다. 이 기능을 **SSO(Single Sign On)** 라고 합니다.

Microsoft ID 플랫폼은 OAuth 2.0 및 OpenID Connect와 같은 업계 표준 프로토콜은 물론, 빠르게 코딩을 시작하는 데 유용한 다양한 플랫폼용 오픈 소스 라이브러리를 지원하면서 Identity As a Service를 제공하여 애플리케이션 개발자의 권한 부여 및 인증을 간소화합니다. 이 플랫폼을 사용하면 개발자는 모든 Microsoft ID에 로그인하고, [Microsoft Graph](https://developer.microsoft.com/graph/), 기타 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다.

다음은 Microsoft ID 플랫폼에서 사용되는 다양 한 프로토콜을 간단하게 비교한 것입니다.

* **OAuth 및 OpenID Connect**: OAuth는 권한 부여에 사용되고 OIDC(OpenID Connect)는 인증에 사용됩니다. OpenID Connect는 OAuth 2.0을 기준으로 구축되었으므로 두 제품 간에 용어와 흐름은 비슷합니다. OpenID Connect를 사용하여 사용자를 인증하고, 권한을 부여 받아 사용자가 소유하는 보호된 리소스에 액세스(OAuth 2.0 사용)하는 두 가지 작업을 모두 하나의 요청으로 진행할 수도 있습니다. 자세한 내용은 [OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md) 및 [OpenID Connect 프로토콜](v2-protocols-oidc.md)을 참조하세요.
* **OAuth 및 SAML**: OAuth는 권한 부여에 사용되고 SAML을 인증에 사용됩니다. 두 프로토콜을 함께 사용하여 사용자를 인증하고(SAML 사용) 권한을 부여 받아 보호된 2.0 리소스에 액세스하는(OAuth 2.0 사용) 방법에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름](v2-saml-bearer-assertion.md)을 참조하세요.
* **OpenID Connect 및 SAML**: OpenID Connect와 SAML은 사용자를 인증하는 데 사용되고, Single Sign-On을 사용하도록 설정하는 데 사용됩니다. SAML 인증은 일반적으로 Azure AD에 페더레이션된 ADFS(Active Directory Federation Services)와 같은 ID 공급자와 함께 사용되므로 엔터프라이즈 애플리케이션에서 자주 사용됩니다. OpenID Connect는 일반적으로 모바일 웹, 웹 사이트 및 Web API와 같이 클라우드에서만 사용되는 앱에서 사용됩니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항을 설명하는 기타 항목:

* 권한 부여 및 인증에 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용하는 방법에 대한 자세한 내용은 [보안 토큰](security-tokens.md)을 참조하세요.
* Microsoft ID 플랫폼과 통합될 수 있도록 애플리케이션을 등록하는 프로세스에 대해 알아보려면 [애플리케이션 모델](application-model.md)을 참조하세요.
* Microsoft ID 플랫폼에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md)을 참조하세요.

* Microsoft ID 플랫폼에서 구현하는 프로토콜에 대한 자세한 내용은 [Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md)을 참조하세요.
* Microsoft ID 플랫폼이 Single Sign-On을 지원하는 방법에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](single-sign-on-saml-protocol.md)을 참조하세요.
* 앱에서 Single Sign-On을 구현할 수 있는 여러 가지 방법에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
