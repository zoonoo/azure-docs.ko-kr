---
title: eBay 계정으로 가입 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 eBay 계정으로 가입 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e2be6fd7ee6d42fe22e4735420ff2b16fa9974ba
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285325"
---
# <a name="set-up-sign-up-and-sign-in-with-an-ebay-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 eBay 계정으로 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-ebay-application"></a>eBay 애플리케이션 만들기

Azure AD B2C(Azure Active Directory B2C)에서 사용자가 eBay 계정으로 로그인할 수 있도록 설정하려면 [eBay 개발자 콘솔](https://developer.ebay.com)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [개발자 계정 만들기](https://developer.ebay.com/api-docs/static/creating-edp-account.html)를 참조하세요. 아직 eBay 개발자 계정이 없다면 [https://developer.ebay.com/signin](https://developer.ebay.com/signin?tab=register)에서 가입할 수 있습니다.

eBay 애플리케이션을 만들려면 다음 단계를 따릅니다.

1. eBay 개발자 계정 자격 증명을 사용하여 eBay 개발자 콘솔의 [애플리케이션 키](https://developer.ebay.com/my/keys)에 로그인합니다.
1. **애플리케이션 제목** 을 입력합니다.
1. **프로덕션** 아래에서 **키 집합 만들기** 를 선택합니다. 
1. **이 계정의 기본 연락처 확인** 페이지에서 계정 세부 정보를 제공합니다. 등록 프로세스를 완료하려면 **키 만들기로 계속** 을 선택합니다.
1. **앱 ID(클라이언트 ID)** 및 **앱 ID(클라이언트 ID)** 값을 복사합니다. 테넌트에 ID 공급자를 추가하려면 둘 다 필요합니다.
1. **사용자 토큰** 을 선택한 다음, **애플리케이션을 통해 eBay에서 토큰 받기** 를 선택합니다.
1. **eBay 리디렉션 URL 추가** 를 선택합니다.
    1. **개인 정보 취급 방침 URL** 에 유효한 URL을 입력합니다(예: `https://www.contoso.com/privacy`). 정책 URL은 애플리케이션에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
    1. **인증 허용 URL** 에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꾸고 `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
1. **저장** 을 선택합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `eBaySecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-ebay-as-an-identity-provider"></a>eBay를 ID 공급자로 구성

사용자가 eBay 계정을 사용하여 로그인하도록 설정하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 eBay 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <Domain>ebay.com</Domain>
        <DisplayName>eBay</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="eBay-OAUTH2">
            <DisplayName>eBay</DisplayName>
            <Protocol Name="OAuth2" />
            <Metadata>
              <Item Key="ProviderName">ebay.com</Item>
              <Item Key="authorization_endpoint">https://auth.ebay.com/oauth2/authorize</Item>
              <Item Key="AccessTokenEndpoint">https://api.ebay.com/identity/v1/oauth2/token</Item>
              <Item Key="ClaimsEndpoint">https://apiz.ebay.com/commerce/identity/v1/user/</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
              <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
              <Item Key="scope">https://api.ebay.com/oauth/api_scope/commerce.identity.readonly</Item>
              <Item Key="UsePolicyInRedirectUri">0</Item>
              <!-- Update the Client ID below to the Application ID -->
              <Item Key="client_id">Your eBay app ID</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="client_secret" StorageReferenceId="eBaySecret"/>
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="individualAccount.firstName"/>
              <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="individualAccount.lastName"/>
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
              <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="ebay.com" AlwaysUseDefaultValue="true" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            </OutputClaims>
            <OutputClaimsTransformations>
              <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
              <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
              <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="eBayExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="eBayExchange" TechnicalProfileReferenceId="eBay-OAUTH2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록](tutorial-register-applications.md)한 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **eBay** 를 선택하여 eBay 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 여기에는 Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.


## <a name="next-steps"></a>다음 단계

[Facebook 토큰을 애플리케이션에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.

::: zone-end
