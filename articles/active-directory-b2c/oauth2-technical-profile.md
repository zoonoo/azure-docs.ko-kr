---
title: 사용자 지정 정책에서 OAuth2 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책에 OAuth2 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33bad4982d54eb18e91be28511fb9137223f4a91
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950972"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 OAuth2 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 OAuth2 프로토콜 id 공급자에 대 한 지원을 제공 합니다. OAuth2은 권한 부여 및 위임 된 인증에 대 한 기본 프로토콜입니다. 자세한 내용은 [RFC 6749 OAuth 2.0 권한 부여 프레임 워크](https://tools.ietf.org/html/rfc6749)를 참조 하세요. OAuth2 기술 프로필을 사용 하 여 OAuth2 기반 id 공급자 (예: Facebook)와 페더레이션 할 수 있습니다. Id 공급자와의 페더레이션을 통해 사용자는 기존 소셜 또는 엔터프라이즈 id로 로그인 할 수 있습니다.

## <a name="protocol"></a>Protocol (Protokoll)

**프로토콜** 요소의 **Name** 특성을 `OAuth2`으로 설정 해야 합니다. 예를 들어 **FACEBOOK OAUTH** 기술 프로필에 대 한 프로토콜은 `OAuth2`합니다.

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>입력 클레임

**Inputclaims** 및 **InputClaimsTransformations** 요소는 필요 하지 않습니다. 그러나 id 공급자에 추가 매개 변수를 보낼 수 있습니다. 다음 예에서는 `contoso.com` 값을 사용 하 여 **domain_hint** 쿼리 문자열 매개 변수를 권한 부여 요청에 추가 합니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 OAuth2 id 공급자가 반환 하는 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 id 공급자에 정의 된 이름에 매핑해야 할 수 있습니다. `DefaultValue` 특성을 설정 하는 동안에는 id 공급자가 반환 하지 않은 클레임을 포함할 수도 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **OutputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

다음 예에서는 Facebook id 공급자에서 반환 하는 클레임을 보여 줍니다.

- **First_name** 클레임은 **givenName** 클레임에 매핑됩니다.
- **Last_name** 클레임은 **성** 클레임에 매핑됩니다.
- 이름 매핑이 없는 **displayName** 클레임입니다.
- 이름 매핑이 없는 **전자 메일** 클레임입니다.

또한 기술 프로필은 id 공급자가 반환 하지 않는 클레임을 반환 합니다.

