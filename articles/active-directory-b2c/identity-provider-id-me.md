---
title: ID.me 계정을 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램에서 ID.me 계정으로 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3c5df0c4112f07a465d38e789b1401132ed25931
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488808"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 ID.me 계정으로 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>필수 조건

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>ID.me 응용 프로그램 만들기

Azure Active Directory B2C (Azure AD B2C)에서 ID.me 계정을 사용 하는 사용자에 대 한 로그인을 사용 하도록 설정 하려면 [API & SDK 용 개발자 리소스 ID.me](https://developers.id.me/)에 응용 프로그램을 만들어야 합니다. 자세한 내용은 [OAuth 통합 가이드](https://developers.id.me/documentation/oauth/overview/kyc)를 참조 하세요. ID.me developer 계정이 아직 없는 경우에서 등록할 수 있습니다 [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. ID.me 계정 자격 증명을 사용 하 여 [API & SDK에 대 한 ID.me Developer 리소스](https://developers.id.me/) 에 로그인 합니다.
1. **내 응용 프로그램 보기** 를 선택 하 고 **계속** 을 선택 합니다.
1. **새로 만들기** 를 선택합니다.
    1. **이름** 및 **표시 이름** 을 입력 합니다.
    1. **리디렉션 URI** 에를 입력 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 합니다. [사용자 지정 도메인](custom-domain.md)을 사용 하는 경우을 입력 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` 합니다. `your-tenant-name`을 테 넌 트의 이름으로,를 `your-domain-name` 사용자 지정 도메인으로 바꿉니다. 
1. **Continue(계속)** 를 클릭합니다.
1. **클라이언트 ID** 및 **클라이언트 암호** 의 값을 복사 합니다. 테넌트에 ID 공급자를 추가하려면 둘 다 필요합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름** 을 입력합니다. 예들 들어 `IdMeSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
9. **키 사용** 에서 `Signature`를 선택합니다.
10. **만들기** 를 클릭합니다.

## <a name="configure-idme-as-an-identity-provider"></a>ID.me을 id 공급자로 구성

사용자가 ID.me 계정을 사용 하 여 로그인 할 수 있도록 하려면 해당 계정을 사용 하 여 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 계정을 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책 확장 파일의 **ClaimsProviders** 요소에 ID.me 계정을 추가 하 여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAuth2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.

### <a name="add-the-claims-transformations"></a>클레임 변환 추가

다음으로 displayName 클레임을 만들려면 클레임 변환이 필요 합니다. 내의 요소에 다음 클레임 변환을 추가 합니다 `<ClaimsTransformations>` `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 **ID.me** 을 선택 하 여 ID.me 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end