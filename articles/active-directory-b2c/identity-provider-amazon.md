---
title: Amazon 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5880b6f44caec053aef292960cecbf64f25c6743
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448577"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Amazon developer console에서 앱 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Amazon 계정이 있는 사용자에 대 한 로그인을 사용 하도록 설정 하려면 [Amazon Developer 서비스 및 기술](https://developer.amazon.com)에서 응용 프로그램을 만들어야 합니다. 자세한 내용은 [Amazon를 사용 하 여 로그인 등록](https://developer.amazon.com/docs/login-with-amazon/register-web.html)을 참조 하세요. Amazon 계정이 아직 없는 경우에서 등록할 수 있습니다 [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> 아래의 **8 단계** 에서 다음 url을 사용 하 여를 `your-tenant-name` 테 넌 트의 이름으로 바꿉니다. 테 넌 트 이름을 입력 하는 경우 Azure AD B2C에 대 문자가 대문자로 정의 된 경우에도 모든 소문자를 사용 합니다.
> - **허용 되는 원본** 에 대해 다음을 입력 합니다.`https://your-tenant-name.b2clogin.com` 
> - **허용 되는 반환 url** 에 대해 다음을 입력 합니다.`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon을 id 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **Id 공급자** 를 선택한 다음 **Amazon** 를 선택 합니다.
1. **이름** 을 입력합니다. 예: *Amazon*.
1. **클라이언트 id** 에 대해 이전에 만든 Amazon 응용 프로그램의 클라이언트 id를 입력 합니다.
1. **클라이언트 암호** 에 대해 기록한 클라이언트 암호를 입력 합니다.
1. **저장** 을 선택합니다.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>사용자 흐름에 Amazon id 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Amazon id 공급자를 추가 하려는 사용자 흐름을 클릭 합니다.
1. **소셜 id 공급자** 에서 **Amazon** 를 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 Amazon 계정으로 **amazon** to sign-on을 선택 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름** 을 입력합니다. 예들 들어 `AmazonSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
9. **키 사용** 에서 `Signature`를 선택합니다.
10. **만들기** 를 클릭합니다.

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon을 id 공급자로 구성

사용자가 Amazon 계정을 사용 하 여 로그인 할 수 있게 하려면 계정을 클레임 공급자로 정의 해야 합니다. 이 Azure AD B2C 끝점을 통해와 통신할 수 있습니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Amazon 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.


1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 Amazon 계정으로 **amazon** to sign-on을 선택 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end
