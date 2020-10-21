---
title: 사용자 지정 정책을 사용 하 여 SSO 및 토큰 사용자 지정 관리
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e72bd04bb41537546191b8ceb320c0722bd10146
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340294"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정 관리

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 토큰, 세션 및 SSO (Single Sign-On) 구성을 관리 하는 방법에 대 한 정보를 제공 합니다.

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>JWT 토큰 수명 및 클레임 구성

토큰 수명 설정을 변경하려면 변경할 정책의 신뢰 당사자 파일에 [ClaimsProviders](claimsproviders.md) 요소를 추가합니다.  **ClaimsProviders** 요소는 [TrustFrameworkPolicy](trustframeworkpolicy.md) 요소의 자식입니다.

BasePolicy 요소와 신뢰 당사자 파일의 RelyingParty 요소 사이에 ClaimsProviders 요소를 삽입 합니다.

이 안에 토큰 수명에 영향을 주는 정보를 입력해야 합니다. XML은 다음과 비슷합니다.

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

위의 예제에서는 다음 값이 설정됩니다.

- **액세스 토큰 수명** - **token_lifetime_secs** 메타데이터 항목을 통해 액세스 토큰 수명 값이 설정됩니다. 기본값은 3600초(60분)입니다.
- **ID 토큰 수명** - **id_token_lifetime_secs** 메타데이터 항목을 통해 ID 토큰 수명 값이 설정됩니다. 기본값은 3600초(60분)입니다.
- **새로 고침 토큰 수명** - **refresh_token_lifetime_secs** 메타데이터 항목을 통해 새로 고침 토큰 수명 값이 설정됩니다. 기본값은 1209600초(14일)입니다.
- **새로 고침 토큰 슬라이딩 윈도우 수명** - 새로 고침 토큰에 대한 슬라이딩 윈도우 수명을 설정하려면 **rolling_refresh_token_lifetime_secs** 메타데이터 항목의 값을 설정합니다. 기본값은 7776000(90일)입니다. 슬라이딩 윈도우 수명을 적용하지 않으려면 이 항목을 `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`로 바꿉니다.
- **발급자(iss) 클레임** - **IssuanceClaimPattern** 메타데이터 항목을 통해 발급자(iss) 클레임을 설정합니다. 적용 가능한 값은 `AuthorityAndTenantGuid` 및 `AuthorityWithTfp`입니다.
- **정책 ID를 나타내는 클레임 설정** - 이 값을 설정하기 위한 옵션은 `TFP`(보안 프레임워크 정책) 및 `ACR`(인증 컨텍스트 참조)입니다. 권장 값은 `TFP`입니다. `None` 값으로 **AuthenticationContextReferenceClaimPattern**을 설정하고

    **ClaimsSchema** 요소에서 이 요소를 추가합니다.

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **OutputClaims** 요소에 다음 요소를 추가합니다.

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR의 경우 **AuthenticationContextReferenceClaimPattern** 항목을 제거합니다.

- **주체(sub) 클레임** - 이 옵션은 기본적으로 ObjectID입니다. 이 설정을 `Not Supported`로 전환하려면 아래 줄을

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    다음 줄로 바꿉니다.

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> PKCE를 사용 하는 권한 부여 코드 흐름을 사용 하는 단일 페이지 응용 프로그램에는 항상 새로 고침 토큰 수명이 24 시간입니다. [브라우저에서 새로 고침 토큰의 보안 영향에 대해 자세히 알아보세요](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>앱에 선택적 클레임 제공

[신뢰 당사자 정책 기술 프로필](relyingparty.md#technicalprofile) 출력 클레임은 응용 프로그램에 반환 되는 값입니다. 출력 클레임을 추가 하면 사용자가 성공적으로 경험 한 후 토큰에 클레임이 발급 되며 응용 프로그램으로 전송 됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정 하 여 원하는 클레임을 출력 클레임으로 추가 합니다.

1. 사용자 지정 정책 파일(예: SignUpOrSignin.xml)을 엽니다.
1. OutputClaims 요소를 찾습니다. 토큰에 포함 하려는 OutputClaim을 추가 합니다. 
1. 출력 클레임 특성을 설정 합니다. 

다음 예에서는 클레임을 추가 합니다 `accountBalance` . AccountBalance 클레임은 분산으로 응용 프로그램에 전송 됩니다. 

```xml
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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim 요소에는 다음 특성이 포함됩니다.

  - **ClaimTypeReferenceId** -정책 파일 또는 부모 정책 파일의 [ClaimsSchema](claimsschema.md) 섹션에 이미 정의 된 클레임 유형의 식별자입니다.
  - 및 **claimtype** -토큰의 클레임 이름을 변경할 수 있습니다. 
  - **DefaultValue** -기본값입니다. 또한 테 넌 트 ID와 같은 [클레임 확인자](claim-resolver-overview.md)에 대 한 기본값을 설정할 수 있습니다.
  - **AlwaysUseDefaultValue** -기본값을 강제로 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-overview.md)에 대해 자세히 알아보세요.
- [사용자 지정 정책에서 세션 동작을 구성](session-behavior-custom-policy.md)하는 방법에 대해 알아봅니다.
- 참조: [JwtIssuer](jwt-issuer-technical-profile.md).
