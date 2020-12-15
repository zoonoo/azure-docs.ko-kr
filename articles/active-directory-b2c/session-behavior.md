---
title: 세션 동작 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 세션 동작을 구성 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: de0f4824cb23a37f37d3834dce67eb0b7edf0b15
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503252"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 세션 동작 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

SSO (Single sign-on)는 사용자가 Azure Active Directory B2C (Azure AD B2C)에서 응용 프로그램을 통해 로그인 할 때 보안 및 편리 함을 추가 합니다. 이 문서에서는 Azure AD B2C에서 사용 되는 Single Sign-On 방법을 설명 하 고 정책을 구성할 때 가장 적합 한 SSO 방법을 선택 하는 데 도움을 줍니다.

Single Sign-On를 통해 사용자는 단일 계정으로 한 번 로그인 하 여 여러 응용 프로그램에 액세스할 수 있습니다. 응용 프로그램은 플랫폼 또는 도메인 이름에 관계 없이 웹, 모바일 또는 단일 페이지 응용 프로그램 일 수 있습니다.

사용자가 응용 프로그램에 처음으로 로그인 하면 Azure AD B2C 쿠키 기반 세션을 유지 합니다. 후속 인증 요청 시 Azure AD B2C은 쿠키 기반 세션을 읽고 유효성을 검사 하며, 사용자에 게 다시 로그인 하 라는 메시지를 표시 하지 않고 액세스 토큰을 발급 합니다. 쿠키 기반 세션이 만료 되거나 유효 하지 않게 되 면 사용자에 게 다시 로그인 하 라는 메시지가 표시 됩니다.  

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C 세션 개요

Azure AD B2C와의 통합에는 세 가지 유형의 SSO 세션이 포함 됩니다.

- Azure AD B2C에서 관리 하는 **Azure AD B2C** 세션
- **페더레이션 id** 공급자-id 공급자가 관리 하는 세션 (예: Facebook, Salesforce 또는 Microsoft 계정)
- 웹, 모바일 또는 단일 페이지 응용 프로그램에서 관리 하는 **응용 프로그램** 세션

