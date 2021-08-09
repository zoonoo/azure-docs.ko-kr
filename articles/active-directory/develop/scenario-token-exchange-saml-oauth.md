---
title: Azure Active Directory에서 SAML 및 OIDC/OAuth를 사용하는 Microsoft ID 플랫폼 토큰 교환 시나리오
description: Azure Active Directory에서 SAML 및 OIDC/OAuth를 사용하는 경우 일반적인 토큰 교환 시나리오에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582352"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>SAML 및 OIDC/OAuth를 사용하는 Microsoft ID 플랫폼 토큰 교환 시나리오

SAML 및 OIDC(OpenID Connect)/OAuth는 SSO(Single Sign-On)를 구현하는 데 사용되는 인기 있는 프로토콜입니다. 일부 앱은 SAML만 구현할 수 있으며 일부 앱은 OIDC/OAuth만 구현할 수 있습니다. 두 프로토콜 모두 토큰을 사용하여 비밀을 전달합니다. SAML에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](single-sign-on-saml-protocol.md)을 참조하세요. OIDC/OAuth에 대한 자세한 내용은 [Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜](active-directory-v2-protocols.md)을 참조하세요.

이 문서에서는 앱이 SAML을 구현하지만 OIDC/OAuth를 사용하는 Graph API를 호출하는 일반적인 시나리오에 대해 간략하게 설명합니다. 이 시나리오를 사용하는 사용자를 위한 기본 지침이 제공됩니다.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>시나리오: SAML 토큰이 있고 Graph API 호출하려고 합니다.
많은 앱이 SAML을 사용하여 구현됩니다. 그러나 Graph API는 OIDC/OAuth 프로토콜을 사용합니다. 간단한 것은 아니지만 SAML 앱에 OIDC/OAuth 기능을 추가하는 것이 가능합니다. 앱에서 OAuth 기능을 사용할 수 있게 되면 Graph API를 사용할 수 있습니다.

일반적인 전략은 OIDC/OAuth 스택을 앱에 추가하는 것입니다. 두 표준을 모두 구현하는 앱에서 세션 쿠키를 사용할 수 있습니다. 토큰을 명시적으로 교환하지 않습니다. 세션 쿠키를 생성하는 SAML을 사용하여 사용자를 로깅합니다. Graph API가 OAuth 흐름을 호출하는 경우 세션 쿠키를 사용하여 인증합니다. 이 전략은 조건부 액세스 검사를 통과하고 사용자에게 권한이 부여된 것으로 가정합니다.

> [!NOTE]
> OIDC/OAuth 동작을 추가하기 위해 권장되는 라이브러리는 MSAL(Microsoft 인증 라이브러리)입니다. MSAL에 대한 자세한 내용은 [MSAL(Microsoft 인증 라이브러리) 개요](msal-overview.md)를 참조하세요. 이전 라이브러리는 ADAL(Active Directory 인증 라이브러리)이라고 하며 MSAL로 대체되므로 사용하지 않는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)
