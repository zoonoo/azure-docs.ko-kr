---
title: GitHub 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 GitHub 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e2c82858b9f4b9846ff5371da347be942eed0a4f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488859"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 GitHub 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth 애플리케이션 만들기

Azure Active Directory B2C (Azure AD B2C)에서 GitHub 계정으로 로그인 할 수 있도록 하려면 [Github 개발자](https://github.com/settings/developers) 포털에서 응용 프로그램을 만들어야 합니다. 자세한 내용은 [OAuth 앱 만들기](https://docs.github.com/en/free-pro-team@latest/developers/apps/creating-an-oauth-app)를 참조 하세요. 아직 GitHub 계정이 없는 경우에는에서 등록할 수 있습니다 [https://www.github.com/](https://www.github.com/) .

1. Github 자격 증명을 사용 하 여 [Github 개발자](https://github.com/settings/developers) 에 게 로그인 합니다.
1. **OAuth 앱**, **새 OAuth 앱** 을 차례로 선택합니다.
1. **애플리케이션 이름** 및 **홈페이지 URL** 을 입력합니다.
1. **권한 부여 콜백 URL**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용 하는 경우을 입력 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` 합니다. 을 `your-domain-name` 사용자 지정 도메인으로,를 `your-tenant-name` 테 넌 트의 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용합니다.
1. **Register application(응용 프로그램 등록)** 을 클릭합니다.
1. **클라이언트 ID** 및 **클라이언트 암호** 의 값을 복사 합니다. 테넌트에 ID 공급자를 추가하려면 둘 다 필요합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-github-as-an-identity-provider"></a>GitHub를 id 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **Id 공급자** 를 선택한 다음 **GitHub (미리 보기)** 를 선택 합니다.
1. **이름** 을 입력합니다. 예: *GitHub*.
1. **클라이언트 id** 에 대해 이전에 만든 GitHub 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호** 에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장** 을 선택합니다.

## <a name="add-github-identity-provider-to-a-user-flow"></a>사용자 흐름에 GitHub id 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. GitHub id 공급자를 추가 하려는 사용자 흐름을 클릭 합니다.
1. **소셜 id 공급자** 아래에서 **GitHub** 를 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지 **에서 github를 선택 하** 여 github 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `GitHubSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-github-as-an-identity-provider"></a>GitHub를 id 공급자로 구성

사용자가 GitHub 계정을 사용 하 여 로그인 할 수 있도록 하려면 해당 계정을 사용 하 여 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 계정을 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책 확장 파일의 **ClaimsProviders** 요소에 GitHub 계정을 추가 하 여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAuth2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
1. 파일을 저장합니다.

### <a name="add-the-claims-transformations"></a>클레임 변환 추가

GitHub 기술 프로필을 사용 하려면 **CreateIssuerUserId** 클레임 변환이 ClaimsTransformations 목록에 추가 되어야 합니다. **ClaimsTransformations** 요소가 파일에 정의 되어 있지 않은 경우 아래와 같이 부모 XML 요소를 추가 합니다. 클레임 변환에는 **numericUserId** 라는 새로운 클레임 유형도 정의 해야 합니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 numericUserId 클레임을 추가 합니다.
1. [ClaimsTransformations](claimstransformations.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. CreateIssuerUserId 클레임 변환을 **ClaimsTransformations** 요소에 추가 합니다.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지 **에서 github를 선택 하** 여 github 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end
