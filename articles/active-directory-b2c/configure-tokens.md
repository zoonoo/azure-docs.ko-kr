---
title: 토큰 구성 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 토큰 수명 및 호환성 설정을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a4299eff296d9795f8d256ff1236a8e8b4ad3e42
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585210"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 토큰 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 [토큰 수명 및 호환성](tokens-overview.md)을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>토큰 수명 동작

다음을 포함 하 여 토큰 수명을 구성할 수 있습니다.

- **액세스 및 id 토큰 수명 (분)** -OAuth 2.0 전달자 토큰 및 id 토큰의 수명입니다. 기본값은 60 분 (1 시간)입니다. 최솟값(포함)은 5분입니다. 최대 (포함)는 1440 분 (24 시간)입니다.
- **새로 고침 토큰 수명 (일)** -응용 프로그램에 범위가 부여 된 경우 새로 고침 토큰을 사용 하 여 새 액세스 토큰을 얻을 수 있는 최대 기간입니다 `offline_access` . 기본값은 14일입니다. 최솟값(포함)은 1일입니다. 최대 (포함) 90 일입니다.
- **새로 고침 토큰 슬라이딩 윈도우 수명** -새로 고침 토큰 슬라이딩 윈도우 유형입니다. `Bounded`**수명 기간 (일)** 의 지정으로 새로 고침 토큰을 확장할 수 있음을 나타냅니다. `No expiry` 새로 고침 토큰 슬라이딩 윈도우 수명이 만료 되지 않음을 나타냅니다.
- **수명 길이 (일)** -이 기간이 경과 하면 응용 프로그램에서 획득 한 가장 최근 새로 고침 토큰의 유효 기간에 관계 없이 사용자가 강제로 다시 인증 됩니다. 값은 **새로 고침 토큰 수명** 값 보다 크거나 같아야 합니다.

다음 다이어그램은 새로 고침 토큰 슬라이딩 윈도우 수명 동작을 보여 줍니다.

![새로 고침 토큰 수명](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> PKCE를 사용 하는 권한 부여 코드 흐름을 사용 하는 단일 페이지 응용 프로그램에는 항상 새로 고침 토큰 수명이 24 시간입니다. [브라우저에서 새로 고침 토큰의 보안 영향에 대해 자세히 알아보세요](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-token-lifetime"></a>토큰 수명 구성

::: zone pivot="b2c-user-flow"

사용자 흐름 토큰 수명을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **토큰 수명** 에서 응용 프로그램의 필요에 맞게 속성을 조정 합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

토큰 호환성의 설정을 변경 하려면 확장에서 [토큰 발급자](jwt-issuer-technical-profile.md) 기술 프로필 메타 데이터를 설정 하거나 영향을 받을 정책의 신뢰 당사자 파일을 설정 합니다. 토큰 발급자 기술 프로필은 다음 예제와 같습니다.

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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

위의 예제에서는 다음 값이 설정됩니다.

- **token_lifetime_secs** 액세스 토큰 수명 (초)입니다. 기본값은 3600 (1 시간)입니다. 최소값은 300 (5 분)입니다. 최대값은 86400 (24 시간)입니다. 
- **id_token_lifetime_secs** -id 토큰 수명 (초)입니다. 기본값은 3600 (1 시간)입니다. 최소값은 300 (5 분)입니다. 최대값은 86400 (24 시간)입니다. 
- **refresh_token_lifetime_secs** 새로 고침 토큰 수명 (초)입니다. 기본값은 120, 9600 (14 일)입니다. 최소값은 86400 (24 시간)입니다. 최대값은 7776000 (90 일)입니다. 
- **rolling_refresh_token_lifetime_secs** -토큰 슬라이딩 윈도우 수명 (초)을 새로 고칩니다. 기본값은 7776000 (90 일)입니다. 최소값은 86400 (24 시간)입니다. 최대값은 31536000 (365 일)입니다. 슬라이딩 윈도우 수명을 적용 하지 않으려면의 값을 `allow_infinite_rolling_refresh_token` 로 설정 `true` 합니다. 
- **allow_infinite_rolling_refresh_token** -새로 고침 토큰 슬라이딩 윈도우 수명이 만료 되지 않습니다. 

::: zone-end


## <a name="token-compatibility-settings"></a>토큰 호환성 설정

다음을 포함 하 여 토큰 호환성을 구성할 수 있습니다.

- **발급자 (iss) 클레임** -액세스 및 ID 토큰 발급자 형식입니다.
- **주체 (sub) 클레임** -토큰이 응용 프로그램 사용자와 같은 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. 기본적으로 주체 클레임은 디렉터리에 있는 사용자의 개체 ID로 채워집니다.
- **사용자 흐름을 나타내는 클레임** -이 클레임은 실행 된 사용자 흐름을 식별 합니다. 가능한 값은 `tfp`(기본값) 또는 `acr`입니다.

::: zone pivot="b2c-user-flow"

사용자 흐름 호환성 설정을 구성 하려면:

1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **토큰 호환성 설정** 에서 응용 프로그램의 필요에 맞게 속성을 조정 합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

토큰 호환성의 설정을 변경 하려면 확장에서 [토큰 발급자](jwt-issuer-technical-profile.md) 기술 프로필 메타 데이터를 설정 하거나 영향을 받을 정책의 신뢰 당사자 파일을 설정 합니다. 토큰 발급자 기술 프로필은 다음 예제와 같습니다.

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **발급자(iss) 클레임** - **IssuanceClaimPattern** 메타데이터 항목을 통해 발급자(iss) 클레임을 설정합니다. 적용 가능한 값은 `AuthorityAndTenantGuid` 및 `AuthorityWithTfp`입니다.
- **정책 ID를 나타내는 클레임 설정** - 이 값을 설정하기 위한 옵션은 `TFP`(보안 프레임워크 정책) 및 `ACR`(인증 컨텍스트 참조)입니다. 권장 값은 `TFP`입니다. `None` 값으로 **AuthenticationContextReferenceClaimPattern** 을 설정하고

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

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>앱에 선택적 클레임 제공

응용 프로그램 클레임은 응용 프로그램에 반환 되는 값입니다. 원하는 클레임을 포함 하도록 사용자 흐름을 업데이트 합니다.

::: zone pivot="b2c-user-flow"

1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **애플리케이션 클레임** 을 선택합니다.
1. 응용 프로그램에 다시 전송 하려는 클레임 및 특성을 선택 합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="next-steps"></a>다음 단계

[액세스 토큰을 요청하는 방법](access-tokens.md)에 대해 자세히 알아보세요.