- Id 공급자의 이름을 포함 하는 **identityProvider** 클레임입니다.
- **Authenticationsource** 클레임은 **기본값을 사용 합니다.**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Id 공급자의 응용 프로그램 식별자입니다. |
| IdTokenAudience | Nem | Id_token의 대상입니다. 지정 하는 경우 Azure AD B2C는 토큰이 id 공급자에서 반환 된 클레임에 있고 지정 된 클레임과 같은지 여부를 확인 합니다. |
| authorization_endpoint | Igen | RFC 6749에 따라 권한 부여 끝점의 URL입니다. |
| AccessTokenEndpoint | Igen | RFC 6749에 따라 토큰 끝점의 URL입니다. |
| ClaimsEndpoint | Igen | RFC 6749에 따라 사용자 정보 끝점의 URL입니다. |
| AccessTokenResponseFormat | Nem | 액세스 토큰 끝점 호출의 형식입니다. 예를 들어 Facebook에는 HTTP GET 메서드가 필요 하지만 액세스 토큰 응답은 JSON 형식입니다. |
| AdditionalRequestQueryParameters | Nem | 추가 요청 쿼리 매개 변수입니다. 예를 들어 id 공급자에 추가 매개 변수를 보낼 수 있습니다. 쉼표 구분 기호를 사용 하 여 여러 매개 변수를 포함할 수 있습니다. |
| ClaimsEndpointAccessTokenName | Nem | 액세스 토큰 쿼리 문자열 매개 변수의 이름입니다. 일부 id 공급자의 클레임 끝점은 GET HTTP 요청을 지원 합니다. 이 경우 전달자 토큰은 권한 부여 헤더 대신 쿼리 문자열 매개 변수를 사용 하 여 전송 됩니다. |
| ClaimsEndpointFormatName | Nem | 형식 쿼리 문자열 매개 변수의 이름입니다. 예를 들어이 LinkedIn 클레임 끝점 `https://api.linkedin.com/v1/people/~?format=json`에서 `format` 이름으로 설정할 수 있습니다. |
| ClaimsEndpointFormat | Nem | 형식 쿼리 문자열 매개 변수의 값입니다. 예를 들어이 LinkedIn 클레임 끝점 `https://api.linkedin.com/v1/people/~?format=json`에서 `json` 값을 설정할 수 있습니다. |
| ProviderName | Nem | Id 공급자의 이름입니다. |
| response_mode | Nem | Id 공급자가 결과를 다시 Azure AD B2C 전송 하는 데 사용 하는 메서드입니다. 가능한 값은 `query`, `form_post` (기본값) 또는 `fragment`입니다. |
| scope | Nem | OAuth2 id 공급자 사양에 따라 정의 된 요청의 범위입니다. `openid`, `profile`및 `email`와 같습니다. |
| HttpBinding | Nem | 액세스 토큰 및 클레임 토큰 끝점에 대 한 예상 HTTP 바인딩입니다. 가능한 값: `GET` 또는 `POST`.  |
| ResponseErrorCodeParamName | Nem | HTTP 200 (Ok)을 통해 반환 되는 오류 메시지를 포함 하는 매개 변수의 이름입니다. |
| ExtraParamsInAccessTokenEndpointResponse | Nem | 일부 id 공급자가 **AccessTokenEndpoint** 의 응답에서 반환할 수 있는 추가 매개 변수를 포함 합니다. 예를 들어 **AccessTokenEndpoint** 의 응답에는 **ClaimsEndpoint** 요청 쿼리 문자열의 access_token 외에도 필수 매개 변수인 `openid`와 같은 추가 매개 변수가 포함 되어 있습니다. 여러 매개 변수 이름을 이스케이프 하 고 쉼표 ', ' 구분 기호로 구분 해야 합니다. |
| ExtraParamsInClaimsEndpointRequest | Nem | 일부 id 공급자가 **ClaimsEndpoint** 요청에서 반환 될 수 있는 추가 매개 변수를 포함 합니다. 여러 매개 변수 이름을 이스케이프 하 고 쉼표 ', ' 구분 기호로 구분 해야 합니다. |

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 다음 특성을 포함 합니다.

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Id 공급자 응용 프로그램의 클라이언트 암호입니다. 암호화 키는 **response_types** 메타 데이터가 `code`으로 설정 된 경우에만 필요 합니다. 이 경우 Azure AD B2C는 액세스 토큰에 대 한 권한 부여 코드를 교환 하는 다른 호출을 수행 합니다. 메타 데이터가 `id_token`로 설정 된 경우 암호화 키를 생략할 수 있습니다. |

## <a name="redirect-uri"></a>리디렉션 URI

Id 공급자의 리디렉션 URL을 구성 하는 경우 `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`를 입력 합니다. **테 넌 트** 를 테 넌 트 이름 (예: contosob2c.onmicrosoft.com)으로 바꾸고 **policyid** 를 정책 식별자 (예: b2c_1a_policy)로 바꾸어야 합니다. 리디렉션 URI는 모두 소문자 여야 합니다.

**Login.microsoftonline.com** 대신 **b2clogin.com** 도메인을 사용 하는 경우 login.microsoftonline.com 대신 b2clogin.com를 사용 해야 합니다.

Példák:

- [사용자 지정 정책을 사용 하 여 OAuth2 id 공급자로 Google + 추가](active-directory-b2c-custom-setup-goog-idp.md)













