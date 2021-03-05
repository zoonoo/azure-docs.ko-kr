---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책의 TechnicalProfiles 요소를 지정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e3d3a41e4100e36ae2400c8076d4c5b713b899d6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174872"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

기술 프로필은 여러 종류의 당사자와 통신 하는 기본 제공 메커니즘으로 프레임 워크를 제공 합니다. 기술 프로필은 Azure AD B2C 테넌트와 통신하거나, 사용자를 만들거나, 사용자 프로필을 읽는 데 사용됩니다. 기술 프로필은 사용자와 상호 작용이 가능하도록 자체 어설션될 수 있습니다. 예를 들어, 로그인할 사용자의 자격 증명을 수집한 후 등록 페이지 또는 암호 재설정 페이지를 렌더링합니다.

## <a name="type-of-technical-profiles"></a>기술 프로필 유형

기술 프로필은 다음 유형의 시나리오를 가능하게 합니다.

- [Application Insights](../azure-monitor/app/app-insights-overview.md)에 이벤트 데이터를 [Application Insights](analytics-with-application-insights.md) 보냅니다.
- [Azure Active Directory](active-directory-technical-profile.md) - Azure Active Directory B2C 사용자 관리를 지원합니다.
- [AZURE ad Multi-Factor Authentication](multi-factor-auth-technical-profile.md) -azure ad MULTI-FACTOR AUTHENTICATION (MFA)를 사용 하 여 전화 번호를 확인 하는 기능을 제공 합니다. 
- [클레임 변환](claims-transformation-technical-profile.md) - 출력 클레임 변환을 호출하여 클레임 값을 조작하거나, 클레임의 유효성을 검사하거나, 출력 클레임 집합의 기본값을 설정합니다.
- [ID 토큰 힌트](id-token-hint.md) - `id_token_hint` JWT 토큰 서명, 발급자 이름 및 토큰 대상의 유효성을 검사 하 고 인바운드 토큰에서 클레임을 추출 합니다.
- [JWT 토큰 발급자](jwt-issuer-technical-profile.md) - 다시 신뢰 당사자 애플리케이션으로 반환되는 JWT 토큰을 내보냅니다.
- [OAuth1](oauth1-technical-profile.md) - OAuth 1.0 프로토콜 ID 공급자와 페더레이션됩니다.
- [OAuth2](oauth2-technical-profile.md) - OAuth 2.0 프로토콜 ID 공급자와 페더레이션됩니다.
- 일회용 [암호](one-time-password-technical-profile.md) -일회용 암호의 생성 및 유효성 검사를 관리 하는 기능을 제공 합니다.
- [Openid connect](openid-connect-technical-profile.md) 를 모든 openid connect connect 프로토콜 id 공급자와 연결 합니다.
- [전화 계수](phone-factor-technical-profile.md) -전화 번호 등록 및 확인을 지원 합니다.
- [RESTful provider](restful-technical-profile.md) -사용자 입력의 유효성을 검사 하거나, 사용자 데이터를 보강 하거나, lob (기간 업무) 응용 프로그램과 통합 하는 등 REST API 서비스에 대 한 호출입니다.
- Saml [id 공급자](identity-provider-generic-saml.md) -saml 프로토콜 id 공급자와 페더레이션.
- [Saml 토큰 발급자](saml-service-provider.md) -신뢰 당사자 응용 프로그램으로 다시 반환 되는 saml 토큰을 내보냅니다.
- [자체 어설션](self-asserted-technical-profile.md) - 사용자와 상호 작용합니다. 예를 들어, 로그인할 사용자의 자격 증명을 수집하고 등록 페이지 또는 암호 재설정을 렌더링합니다.
- [세션 관리](custom-policy-reference-sso.md) - 여러 유형의 세션을 처리합니다.

## <a name="technical-profile-flow"></a>기술 프로필 흐름

