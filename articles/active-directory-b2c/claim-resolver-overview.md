---
title: 사용자 지정 정책에서 확인자 클레임
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 클레임 확인자를 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c4bbd98682d964cfdf72031c7d6cb77cf42a809
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396079"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C 사용자 지정 정책의 클레임 해결 프로그램 정보

Azure Active Directory B2C(Azure AD B2C) [사용자 지정 정책의](custom-policy-overview.md) 클레임 해결 프로그램은 정책 이름, 요청 상관 ID, 사용자 인터페이스 언어 등과 같은 권한 부여 요청에 대한 컨텍스트 정보를 제공합니다.

입력 또는 출력 클레임에서 클레임 해결 프로그램을 사용하려면 [ClaimsSchema](claimsschema.md) 요소 아래에서 **ClaimType** 문자열을 정의한 후 입력 또는 출력 클레임 요소의 클레임 해결 프로그램에 **DefaultValue**를 설정합니다. Azure AD B2C는 클레임 해결 프로그램의 값을 읽고 기술 프로필의 값을 사용합니다.

다음 예에서는 이름이 `correlationId`인 클레임 유형이 `string`의 **DataType**으로 정의됩니다.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

기술 프로필에서 클레임 해결 프로그램을 클레임 유형으로 매핑합니다. Azure AD B2C는 클레임 해결 프로그램 `{Context:CorrelationId}`의 값을 클레임 `correlationId`에 입력하고 클레임을 기술 프로필에 보냅니다.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>클레임 해결 프로그램 유형

다음 섹션은 사용 가능한 클레임 해결 프로그램을 나열합니다.

### <a name="culture"></a>문화권

| 클레임 | Description | 예제 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 언어에 대한 2자 ISO 코드 | en |
| {Culture:LCID}   | 언어 코드의 LCID | 1033 |
| {Culture:RegionName} | 지역에 대한 2자 ISO 코드 | US |
| {Culture:RFC5646} | RFC5646 언어 코드 | ko-KR |

### <a name="policy"></a>정책

| 클레임 | Description | 예제 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 신뢰 당사자 정책 이름입니다. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 신뢰 당사자 정책의 테넌트 ID입니다. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 신뢰 당사자 정책의 테넌트 개체 ID입니다. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 보안 프레임워크의 테넌트 ID입니다. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 클레임 | Description | 예제 |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` 쿼리 문자열 매개 변수입니다. | 해당 없음 |
| {OIDC:ClientId} |`client_id` 쿼리 문자열 매개 변수입니다. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` 쿼리 문자열 매개 변수입니다. | facebook.com |
| {OIDC:LoginHint} |  `login_hint` 쿼리 문자열 매개 변수입니다. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`입니다. | 해당 없음 |
| {OIDC:Nonce} |`Nonce` 쿼리 문자열 매개 변수입니다. | defaultNonce |
| {OIDC:암호}| [리소스 소유자 암호 자격 증명은](ropc-custom.md) 사용자의 암호를 플로우합니다.| 암호1| 
| {OIDC:Prompt} | `prompt` 쿼리 문자열 매개 변수입니다. | 로그인 |
| {OIDC:리디렉션유리} |`redirect_uri` 쿼리 문자열 매개 변수입니다. | https://jwt.ms |
| {OIDC:Resource} |`resource` 쿼리 문자열 매개 변수입니다. | 해당 없음 |
| {OIDC:범위} |`scope` 쿼리 문자열 매개 변수입니다. | openid |
| {OIDC:사용자 이름}| [리소스 소유자 암호 자격 증명은](ropc-custom.md) 사용자의 사용자 이름을 플로우합니다.| emily@contoso.com| 

### <a name="context"></a>Context

| 클레임 | Description | 예제 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | ID 경험 프레임워크 버전(빌드 번호)입니다.  | 1.0.507.0 |
| {Context:CorrelationId} | 상관관계 ID입니다.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |날짜 시간(UTC)입니다.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |정책 배포 모드입니다.  | 프로덕션 |
| {Context:IPAddress} | 사용자 IP 주소입니다. | 11.111.111.11 |
| {컨텍스트:KMSI} | 로그인 [유지](custom-policy-keep-me-signed-in.md) 확인란이 선택되어 있는지 여부를 나타냅니다. |  true |

### <a name="non-protocol-parameters"></a>비 프로토콜 매개 변수

OIDC 또는 OAuth2 요청의 일부로 포함된 모든 매개 변수 이름은 사용자 경험에서 클레임에 매핑될 수 있습니다. 예를 들어, 애플리케이션의 요청에는 이름이 `app_session`, `loyalty_number` 또는 사용자 지정 쿼리 문자열인 쿼리 문자열 매개 변수가 포함될 수 있습니다.

| 클레임 | Description | 예제 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 쿼리 문자열 매개 변수입니다. | Hawaii |
| {OAUTH-KV:app_session} | 쿼리 문자열 매개 변수입니다. | A3C5R |
| {OAUTH-KV:loyalty_number} | 쿼리 문자열 매개 변수입니다. | 1234 |
| {OAUTH-KV:any custom query string} | 쿼리 문자열 매개 변수입니다. | 해당 없음 |

### <a name="oauth2"></a>OAuth2

| 클레임 | Description | 예제 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 액세스 토큰 | 해당 없음 |


### <a name="saml"></a>SAML

| 클레임 | Description | 예제 |
| ----- | ----------- | --------|
| {SAML:오트문맥도 클래스 참조] | SAML 요청의 `AuthnContextClassRef` 요소 값입니다. | 항아리:오아시스:이름:tc:SAML:2.0:ac:클래스:암호 보호교통 |
| {SAML:이름 ID 정책 형식} | SAML `Format` 요청의 `NameIDPolicy` 요소에서 속성입니다. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:발급자} |  SAML `Issuer` 요청의 SAML 요소 값입니다.| `https://contoso.com` |
| {SAML:만들기 허용} | SAML 요청의 `AllowCreate` `NameIDPolicy` 요소에서 속성 값입니다. | True |
| {SAML:포스오트n} | SAML 요청의 `ForceAuthN` `AuthnRequest` 요소에서 속성 값입니다. | True |
| {SAML:공급자 이름} | SAML 요청의 `ProviderName` `AuthnRequest` 요소에서 속성 값입니다.| Contoso.com |

