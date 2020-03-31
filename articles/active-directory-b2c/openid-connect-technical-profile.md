---
title: 사용자 지정 정책에서 OpenID 연결 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 OpenID Connect 기술 프로필을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332756"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 OpenID Connect 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) 프로토콜 ID 공급자에 대한 지원을 제공합니다. OpenID Connect 1.0은 OAuth 2.0을 토대로 ID 계층을 정의하고 최신 인증 프로토콜을 나타냅니다. OpenID Connect 기술 프로필을 사용하면 Azure AD와 같은 OpenID Connect 기반 ID 공급자를 통해 페더레이트할 수 있습니다. ID 공급자와 페더레이션을 사용하면 사용자가 기존 소셜 또는 엔터프라이즈 ID로 로그인할 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `OpenIdConnect`로 설정해야 합니다. 예를 들어 **MSA-OIDC** 기술 프로필의 프로토콜은 `OpenIdConnect`입니다.

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 및 **InputClaimsTransformations** 요소는 필요하지 않습니다. 그러나 ID 공급자에게 추가 매개 변수를 보내는 것이 좋습니다. 다음 예제는 값이 `contoso.com`인 **domain_hint** 쿼리 문자열 매개 변수를 권한 부여 요청에 추가합니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소에는 OpenID Connect ID 공급자가 반환한 클레임 목록이 포함되어 있습니다. 정책에 정의된 클레임 이름을 ID 공급자에 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

다음 예제는 Microsoft 계정 ID 공급자가 반환한 클레임을 보여 줍니다.

- **issuerUserId** 클레임에 매핑되는 **하위** 클레임입니다.
- **displayName** 클레임에 매핑된 **name** 클레임입니다.
- 이름 매핑이 없는 **email**입니다.

기술 프로필은 ID 공급자가 반환하지 않은 클레임도 반환합니다.

- ID 공급자의 이름을 포함하는 **identityProvider** 클레임입니다.
- 기본값이 **socialIdpAuthentication**인 **authenticationSource** 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| client_id | yes | ID 공급자의 애플리케이션 식별자입니다. |
| IdTokenAudience | 예 | id_token의 대상 그룹입니다. 지정된 경우 Azure AD B2C는 ID 공급자가 반환하는 토큰의 `aud` 클레임이 IdTokenAudience 메타데이터에 지정된 클레임과 동일한지 여부를 확인합니다.  |
| METADATA | yes | OpenID Connect ID 공급자 구성 문서를 가리키는 URL로, OpenID 잘 알려진 구성 끝점이라고도 합니다. URL에는 `{tenant}` 테넌트 이름으로 대체되는 식이 포함될 수 있습니다.  |
| authorization_endpoint | 예 | OpenID Connect ID 공급자 구성 권한 부여 끝점을 가리키는 URL입니다. authorization_endpoint 메타데이터의 값은 OpenID `authorization_endpoint` 잘 알려진 구성 끝점에 지정된 것보다 우선합니다. URL에는 `{tenant}` 테넌트 이름으로 대체되는 식이 포함될 수 있습니다. |
| 발급자 | 예 | OpenID Connect ID 공급자의 고유 식별자입니다. 발급자 메타데이터의 값은 OpenID `issuer` 잘 알려진 구성 끝점에 지정된 값보다 우선합니다.  지정된 경우 Azure AD B2C는 ID 공급자가 반환하는 토큰의 `iss` 클레임이 발급자 메타데이터에 지정된 클레임과 동일한지 여부를 확인합니다. |
| ProviderName | 예 | ID 공급자의 이름입니다.  |
| response_types | 예 | OpenID Connect Core 1.0 사양에 따른 응답 유형입니다. 가능한 값은 `id_token`, `code` 또는 `token`입니다. |
| response_mode | 예 | ID 공급자가 결과를 다시 Azure AD B2C에 보내는 데 사용하는 방법입니다. 가능한 값은 `query`, `form_post`(기본값) 또는 `fragment`입니다. |
| scope | 예 | OpenID 연결 코어 1.0 사양에 따라 정의된 요청의 범위입니다. 예를 들어 `openid`, `profile` 및 `email`입니다. |
| HttpBinding | 예 | 액세스 토큰 및 클레임 토큰 엔드포인트에 필요한 HTTP 바인딩입니다. 가능한 값은 `GET` 또는 `POST`입니다.  |
| ValidTokenIssuerPrefixes | 예 | Azure Active Directory와 같은 다중 테넌트 ID 공급자를 사용할 때 각 테넌트에 로그인하는 데 사용할 수 있는 키입니다. |
| UsePolicyInRedirectUri | 예 | 리디렉션 URI를 구성할 때 정책을 사용할지 여부를 나타냅니다. ID 공급자에서 애플리케이션을 구성할 때 리디렉션 URI를 지정해야 합니다. 리디렉션URI는 Azure AD B2C를 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`가리킵니다.  `false`를 지정하는 경우 사용하는 각 정책에 대해 리디렉션 URI를 추가해야 합니다. 예: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp` |
| MarkAsFailureOnStatusCode5xx | 예 | HTTP 상태 코드가 5xx 범위에 있는 경우 외부 서비스에 대한 요청을 실패로 표시할지 여부를 나타냅니다. 기본값은 `false`입니다. |
| DiscoverMetadataByTokenIssuer | 예 | JWT 토큰의 발급자를 사용하여 OIDC 메타데이터를 검색할지 여부를 나타냅니다. |
| 클레임 해결내 처리 포함  | 예 | 입력 및 출력 클레임의 경우 [클레임 해결이](claim-resolver-overview.md) 기술 프로필에 포함되는지 여부를 지정합니다. 가능한 값: `true` `false`  " 또는 (기본값) . 기술 프로필에서 클레임 확인자를 사용하려면 이 것을 `true`로 설정합니다. |

