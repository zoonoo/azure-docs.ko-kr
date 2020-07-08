---
title: 사용자 지정 정책을 사용 하 여 세션 동작 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 세션 동작을 구성 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385266"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 세션 동작 구성

Azure Active Directory B2C (Azure AD B2C)의 [sso (Single sign-on) 세션](session-overview.md) 관리를 통해 관리자는 사용자가 이미 인증 된 후 사용자와의 상호 작용을 제어할 수 있습니다. 예를 들어 관리자는 id 공급자의 선택 여부 또는 계정 세부 정보를 다시 입력 해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

## <a name="session-behavior-properties"></a>세션 동작 속성

다음 속성을 사용하여 웹 애플리케이션 세션을 관리할 수 있습니다.

- **웹앱 세션 수명(분)** - 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
    - 기본값은 86400 초 (1440 분)입니다.
    - 최소 (포함) = 900 초 (15 분)
    - 최대 (포함) = 86400 초 (1440 분)
- **웹 앱 세션 제한 시간** - [세션 만료 유형](session-overview.md#session-expiry-type), *롤링*또는 *절대*입니다. 
- **Single sign-on 구성** -Azure AD B2C 테 넌 트의 여러 앱 및 사용자 흐름에 걸친 SSO (Single Sign-On) 동작의 [세션 범위](session-overview.md#session-scope) 입니다. 

## <a name="configure-the-properties"></a>속성 구성

세션 동작 및 SSO 구성을 변경하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 바로 뒤에 있어야 합니다. **UserJourneyBehavors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Single Sign-Out

### <a name="configure-the-applications"></a>응용 프로그램 구성

사용자를 Azure AD B2C 로그 아웃 끝점으로 리디렉션하는 경우 (OAuth2 및 SAML 프로토콜 모두) 브라우저에서 사용자의 세션을 지웁니다 Azure AD B2C.  [Single sign-on](session-overview.md#single-sign-out)을 허용 하려면 `LogoutUrl` Azure Portal에서 응용 프로그램의를 설정 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 페이지의 오른쪽 위 모서리에서 계정을 클릭 하 여 Azure AD B2C 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 하 고 **앱 등록**을 선택한 다음 응용 프로그램을 선택 합니다.
1. **설정**을 선택 하 고 **속성**을 선택한 다음 **로그 아웃 URL** 텍스트 상자를 찾습니다. 

### <a name="configure-the-token-issuer"></a>토큰 발급자 구성 

단일 로그 아웃을 지원 하려면 JWT 및 SAML 토큰 발급자 기술 프로필은 다음을 지정 해야 합니다.

- 프로토콜 이름 (예:)`<Protocol Name="OpenIdConnect" />`
- 세션 기술 프로필에 대 한 참조입니다 (예:) `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

다음 예제에서는 단일 로그 아웃으로 JWT 및 SAML 토큰 발급자를 보여 줍니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-overview.md)에 대해 자세히 알아보세요.