![SSO 세션](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C 세션 

사용자가 로컬 또는 소셜 계정으로 성공적으로 인증 하면 Azure AD B2C는 사용자의 브라우저에 쿠키 기반 세션을 저장 합니다. 쿠키는 Azure AD B2C 테 넌 트 도메인 이름 (예:)에 저장 됩니다 `https://contoso.b2clogin.com` .

사용자가 처음에 페더레이션된 계정으로 로그인 한 후 세션 시간 창 (TTL (time-to-live) 또는 TTL)이 동일한 앱 또는 다른 앱에 로그인 하는 경우 Azure AD B2C 페더레이션 id 공급자 로부터 새 액세스 토큰을 획득 하려고 시도 합니다. 페더레이션된 id 공급자 세션이 만료 되거나 잘못 된 경우 페더레이션 id 공급자는 사용자에 게 자격 증명을 입력 하 라는 메시지를 표시 합니다. 세션이 아직 활성 상태 이면 (또는 사용자가 페더레이션된 계정 대신 로컬 계정으로 로그인 한 경우) Azure AD B2C 사용자에 게 권한을 부여 하 고 추가 프롬프트를 제거 합니다.

세션 TTL 및 Azure AD B2C 정책과 응용 프로그램 간에 세션을 공유 하는 방법을 비롯 하 여 세션 동작을 구성할 수 있습니다.

### <a name="federated-identity-provider-session"></a>페더레이션된 id 공급자 세션

소셜 또는 엔터프라이즈 id 공급자는 자체 세션을 관리 합니다. 쿠키는 id 공급자의 도메인 이름 (예:)으로 저장 됩니다 `https://login.salesforce.com` . Azure AD B2C 페더레이션된 id 공급자 세션을 제어 하지 않습니다. 대신, 세션 동작은 페더레이션된 id 공급자에 의해 결정 됩니다. 

다음 시나리오를 고려하세요.

1. 사용자가 Facebook에 로그인 하 여 피드를 확인 합니다.
2. 나중에 사용자가 응용 프로그램을 열고 로그인 프로세스를 시작 합니다. 응용 프로그램은 Azure AD B2C 사용자를 리디렉션하여 로그인 프로세스를 완료 합니다.
3. Azure AD B2C 등록 또는 로그인 페이지에서 사용자는 Facebook 계정으로 로그인 하도록 선택 합니다. 사용자가 Facebook으로 리디렉션됩니다. Facebook에 활성 세션이 있는 경우 사용자에 게 자격 증명을 제공 하 라는 메시지가 표시 되지 않으며 Facebook 토큰을 사용 하 여 Azure AD B2C로 즉시 리디렉션됩니다.

### <a name="application-session"></a>응용 프로그램 세션

웹, 모바일 또는 단일 페이지 응용 프로그램은 OAuth 액세스, ID 토큰 또는 SAML 토큰으로 보호할 수 있습니다. 사용자가 앱의 보호 된 리소스에 액세스 하려고 하면 앱은 응용 프로그램 쪽에 활성 세션이 있는지 여부를 확인 합니다. 앱 세션이 없거나 세션이 만료 된 경우 앱은 사용자에 게 로그인 페이지 Azure AD B2C 하 게 됩니다.

응용 프로그램 세션은와 같은 응용 프로그램 도메인 이름으로 저장 된 쿠키 기반 세션이 될 수 있습니다 `https://contoso.com` . 모바일 응용 프로그램은 다른 방식으로 세션을 저장할 수 있지만 비슷한 방법을 사용 합니다.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Azure AD B2C 세션 동작 구성

다음을 포함 하 여 Azure AD B2C 세션 동작을 구성할 수 있습니다.

- **웹 앱 세션 수명 (분)** -인증에 성공한 후 Azure AD B2C 세션 쿠키가 사용자 브라우저에 저장 되는 시간입니다. 세션 수명은 15에서 720 분 사이의 값으로 설정할 수 있습니다.

- **웹 앱 세션 제한 시간** -세션이 세션 수명 시간 설정 또는 로그인 상태 유지 설정에 의해 확장 되는 방법을 나타냅니다.
  - **롤링** -사용자가 쿠키 기반 인증 (기본값)을 수행할 때마다 세션이 확장 됨을 나타냅니다.
  - **절대** -지정 된 기간 후 사용자가 다시 인증 해야 함을 나타냅니다.

- **Single sign-on 구성** -다음 범위를 사용 하 여 Azure AD B2C 세션을 구성할 수 있습니다.
  - **테넌트** - 이 설정은 기본값입니다. 이 설정을 사용하여 B2C 테넌트의 여러 애플리케이션 및 사용자 흐름이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 응용 프로그램에 로그인 하면 사용자는 액세스할 때 다른 사용자에 게 원활 하 게 로그인 할 수 있습니다.
  - **애플리케이션** - 이 설정을 통해 다른 애플리케이션과 독립적으로 애플리케이션에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 Contoso 잡화에 이미 로그인 했는지 여부에 관계 없이 사용자가 Contoso Pharmacy에 로그인 할 수 있도록 하려면이 설정을 사용할 수 있습니다.
  - **정책** - 이 설정을 통해 이를 사용하는 애플리케이션과 독립적으로 사용자 흐름에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인 하 고 MFA (multi-factor authentication) 단계를 완료 한 경우 사용자 흐름에 연결 된 세션이 만료 되지 않는 한 사용자에 게 여러 응용 프로그램의 더 높은 보안 부분에 대 한 액세스 권한을 부여할 수 있습니다.
  - **사용 안 함** -이 설정은 사용자가 정책을 실행할 때마다 전체 사용자 흐름을 통해 실행 되도록 합니다.
::: zone pivot="b2c-custom-policy"
- **로그인 유지** -영구 쿠키를 사용 하 여 세션 수명 시간을 연장 합니다. 사용자가 브라우저를 닫았다가 다시 열고 나면 세션이 활성 상태로 유지 됩니다. 사용자가 로그 아웃 한 경우에만 세션이 해지 됩니다. 로그인 유지 기능은 로컬 계정으로 로그인 하는 경우에만 적용 됩니다. 로그인 유지 기능이 세션 수명 보다 우선적으로 적용 됩니다. 로그인 유지 기능이 사용 하도록 설정 되어 있고 사용자가 선택 하는 경우이 기능은 세션이 만료 되는 시간을 결정 합니다. 
::: zone-end

::: zone pivot="b2c-user-flow"

세션 동작을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **웹 앱 세션 수명 (분)**, **웹 앱 세션 시간 제한**, **Single sign-on 구성** 을 구성 하 고 필요 **에 따라 로그 아웃 요청에 ID 토큰을 요구** 합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

세션 동작 및 SSO 구성을 변경하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 바로 뒤에 있어야 합니다. **UserJourneyBehavors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="enable-keep-me-signed-in-kmsi"></a>로그인 유지 사용 (KMSI)

Azure Active Directory B2C (Azure AD B2C) 디렉터리에 로컬 계정이 있는 웹 및 네이티브 응용 프로그램 사용자에 게 로그인 유지 기능을 사용 하도록 설정할 수 있습니다. 이 기능은 사용자의 사용자 이름과 암호를 다시 입력 하 라는 메시지를 표시 하지 않고 응용 프로그램으로 돌아가는 사용자에 게 액세스 권한을 부여 합니다. 사용자가 로그아웃하면 이 액세스 권한이 철회됩니다.

![로그인 상태 유지 확인란을 표시 하는 등록 로그인 페이지의 예](./media/session-behavior/keep-me-signed-in.png)

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

### <a name="configure-the-page-identifier"></a>페이지 식별자 구성

KMSI를 사용 하도록 설정 하려면 콘텐츠 정의 `DataUri` 요소를 [페이지 식별자](contentdefinitions.md#datauri) `unifiedssp` 및 [페이지 버전](page-layout.md) *1.1.0* 이상으로 설정 합니다.

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다. 이 확장 파일은 사용자 지정 정책 시작 팩에 포함 된 정책 파일 중 하나로, 필수 구성 요소, [사용자 지정 정책 시작](custom-policy-get-started.md)에서 가져와야 합니다.
1. **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 정책의 **BuildingBlocks** 요소에 **contentdefinitions** 요소를 추가 합니다.

    사용자 지정 정책은 다음 코드 조각과 같이 표시 됩니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>자체 어설션된 기술 프로필에 메타 데이터 추가

KMSI 확인란을 등록 및 로그인 페이지에 추가 하려면 `setting.enableRememberMe` 메타 데이터를 true로 설정 합니다. 확장 파일에서 SelfAsserted-LocalAccountSignin-Email 기술 프로필을 재정의 합니다.

1. ClaimsProviders 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. ClaimsProviders 요소에 다음 클레임 공급자를 추가 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 확장 파일을 저장합니다.

### <a name="configure-a-relying-party-file"></a>신뢰 당사자 파일 구성

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. `<UserJourneyBehaviors>`자식 노드가 아직 없으면 `<RelyingParty>` 노드에 추가 합니다. 바로 뒤에 위치 해야 합니다 `<DefaultUserJourney ReferenceId="User journey Id" />` (예:) `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

SessionExpiryInSeconds의 값을 짧은 기간 (1200 초)으로 설정 하는 것이 좋지만, KeepAliveInDays의 값은 다음 예제와 같이 상대적으로 긴 기간 (30 일)으로 설정할 수 있습니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>로그아웃

응용 프로그램에서 사용자를 로그 아웃 하려는 경우 응용 프로그램의 쿠키를 지우거 나 사용자를 사용 하 여 세션을 종료 하는 것 만으로는 충분 하지 않습니다. 로그 아웃 하려면 Azure AD B2C 사용자를 리디렉션해야 합니다. 그렇지 않으면 사용자가 자격 증명을 다시 입력 하지 않고도 응용 프로그램에 다시 인증할 수 있습니다.

로그 아웃 요청 시 다음을 Azure AD B2C 합니다.

1. Azure AD B2C 쿠키 기반 세션을 무효화 합니다.
::: zone pivot="b2c-user-flow"
2. 페더레이션된 id 공급자에서 로그 아웃 하려고 합니다.
::: zone-end
::: zone pivot="b2c-custom-policy"
3. 페더레이션된 id 공급자에서 로그 아웃 하려고 합니다.
   - Openid connect Connect-id 공급자의 잘 알려진 구성 끝점이 위치를 지정 하는 경우 `end_session_endpoint` 입니다.
   - OAuth2- [id 공급자 메타 데이터](oauth2-technical-profile.md#metadata) 에 위치가 포함 된 경우 `end_session_endpoint`
   - SAML- [id 공급자 메타 데이터](saml-identity-provider-technical-profile.md#metadata) 에 위치가 포함 된 경우 `SingleLogoutService`
4. 필요에 따라 다른 응용 프로그램에서 로그 아웃 합니다. 자세한 내용은 [Single sign-on](#single-sign-out) 섹션을 참조 하세요.

> [!NOTE]
> Id 공급자 기술 프로필 메타 데이터를로 설정 하 여 페더레이션 id 공급자에서 로그 아웃을 사용 하지 않도록 설정할 수 있습니다 `SingleLogoutEnabled` `false` .
::: zone-end

로그 아웃 하면 Azure AD B2C를 사용 하 여 사용자의 Single Sign-On 상태가 지워지므로 사용자의 소셜 id 공급자 세션에서 로그 아웃 하지 못할 수 있습니다. 사용자가 후속 로그인 중에 동일한 id 공급자를 선택 하는 경우 자격 증명을 입력 하지 않고 다시 인증할 수 있습니다. 사용자가 응용 프로그램에서 로그 아웃 하려는 경우 반드시 Facebook 계정에서 로그 아웃 하려는 것은 아닙니다. 그러나 로컬 계정을 사용 하는 경우 사용자의 세션이 제대로 종료 됩니다.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Single Sign-Out 

사용자를 Azure AD B2C 로그 아웃 끝점으로 리디렉션하는 경우 (OAuth2 및 SAML 프로토콜 모두) 브라우저에서 사용자의 세션을 지웁니다 Azure AD B2C. 그러나 사용자는 인증을 위해 Azure AD B2C를 사용 하는 다른 응용 프로그램에 여전히 로그인 되어 있을 수 있습니다. 이러한 응용 프로그램이 사용자에 게 동시에 로그인 할 수 있도록 하기 위해 Azure AD B2C는 `LogoutUrl` 현재 사용자가 로그인 되어 있는 모든 응용 프로그램의 등록 된에 HTTP GET 요청을 보냅니다.

애플리케이션은 사용자를 식별하는 모든 세션을 지우고 `200` 요청을 반환하여 이 요청에 응답해야 합니다. 응용 프로그램에서 single sign-on을 지원 하려면 응용 프로그램의 코드에서를 구현 해야 합니다 `LogoutUrl` . 

단일 로그 아웃을 지원 하려면 JWT 및 SAML 토큰 발급자 기술 프로필은 다음을 지정 해야 합니다.

- 프로토콜 이름 (예:) `<Protocol Name="OpenIdConnect" />`
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

::: zone-end

### <a name="secure-your-logout-redirect"></a>로그 아웃 리디렉션 보안

로그 아웃 한 후에는 `post_logout_redirect_uri` 응용 프로그램에 대해 지정 된 회신 url에 관계 없이 사용자가 매개 변수에 지정 된 URI로 리디렉션됩니다. 그러나 유효한를 `id_token_hint` 전달 하 고 **로그 아웃 요청에 ID 토큰 필요** 를 설정 하는 경우 Azure AD B2C는 `post_logout_redirect_uri` 리디렉션을 수행 하기 전에의 값이 응용 프로그램의 구성 된 리디렉션 uri 중 하 나와 일치 하는지 확인 합니다. 응용 프로그램에 대해 일치 하는 회신 URL이 구성 되지 않은 경우 오류 메시지가 표시 되 고 사용자가 리디렉션되지 않습니다. 

::: zone pivot="b2c-user-flow"

로그 아웃 요청에 ID 토큰을 요구 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **로그 아웃 요청에 ID 토큰 필요** 를 사용 하도록 설정 합니다.
1. **Azure AD B2C** 으로 돌아갑니다.
1. **앱 등록** 를 선택 하 고 응용 프로그램을 선택 합니다.
1. **인증** 을 선택합니다.
1. **로그 아웃 URL** 텍스트 상자에 사후 로그 아웃 리디렉션 URI를 입력 하 고 **저장** 을 선택 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

로그 아웃 요청에 ID 토큰을 요구 하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가 합니다. 그런 다음 **SingleSignOn** 요소의 **EnforceIdTokenHintOnLogout** 를로 설정 `true` 합니다. **UserJourneyBehaviors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

응용 프로그램 로그 아웃 URL을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 를 선택 하 고 응용 프로그램을 선택 합니다.
1. **인증** 을 선택합니다.
1. **로그 아웃 URL** 텍스트 상자에 사후 로그 아웃 리디렉션 URI를 입력 하 고 **저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 토큰을 구성](configure-tokens.md)하는 방법에 대해 알아봅니다.