모든 유형의 기술 프로필은 동일한 개념을 공유합니다. 먼저 입력 클레임을 읽고 클레임 변환을 실행 합니다. 그런 다음 id 공급자, REST API 또는 Azure AD 디렉터리 서비스와 같은 구성 된 파티와 통신 합니다. 프로세스가 완료 된 후 기술 프로필은 출력 클레임을 반환 하 고 출력 클레임 변환을 실행할 수 있습니다. 다음 다이어그램은 기술 프로필에서 참조되는 변환 및 매핑이 처리되는 방법을 보여 줍니다. 클레임 변환이 실행 된 후에는 출력 클레임이 클레임 모음에 즉시 저장 됩니다. 기술 프로필은와 상호 작용 합니다.

![기술 프로필 흐름을 보여 주는 다이어그램](./media/technical-profiles/technical-profile-flow.png)

1. Sso **(Single sign-on) 세션 관리** - [sso 세션 관리](custom-policy-reference-sso.md)를 사용 하 여 기술 프로필의 세션 상태를 복원 합니다.
1. **입력 클레임 변환** -기술 프로필을 시작 하기 전에 Azure AD B2C 입력 [클레임 변환을](claimstransformations.md)실행 합니다.
1. **입력 클레임** -기술 프로필에 사용 되는 클레임 모음에서 클레임을 선택 합니다.
1. **기술 프로필 실행** - 기술 프로필이 클레임을 구성된 당사자와 교환합니다. 예를 들면 다음과 같습니다.
    - 사용자를 ID 공급자로 리디렉션하여 로그인을 완료합니다. 로그인에 성공하면 사용자가 다시 돌아가고 기술 프로필 실행이 계속됩니다.
    - 매개 변수를 InputClaims로 보내고 정보를 다시 OutputClaims로 가져오는 동안 REST API를 호출합니다.
    - 사용자 계정을 만들거나 업데이트합니다.
    - MFA 텍스트 메시지를 보내고 확인합니다.