## <a name="using-claim-resolvers"></a>클레임 확인자 사용

클레임 확인자를 다음 요소와 함께 사용할 수 있습니다.

| 항목 | 요소 | 설정 |
| ----- | ----------------------- | --------|
|Application Insights 기술 프로필 |`InputClaim` | |
|[Azure Active 디렉터리](active-directory-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[오픈 ID 연결](openid-connect-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[클레임 변환](claims-transformation-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful 공급자](restful-technical-profile.md) 기술 프로필| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) 기술 프로필| `OutputClaim`| 1, 2|
|[자체 어설션된](self-asserted-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) 기술 프로필| `OutputClaim`| 2 |

설정:
1. `IncludeClaimResolvingInClaimsHandling` 메타데이터를 로 `true`설정해야 합니다.
1. 입력 또는 출력 `AlwaysUseDefaultValue` 클레임 특성을 `true`로 설정해야 합니다.

## <a name="claim-resolvers-samples"></a>클레임 확인자 샘플

### <a name="restful-technical-profile"></a>RESTful 기술 프로필

[RESTful](restful-technical-profile.md) 기술 프로필에서 사용자 언어, 정책 이름, 범위 및 클라이언트 ID를 전송할 수 있습니다. 클레임에 따라 REST API는 사용자 지정 비즈니스 논리를 실행할 수 있으며 필요한 경우 지역화된 오류 메시지를 발생시게 됩니다.

다음 예제에서는 이 시나리오와 함께 RESTful 기술 프로필을 보여 주며 있습니다.

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>직접 로그인

클레임 해결 프로그램을 사용하여 로그인 이름을 미리 채우거나 Facebook, LinkedIn 또는 Microsoft 계정 같은 특정 소셜 ID 공급자에 직접 로그인할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md)을 참조하세요.

### <a name="dynamic-ui-customization"></a>동적 UI 사용자 지정

Azure AD B2C를 사용하면 쿼리 문자열 매개 변수를 HTML 콘텐츠 정의 끝점으로 전달하여 페이지 콘텐츠를 동적으로 렌더링할 수 있습니다. 예를 들어 이 기능을 사용하면 웹 또는 모바일 응용 프로그램에서 전달하는 사용자 지정 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지의 백그라운드 이미지를 수정할 수 있습니다. 자세한 내용은 [Dynamically configure the UI by using custom policies in Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)(Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 동적으로 UI 구성)를 참조하세요. 언어 매개 변수를 기반으로 HTML 페이지를 지역화하거나 클라이언트 ID를 기반으로 콘텐츠를 변경할 수도 있습니다.

다음 예제에서는 **campaignId라는** 쿼리 문자열 매개 변수에 의 `Hawaii`값으로 **app** 전달 합니다. **language** `en-US`

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

결과적으로 Azure AD B2C는 위의 매개 변수를 HTML 콘텐츠 페이지로 보냅니다.

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>콘텐츠 정의

[ContentDefinition에서](contentdefinitions.md) `LoadUri`클레임 확인자를 보내 사용된 매개 변수에 따라 다른 위치에서 콘텐츠를 가져올 수 있습니다.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights 기술 프로필

Azure Application Insights 및 클레임 해결 프로그램을 사용하면 사용자 동작에 대한 인사이트를 얻을 수 있습니다. Application Insights 기술 프로필에서 Azure Application Insights에 지속되는 입력 클레임을 보냅니다. 자세한 내용은 [Application Insights를 사용하여 Azure AD B2C 경험의 사용자 동작 추적](analytics-with-application-insights.md)을 참조하세요. 다음 예는 정책 ID, 상관 관계 ID, 언어 및 클라이언트 ID를 Azure Application Insights에 보냅니다.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>정당 정책 의존

신뢰할 수 있는 [파티](relyingparty.md) 정책 기술 프로필에서 테넌트 ID 또는 상관 관계 ID를 JWT 내의 통신 당사자 응용 프로그램에 보낼 수 있습니다.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