### <a name="ui-elements"></a>UI 요소
 
다음 설정을 사용하여 오류가 발생할 때 표시되는 오류 메시지를 구성할 수 있습니다. 메타데이터는 OpenID Connect 기술 프로필에서 구성해야 합니다. 오류 메시지를 [지역화할](localization-string-ids.md#sign-up-or-sign-in-error-messages)수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | 예 | 제공된 사용자 이름이 있는 계정이 디렉터리에서 찾을 수 없는 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfInvalidPassword | 예 | 암호가 올바르지 않으면 사용자에게 표시할 메시지입니다. |
| UserMessageIfOldPasswordUsed| 예 |  이전 암호가 사용된 경우 사용자에게 표시할 메시지입니다.|

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| client_secret | yes | ID 공급자 애플리케이션의 클라이언트 암호입니다. 암호화 키는 **response_types** 메타데이터가 `code`로 설정된 경우에만 필요합니다. 이 경우 Azure AD B2C는 액세스 토큰에 대한 인증 코드를 교환하는 다른 호출을 수행합니다. 메타데이터가 `id_token`으로 설정된 경우 암호화 키를 생략할 수 있습니다.  |

## <a name="redirect-uri"></a>리디렉션 URI

ID 공급자의 리디렉션 URI를 구성할 때 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`를 입력합니다. 테넌트의 `{your-tenant-name}` 이름으로 바꿔야 합니다. 리디렉션 URI는 모두 소문자여야 합니다.

예제:

- [사용자 지정 정책을 사용하여 MSA(Microsoft 계정)를 ID 공급자로 추가](identity-provider-microsoft-account-custom.md)
- [Azure AD 계정을 사용하여 로그인](identity-provider-azure-ad-single-tenant-custom.md)
- [사용자 지정 정책을 사용하여 사용자가 다중 테넌트 Azure AD ID 공급자에 로그인하도록 허용](identity-provider-azure-ad-multi-tenant-custom.md)