1. **유효성 검사 기술 프로필** - [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 은 [유효성 검사 기술](validation-technical-profile.md) 프로필을 호출 하 여 사용자가 프로 파일링 한 데이터의 유효성을 검사할 수 있습니다.
1. **출력 클레임** -클레임이 클레임 모음으로 다시 반환 됩니다. 다음 오케스트레이션 단계 또는 출력 클레임 변환에서 해당 클레임을 사용할 수 있습니다.
1. **출력 클레임 변환** -기술 프로필이 완료 된 후 Azure AD B2C 출력 [클레임 변환을](claimstransformations.md)실행 합니다. 
1. Sso **(Single sign-on) 세션 관리** - [sso 세션 관리](custom-policy-reference-sso.md)를 사용 하 여 세션에 대 한 기술 프로필의 데이터를 유지 합니다.

**TechnicalProfiles** 요소는 클레임 공급자가 지원하는 기술 프로필 집합을 포함합니다. 모든 클레임 공급자에는 기술 프로필이 하나 이상 있어야 합니다. 기술 프로필은 끝점 및 클레임 공급자와 통신 하는 데 필요한 프로토콜을 결정 합니다. 하나의 클레임 공급자가 여러 개의 기술 프로필을 포함할 수 있습니다.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
|---------|---------|---------|
| Id | 예 | 기술 프로필의 고유 식별자입니다. 정책 파일의 다른 요소에서 이 식별자를 사용하여 기술 프로필을 참조할 수 있습니다. 예를 들어 **OrchestrationSteps** 및 **ValidationTechnicalProfile** 입니다. |

**TechnicalProfile** 에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 도메인 | 0:1 | 기술 프로필의 도메인 이름입니다. 예를 들어 기술 프로필이 Facebook ID 공급자를 지정하는 경우 도메인 이름은 Facebook.com입니다. |
| DisplayName | 1:1 | 기술 프로필의 표시 이름입니다. |
| 설명 | 0:1 | 기술 프로필에 대 한 설명입니다. |
| 프로토콜 | 1:1 | 다른 당사자와의 통신에 사용되는 프로토콜입니다. |
| 메타데이터 | 0:1 | 기술 프로필의 동작을 제어 하는 키/값의 컬렉션입니다. |
| InputTokenFormat | 0:1 | 입력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 또는 `SAML2`입니다. `JWT` 값은 IETF 사양을 기준으로 JSON Web Token을 나타냅니다. `SAML11` 값은 OASIS 사양을 기준으로 SAML 1.1 보안 토큰을 나타냅니다.  `SAML2` 값은 OASIS 사양을 기준으로 SAML 2.0 보안 토큰을 나타냅니다. |
| OutputTokenFormat | 0:1 | 출력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 또는 `SAML2`입니다. |
| CryptographicKeys | 0:1 | 기술 프로필에 사용되는 암호화 키 목록입니다. |
| InputClaimsTransformations | 0:1 | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| InputClaims | 0:1 | 기술 프로필에서 입력으로 사용되는 클레임 유형에 대한 이전에 정의된 참조 목록입니다. |
| PersistedClaims | 0:1 | 기술 프로필에 의해 유지 되는 클레임 형식에 대해 이전에 정의 된 참조 목록입니다. |
| DisplayClaims | 0:1 | [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에 의해 제공 되는 클레임 유형에 대 한 이전에 정의 된 참조 목록입니다. DisplayClaims 기능은 현재 **미리 보기** 상태입니다. |
| OutputClaims | 0:1 | 기술 프로필에서 출력으로 사용되는 클레임 유형에 대한 이전에 정의된 참조 목록입니다. |
| OutputClaimsTransformations | 0:1 | 클레임 공급자로부터 클레임이 수신된 후에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| ValidationTechnicalProfiles | 0:n | 기술 프로필이 유효성 검사를 위해 사용하는 다른 기술 프로필에 대한 참조 목록입니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md) 을 참조 하세요.|
| SubjectNamingInfo | 0:1 | 주체 이름이 클레임과 별도로 지정되는 토큰에서 주체 이름 생성을 제어합니다. 예를 들어 OAuth 또는 SAML입니다.  |
| IncludeInSso | 0:1 |  이 기술 프로필을 사용 하 여 세션에 대해 SSO (Single Sign-On) 동작을 적용 해야 하는지, 아니면 명시적으로 상호 작용 해야 하는지 여부입니다. 이 요소는 유효성 검사 기술 프로필 내에서 사용 되는 SelfAsserted 프로필에만 사용할 수 있습니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| IncludeClaimsFromTechnicalProfile | 0:1 | 모든 입력 및 출력 클레임을 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. 참조된 기술 프로필은 동일한 정책 파일에 정의되어야 합니다. |
| IncludeTechnicalProfile |0:1 | 모든 데이터를 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. |
| UseTechnicalProfileForSessionManagement | 0:1 | 세션 관리에 사용할 다른 기술 프로필입니다. |
|EnabledForUserJourneys| 0:1 |사용자 경험에서 기술 프로필을 실행할지 여부를 제어합니다.  |

## <a name="protocol"></a>프로토콜

**프로토콜** 은 다른 당사자와 통신 하는 데 사용할 프로토콜을 지정 합니다. **Protocol** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 이름 | 예 | 기술 프로필의 일부로 사용되는 Azure AD B2C에서 지원하는 유효한 프로토콜의 이름입니다. 가능한 값은 `OAuth1` , `OAuth2` , `SAML2` , `OpenIdConnect` , `Proprietary` 또는 `None` 입니다. |
| Handler | 예 | 프로토콜 이름이로 설정 된 경우 `Proprietary` Azure AD B2C에서 프로토콜 처리기를 확인 하는 데 사용 하는 어셈블리의 이름을 지정 합니다. |

## <a name="metadata"></a>메타데이터

**Metadata** 요소는 특정 프로토콜에 대 한 관련 구성 옵션을 포함 합니다. 지원 되는 메타 데이터 목록은 해당 [기술 프로필](#type-of-technical-profiles) 사양에 설명 되어 있습니다. **Metadata** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 항목 | 0:n | 기술 프로필과 관련된 메타데이터입니다. 기술 프로필 유형마다 다른 메타데이터 항목 집합이 있습니다. 자세한 내용은 기술 프로필 유형 섹션을 참조 하세요.  |

### <a name="item"></a>항목

**Metadata** 요소의 **Item** 요소에는 다음 특성이 포함 됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 키 | 예 | 메타데이터 키입니다. 메타 데이터 항목 목록은 각 [기술 프로필 유형을](#type-of-technical-profiles)참조 하세요. |

다음 예제에서는 [OAuth2 technical profile](oauth2-technical-profile.md#metadata)과 관련 된 메타 데이터를 사용 하는 방법을 보여 줍니다.

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

다음 예제에서는 [기술 프로필 REST API](restful-technical-profile.md#metadata)와 관련 된 메타 데이터를 사용 하는 방법을 보여 줍니다.

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>암호화 키

와 통합 하는 서비스와의 트러스트를 설정 하기 위해 Azure AD B2C는 암호 및 인증서를 [정책 키](policy-keys-overview.md)형식으로 저장 합니다. 기술 프로필을 실행 하는 동안 Azure AD B2C는 Azure AD B2C 정책 키에서 암호화 키를 검색 합니다. 그런 다음는 트러스트 설정, 암호화 또는 토큰 서명 키를 사용 합니다. 이러한 트러스트는 다음으로 구성 됩니다.

- [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)및 [SAML](identity-provider-generic-saml.md) id 공급자와 페더레이션
- [REST API services](secure-rest-api.md) 를 사용 하 여 연결 보호
- [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) 및 [SAML](saml-service-provider.md) 토큰 서명 및 암호화

**CryptographicKeys** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 키 | 1:n | 이 기술 프로필에서 사용되는 암호화 키입니다. |

### <a name="key"></a>키

**Key** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 정책 파일의 다른 요소에서 참조되는 특정 키 쌍의 고유 식별자입니다. |
| StorageReferenceId | 예 | 정책 파일의 다른 요소에서 참조되는 스토리지 키 컨테이너의 식별자입니다. |

## <a name="input-claims-transformations"></a>입력 클레임 변환

**InputClaimsTransformations** 요소에는 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 입력 클레임 변환 요소의 컬렉션이 포함 될 수 있습니다. 

클레임 변환 컬렉션에서 이전 클레임 변환의 출력 클레임은 후속 입력 클레임 변환에 대 한 입력 클레임 일 수 있습니다 .이를 통해 서로에 따라 일련의 클레임 변환을 수행할 수 있습니다.

**InputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

다음 기술 프로필은 **CreateOtherMailsFromEmail** 클레임 변환에 대 한 참조입니다. 클레임 변환은 `email` `otherMails` 데이터를 디렉터리에 유지 하기 전에 컬렉션에 클레임 값을 추가 합니다.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

**Inputclaims** 은 클레임 모음에서 클레임을 선택 하 고 기술 프로필에 사용 됩니다. 예를 들어, [자체 어설션 기술 프로필](self-asserted-technical-profile.md)은 입력 클레임을 사용하여 사용자가 제공하는 출력 클레임을 미리 채웁니다. REST API 기술 프로필은 입력 클레임을 사용하여 입력 매개 변수를 REST API 엔드포인트에 보냅니다. Azure Active Directory는 입력 클레임을 고유 식별자로 사용하여 계정을 읽거나, 업데이트하거나, 삭제합니다.

**InputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 필요한 입력 클레임 유형입니다. |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 클레임 유형의 식별자입니다. 클레임은 정책 파일 또는 부모 정책 파일의 클레임 스키마 섹션에 이미 정의 되어 있습니다. |
| DefaultValue | 예 | ClaimTypeReferenceId로 표시된 클레임이 없는 경우 결과 클레임이 기술 프로필에서 InputClaim로 사용될 수 있도록 클레임을 만드는 데 사용할 기본값입니다. |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

## <a name="display-claims"></a>클레임 표시

**DisplayClaims** 요소에는 사용자 로부터 데이터를 수집 하기 위해 화면에 표시할 클레임 목록이 포함 되어 있습니다. 표시 클레임 컬렉션에서 [클레임 형식](claimsschema.md)에 대 한 참조 나 사용자가 만든 [DisplayControl](display-controls.md) 를 포함할 수 있습니다. 

- 클레임 유형은 화면에 표시할 클레임에 대 한 참조입니다. 
  - 사용자가 특정 클레임에 대 한 값을 제공 하도록 강제 하려면 **DisplayClaim** 요소의 **필수** 특성을로 설정 `true` 합니다.
  - 표시 클레임 값을 미리 채우려면 앞에서 설명한 입력 클레임을 사용 합니다. 이 요소는 기본값을 포함할 수도 있습니다.
  - **DisplayClaims** Collection의 **ClaimType** 요소는 **userinputtype** 요소를 Azure AD B2C에서 지 원하는 사용자 입력 형식으로 설정 해야 합니다. 예를 들어 `TextBox` 또는 `DropdownSingleSelect`입니다.
- 표시 컨트롤은 특수 기능이 있고 Azure AD B2C 백 엔드 서비스와 상호 작용 하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 유효성 검사 기술 프로필을 호출 하는 페이지에서 작업을 수행할 수 있습니다. 예를 들어 전자 메일 주소, 전화 번호 또는 고객 충성도 번호를 확인 합니다.

**DisplayClaims** 의 요소 순서는 Azure AD B2C 화면에서 클레임을 렌더링 하는 순서를 지정 합니다. 

**DisplayClaims** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 필요한 입력 클레임 유형입니다. |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DisplayControlReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의 되어 있는 [표시 컨트롤](display-controls.md) 의 식별자입니다. |
| 필수 | 예 | 표시 클레임이 필요한 지 여부를 나타냅니다. |

다음 예제에서는 자체 어설션된 기술 프로필의에 표시 클레임 및 표시 컨트롤을 사용 하는 방법을 보여 줍니다.

![표시 클레임이 있는 자체 어설션된 기술 프로필](./media/technical-profiles/display-claims.png)

다음 기술 프로필에서:

- 첫 번째 표시 클레임은 `emailVerificationControl` 전자 메일 주소를 수집 하 고 확인 하는 표시 컨트롤에 대 한 참조를 만듭니다.
- 다섯 번째 표시 클레임은 `phoneVerificationControl` 전화 번호를 수집 하 고 확인 하는 표시 컨트롤에 대 한 참조를 만듭니다.
- 다른 표시 클레임은 사용자 로부터 수집 되는 ClaimTypes입니다.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>지속형 클레임

**Persistedclaims** 요소에는 [azure ad 기술 프로필](active-directory-technical-profile.md) 에 의해 유지 되어야 하는 모든 값이 포함 되어 있으며, 정책 및 azure Ad 특성 이름에서 [ClaimsSchema](claimsschema.md) 섹션에 이미 정의 된 클레임 형식 간에 가능한 매핑 정보가 포함 됩니다.

클레임의 이름은 Azure AD 특성 이름을 포함 하는 소송 **claimtype** 특성이 지정 되지 않은 경우 [azure ad 특성](user-profile-attributes.md) 의 이름입니다.

**PersistedClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 저장할 클레임 유형입니다. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 예 | 클레임이 없는 경우 클레임을 만드는 데 사용할 기본값입니다. |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

다음 예제에서는 새 로컬 계정을 만드는 **AAD UserWriteUsingLogonEmail** 기술 프로필 또는 [스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)이 다음 클레임을 유지 합니다.

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 은 기술 프로필이 완료 된 후 클레임 모음으로 다시 반환 되는 클레임의 컬렉션입니다. 다음 오케스트레이션 단계 또는 출력 클레임 변환에서 해당 클레임을 사용할 수 있습니다. **OutputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 필요한 출력 클레임 유형입니다. |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 예 | 클레임이 없는 경우 클레임을 만드는 데 사용할 기본값입니다. |
|AlwaysUseDefaultValue |예 |기본값을 강제로 사용합니다.  |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. 파트너 클레임 유형 특성을 지정 하지 않으면 지정 된 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

## <a name="output-claims-transformations"></a>출력 클레임 변환

**OutputClaimsTransformations** 요소는 **OutputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다. 출력 클레임 변환은 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 됩니다. 실행 후 출력 클레임이 다시 청구 모음에 저장됩니다. 이러한 클레임은 다음 오케스트레이션 단계에서 사용할 수 있습니다.

클레임 변환 컬렉션에서 이전 클레임 변환의 출력 클레임은 후속 입력 클레임 변환의 입력 클레임이 될 수 있으므로 서로에 따라 일련의 클레임 변환을 사용할 수 있습니다.

**OutputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

다음 기술 프로필은 디렉터리에서 클레임을 읽은 후에 계정이 사용 되는지 여부를 평가 하기 위해 AssertAccountEnabledIsTrue 클레임 변환을 참조 합니다 `accountEnabled` .    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md#validation-technical-profiles)에서 출력 클레임의 유효성을 검사 하는 데 사용 됩니다. 유효성 검사 기술 프로필은 [Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md)와 같은 모든 프로토콜의 일반적인 기술 프로필입니다. 유효성 검사 기술 프로필은 출력 클레임을 반환 하거나 오류 코드를 반환 합니다. 오류 메시지는 화면에 사용자에 게 렌더링 되어 사용자가 다시 시도할 수 있습니다.

다음 다이어그램에서는 Azure AD B2C 유효성 검사 기술 프로필을 사용 하 여 사용자 자격 증명의 유효성을 검사 하는 방법을 보여 줍니다.

![다이어그램 유효성 검사 기술 프로필 흐름](./media/technical-profiles/validation-technical-profile.png) 

**ValidationTechnicalProfiles** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용되는 기술 프로필의 식별자입니다. 참조된 기술 프로필의 모든 입력 클레임이 참조 기술 프로필의 출력 클레임에 표시되어야 합니다. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

제목 **Naminginfo** 는 [신뢰 당사자 정책의](relyingparty.md#subjectnaminginfo)토큰에 사용 되는 주체 이름을 정의 합니다. **SubjectNamingInfo** 에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimType | 예 | 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |

## <a name="include-technical-profile"></a>기술 프로필 포함

기술 프로필에는 다른 기술 프로필을 포함 하 여 설정을 변경 하거나 새 기능을 추가할 수 있습니다. **IncludeTechnicalProfile** 요소는 기술 프로필이 파생 되는 일반적인 기술 프로필에 대 한 참조입니다. 정책 요소의 중복성과 복잡성을 줄이려면 핵심 요소를 공유 하는 여러 기술 프로필을 사용 하는 경우 포함을 사용 합니다. 공통 기술 프로필을 포함 하는 특정 작업 기술 프로필과 함께 일반적인 구성 집합과 공통 기술 프로필을 사용 합니다. 

여러 시나리오에 대해 서로 다른 클레임 집합을 보내야 하는 단일 끝점을 포함 하는 [REST API 기술 프로필이](restful-technical-profile.md) 있다고 가정 합니다. REST API 끝점 URI, 메타 데이터, 인증 유형 및 암호화 키와 같은 공유 기능을 사용 하 여 일반적인 기술 프로필을 만듭니다. 일반적인 기술 프로필을 포함 하는 특정 작업 기술 프로필을 만듭니다. 그런 다음 입력 클레임을 추가 하거나 클레임을 출력 하거나 해당 기술 프로필과 관련 된 REST API 끝점 URI를 덮어씁니다.

**IncludeTechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의 된 기술 프로필의 식별자입니다. |


다음 예제에서는 포함을 사용 하는 방법을 보여 줍니다.

- *REST-일반* -기본 구성이 포함 된 일반적인 기술 프로필입니다.
- *Rest-ValidateProfile* - *Rest-API 일반* 기술 프로필을 포함 하 고 입력 및 출력 클레임을 지정 합니다.
- *Rest-UpdateProfile* -REST- *API 일반* 기술 프로필을 포함 하 고, 입력 클레임을 지정 하 고, `ServiceUrl` 메타 데이터를 덮어씁니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>다중 수준 포함 

기술 프로필에는 단일 기술 프로필이 포함 될 수 있습니다. 포함 수준의 수에는 제한이 없습니다. 예를 들어, **AAD-UserReadUsingAlternativeSecurityId-NoError** 기술 프로필에는 **AAD-UserReadUsingAlternativeSecurityId** 가 포함됩니다. 이 기술 프로필은 `RaiseErrorIfClaimsPrincipalDoesNotExist` 메타 데이터 항목을로 설정 하 `true` 고, 소셜 계정이 디렉터리에 없으면 오류를 발생 시킵니다. **UserReadUsingAlternativeSecurityId-NoError** 는이 동작을 재정의 하 고 해당 오류 메시지를 비활성화 합니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** 에는 `AAD-Common` 기술 프로필이 포함됩니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**UserReadUsingAlternativeSecurityId-NoError** 와 **UserReadUsingAlternativeSecurityId** 는 모두 **aad 일반** 기술 프로필에 지정 되어 있으므로 필수 **프로토콜** 요소를 지정 하지 않습니다.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>세션 관리에 기술 프로필 사용

[Single sign-on 세션 기술 프로필](custom-policy-reference-sso.md)에 대 한 **UseTechnicalProfileForSessionManagement** 요소 참조입니다. **UseTechnicalProfileForSessionManagement** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="enabled-for-user-journeys"></a>사용자 경험 사용

사용자 경험의 [ClaimsProviderSelections](userjourneys.md#claims-provider-selection)는 클레임 공급자 선택 옵션과 해당 순서를 정의합니다. **EnabledForUserJourneys** 요소를 사용하여 사용자에게 제공되는 클레임 공급자를 필터링합니다. **EnabledForUserJourneys** 요소에는 다음 값 중 하나가 포함됩니다.

- **Always** - 항상 기술 프로필을 실행합니다.
- **Never** - 기술 프로필을 건너뜁니다.
- **OnClaimsExistence** - 기술 프로필에 지정된 특정 클레임이 있는 경우에만 실행합니다.
- **OnItemExistenceInStringCollectionClaim** - 문자열 컬렉션 클레임에 항목이 있는 경우에만 실행합니다.
- **OnItemAbsenceInStringCollectionClaim** - 문자열 컬렉션 클레임에 항목이 없는 경우에만 실행합니다.

**OnclaimOnItemExistenceInStringCollectionClaim isto,** 또는 **OnItemAbsenceInStringCollectionClaim** 를 사용 하려면 다음 메타 데이터를 제공 해야 합니다. 

- **ClaimTypeOnWhichToEnable** -평가할 클레임의 유형을 지정 합니다.
- **ClaimValueOnWhichToEnable** -비교할 값을 지정 합니다.

다음 기술 프로필은 **identityProviders** 문자열 컬렉션에 `facebook.com` 값이 포함된 경우에만 실행됩니다.

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
