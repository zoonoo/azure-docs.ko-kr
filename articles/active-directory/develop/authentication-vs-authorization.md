---
title: 인증 및 권한 부여 | Microsoft
titleSuffix: Microsoft identity platform
description: V2.0 (Microsoft identity platform)에서 인증 및 권한 부여의 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: fca794d51e06c72f157dc063445d1cab09d92d28
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115866"
---
# <a name="authentication-vs-authorization"></a>인증과 권한 부여의 차이점

이 문서에서는 인증 및 권한 부여를 정의 하 고 Microsoft id 플랫폼을 사용 하 여 웹 앱, 웹 Api 또는 보호 된 웹 Api를 호출 하는 앱의 사용자를 인증 하 고 권한을 부여 하는 방법을 간략하게 설명 합니다. 익숙하지 않은 용어가 표시 되는 경우 [용어집](developer-glossary.md) 또는 기본 개념을 다루는 [Microsoft id 플랫폼 비디오](identity-videos.md) 를 사용해 보세요.

## <a name="authentication"></a>인증

**인증은** 사용자가 누구 인지를 증명 하는 과정입니다. Authentication(인증)은 종종 AuthN으로 축약됩니다. Microsoft id 플랫폼은 인증을 처리 하기 위한 [Openid connect Connect](https://openid.net/connect/) 프로토콜을 구현 합니다.

## <a name="authorization"></a>권한 부여

**권한 부여** 는 인증 된 당사자에 게 작업을 수행할 수 있는 권한을 부여 하는 행위입니다. 액세스할 수 있는 데이터와 해당 데이터로 수행할 수 있는 작업을 지정 합니다. Authorization(권한 부여)는 종종 AuthZ로 축약됩니다. Microsoft id 플랫폼은 권한 부여를 처리 하기 위한 [OAuth 2.0](https://oauth.net/2/) 프로토콜을 구현 합니다.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Microsoft id 플랫폼을 사용한 인증 및 권한 부여

여러 앱에서 사용자를 추가 하거나 제거 해야 하는 경우 높은 관리 부담을 초래 하는 앱을 만드는 대신, 앱이 중앙 집중식 id 공급자에 게 해당 책임을 위임할 수 있습니다.

Azure AD (Azure Active Directory)는 클라우드의 중앙 집중식 id 공급자입니다. 인증 및 권한 부여를 위임 하면 사용자가 특정 위치에 있어야 하는 조건부 액세스 정책, multi-factor authentication 사용, 사용자가 한 번 로그인 한 다음 동일한 중앙 디렉터리를 공유 하는 모든 웹 앱에 자동으로 로그인 하도록 하는 등의 시나리오를 사용할 수 있습니다. 이 기능을 **sso (Single Sign On)** 라고 합니다.

Microsoft id 플랫폼은 id를 서비스로 제공 하 여 응용 프로그램 개발자에 대 한 인증 및 권한 부여를 간소화 하 고, OAuth 2.0 및 Openid connect Connect와 같은 산업 표준 프로토콜을 지원 하며, 코딩을 신속 하 게 시작 하는 데 도움이 되는 다양 한 플랫폼에 대 한 오픈 소스 라이브러리를 지원 합니다. 개발자는이를 통해 모든 Microsoft id를 로그인 하는 응용 프로그램을 작성 하 고, [Microsoft Graph](https://developer.microsoft.com/graph/), 호출할 토큰, 기타 microsoft api 또는 개발자가 빌드한 api를 가져올 수 있습니다. 자세한 내용은 [Microsoft id 플랫폼의 진화](about-microsoft-identity-platform.md)를 참조 하세요.

다음은 Microsoft id 플랫폼에서 사용 되는 다양 한 프로토콜을 간단 하 게 비교한 것입니다.

* **Oauth 및 Openid connect connect**: oauth는 권한 부여에 사용 되 고 Oidc (openid connect connect)는 인증에 사용 됩니다. Openid connect Connect는 OAuth 2.0을 기반으로 구축 되므로 용어와 흐름은 둘 사이에서 비슷합니다. Openid connect Connect를 사용 하 여 사용자를 인증 하 고 권한 부여를 사용 하 여 한 요청에서 사용자가 소유한 보호 된 리소스 (OAuth 2.0 사용)에 액세스할 수도 있습니다. 자세한 내용은 [OAuth 2.0 및 Openid connect connect 프로토콜](active-directory-v2-protocols.md) 및 [openid connect connect 프로토콜](v2-protocols-oidc.md)을 참조 하세요.
* **Oauth 및 saml**: oauth가 권한 부여에 사용 되 고, saml가 인증에 사용 됩니다. 두 프로토콜을 함께 사용 하 여 사용자를 인증 하는 방법에 대 한 자세한 내용 (SAML 사용) 및 보호 된 리소스에 대 한 액세스 권한 부여 가져오기 (OAuth 2.0 사용)에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름](v2-saml-bearer-assertion.md) 을 참조 하세요.
* **Openid connect connect vs. saml**: 사용자를 인증 하는 데 사용 되 고 Single sign-on을 사용 하도록 설정 하는 데 사용 되는 openid connect CONNECT 및 saml. SAML 인증은 일반적으로 Azure AD에 페더레이션된 ADFS (Active Directory Federation Services)와 같은 id 공급자와 함께 사용 되므로 엔터프라이즈 응용 프로그램에서 자주 사용 됩니다. Openid connect 연결은 일반적으로 클라우드의 앱, 웹 사이트 및 웹 Api와 같은 전적으로 사용 되는 앱에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항에 대해 설명 하는 다른 항목:

* 인증 및 권한 부여에서 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용 하는 방법을 알아보려면 [보안 토큰](security-tokens.md) 을 참조 하세요.
* Microsoft id 플랫폼과 통합 될 수 있도록 응용 프로그램을 등록 하는 프로세스에 대해 알아보려면 [응용 프로그램 모델](application-model.md) 을 참조 하세요.
* Microsoft id 플랫폼에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md) 을 참조 하세요.

Microsoft id 플랫폼에서 구현 하는 프로토콜에 대 한 자세한 내용은 다음을 확인 하세요.

* Openid connect Connect 및 OAuth 2.0 표준에 대 한 자세한 내용은 [Microsoft id 플랫폼에서 oauth 2.0 및 Openid connect Connect 프로토콜](active-directory-v2-protocols.md) 을 참조 하세요.
* Microsoft id 플랫폼이 Single Sign-on을 지 원하는 방법에 대 한 자세한 내용은 [Single SIGN-ON SAML 프로토콜](single-sign-on-saml-protocol.md) 을 참조 하세요.
