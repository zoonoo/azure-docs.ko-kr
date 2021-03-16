---
title: Twitter 계정을 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Twitter 계정으로 등록 및 로그인을 제공합니다.
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
ms.openlocfilehash: 397fff3fafad52b3b989049096fb2d3e1fa2cba1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488519"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Twitter 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>애플리케이션 만들기

Azure AD B2C에서 Twitter 계정을 사용 하는 사용자에 대 한 로그인을 사용 하도록 설정 하려면 Twitter 응용 프로그램을 만들어야 합니다. Twitter 계정이 아직 없는 경우에서 등록할 수 있습니다 [https://twitter.com/signup](https://twitter.com/signup) . [개발자 계정에도 적용](https://developer.twitter.com/en/apply/user.html)해야 합니다. 자세한 내용은 [액세스에 대 한 적용](https://developer.twitter.com/en/apply-for-access)을 참조 하세요.

1. Twitter 계정 자격 증명을 사용 하 여 [Twitter 개발자 포털](https://developer.twitter.com/portal/projects-and-apps) 에 로그인 합니다.
1. **독립 실행형 앱** 에서 **+ 앱 만들기** 를 선택 합니다.
1. **앱 이름을** 입력 한 다음, **완료** 를 선택 합니다.
1. **앱 키** 및 **API 키 암호** 의 값을 복사 합니다.  이 둘을 모두 사용 하 여 테 넌 트에서 Twitter를 id 공급자로 구성 합니다. 
1. **앱** 설정에서 **앱 설정** 을 선택 합니다.
1. **인증 설정** 에서 **편집** 을 선택 합니다.
    1. **3-직선 OAuth 사용** 확인란을 선택 합니다.
    1. **사용자의 메일 주소 요청** 확인란을 선택 합니다.
    1. **콜백 url** 에 대해를 입력 `https://your-tenant.b2clogin.com/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` 합니다.  [사용자 지정 도메인](custom-domain.md)을 사용 하는 경우을 입력 `https://your-domain-name/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` 합니다. Azure AD B2C에 대문자를 사용 하 여 정의한 경우에도 모든 소문자를 사용 하 여 테 넌 트 이름 및 사용자 흐름 id를 입력 합니다. 다음을
        - `your-tenant-name` 을 테 넌 트 이름의 이름으로 바꿉니다.
        - `your-domain-name` 사용자 지정 도메인을 사용 합니다.
        - `your-user-flow-Id` 사용자 흐름의 식별자로 바꿉니다. 예들 들어 `b2c_1a_signup_signin_twitter`입니다. 
    
    1. **웹 사이트 URL** 에 대해를 입력 `https://your-tenant.b2clogin.com` 합니다. `your-tenant`을 테넌트 이름으로 바꿉니다. 예들 들어 `https://contosob2c.b2clogin.com`입니다. [사용자 지정 도메인](custom-domain.md)을 사용 하는 경우을 입력 `https://your-domain-name` 합니다.
    1. **서비스 약관** 에 대 한 URL을 입력 합니다 (예:) `http://www.contoso.com/tos` . 정책 URL은 응용 프로그램에 대 한 사용 약관을 제공 하기 위해 유지 관리 하는 페이지입니다.
    1. **개인 정보 취급 방침** 에 대 한 URL을 입력 합니다 (예:) `http://www.contoso.com/privacy` . 정책 URL은 애플리케이션에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
    1. **저장** 을 선택합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter를 id 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **Id 공급자** 를 선택한 다음 **Twitter** 를 선택 합니다.
1. **이름** 을 입력합니다. 예: *Twitter*.
1. **클라이언트 ID** 에 대해 이전에 만든 Twitter 응용 프로그램의 *API 키* 를 입력 합니다.
1. **클라이언트 암호** 의 경우 기록한 *API 키 암호* 를 입력 합니다.
1. **저장** 을 선택합니다.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>사용자 흐름에 Twitter id 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Twitter id 공급자를 추가 하려는 사용자 흐름을 선택 합니다.
1. **소셜 id 공급자** 아래에서 **Twitter** 를 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 **twitter** 를 선택 하 여 twitter 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에 기록한 비밀 키를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름** 을 입력합니다. 예들 들어 `TwitterSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
9. **키 사용** 에서 `Encryption`를 선택합니다.
10. **만들기** 를 클릭합니다.

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter를 id 공급자로 구성

사용자가 Twitter 계정을 사용 하 여 로그인 할 수 있도록 하려면 해당 계정을 사용 하 여 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 계정을 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Twitter 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Client_id** 의 값을 이전에 기록한 *API 키 암호로* 바꿉니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 **twitter** 를 선택 하 여 twitter 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end
