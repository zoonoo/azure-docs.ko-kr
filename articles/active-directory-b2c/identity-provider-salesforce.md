---
title: Salesforce 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램에서 Salesforce 계정으로 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f87ef03a3af110ec17b7d907d4c2eac1bc1ca58b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579929"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 Salesforce 계정으로 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Salesforce 응용 프로그램 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Salesforce 계정이 있는 사용자에 대 한 로그인을 사용 하도록 설정 하려면 Salesforce [앱 관리자](https://login.salesforce.com/)에서 응용 프로그램을 만들어야 합니다. 자세한 내용은 [기본 연결 된 앱 설정 구성](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)및 [API 통합에 OAuth 설정 사용](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm) 을 참조 하세요.

1. [Salesforce에 로그인](https://login.salesforce.com/)합니다.
1. 메뉴에서 **설정** 을 선택 합니다.
1.  **앱** 을 확장 하 고 **앱 관리자** 를 선택 합니다.
1. **새로 연결 된 앱** 을 선택 합니다.
1. **기본 정보** 아래에서 다음을 입력 합니다.
    1. **연결 된 앱 이름** -연결 된 앱 이름이 앱 관리자 및 앱 시작 관리자 타일에 표시 됩니다. 이름은 조직 내에서 고유 해야 합니다. 
    1. **API 이름** 
    1. **연락처 전자 메일** -Salesforce에 대 한 연락처 전자 메일
1. **API (Oauth 설정 사용)** 에서 **OAuth 설정 사용** 을 선택 합니다.
    1. **Callback URL**(콜백 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용 하는 경우을 입력 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` 합니다. `your-tenant-name`을 테 넌 트의 이름으로,를 `your-domain-name` 사용자 지정 도메인으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
    1. **선택한 OAuth 범위** 에서 **기본 정보에 액세스 (id, 프로필, 전자 메일, 주소, 전화)** 를 선택 하 고 **고유 식별자 (openid connect)에** 대 한 액세스를 허용 합니다.
    1. **웹 서버 흐름에 대해 암호 요구를** 선택 합니다.
1. **ID 토큰 구성** 을 선택 합니다. 
    1. 토큰을 5 분 **동안 유효** 하 게 설정 합니다.
    1. **표준 클레임 포함** 을 선택 합니다.
1. **저장** 을 클릭합니다.
1. **소비자 키** 및 **소비자 암호** 의 값을 복사 합니다. 테 넌 트에서 Salesforce를 id 공급자로 구성 하려면 둘 다 필요 합니다. **클라이언트 암호** 는 중요 한 보안 자격 증명입니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Salesforce를 id 공급자로 구성

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Salesforce* 를 입력 합니다.
1. **메타 데이터 url** 에 대해 [Salesforce openid connect CONNECT 구성 문서의](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)url을 입력 합니다. 샌드박스에서 login.salesforce.com는 test.salesforce.com로 대체 됩니다. 커뮤니티의 경우 login.salesforce.com은 username.force.com/.well-known/openid-configuration과 같은 커뮤니티 URL로 바뀝니다. URL은 HTTPS 여야 합니다.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. **Client ID** 에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호** 에 입력합니다.
1. **범위** 에 `openid id profile email`을 입력합니다.
1. **응답 유형** 및 **응답 모드** 에 대한 기본값을 그대로 둡니다.
1. (선택 사항) **도메인 힌트** 에 `contoso.com`을 입력합니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요.
1. **ID 공급자 클레임 매핑** 에서 다음 클레임을 선택합니다.

    - **사용자 ID**: *sub*
    - **표시 이름**: *name*
    - **지정된 이름**: *given_name*
    - **성**: *family_name*
    - **전자 메일**: *전자 메일*

1. **저장** 을 선택합니다.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>사용자 흐름에 Salesforce id 공급자 추가 

이 시점에서 Salesforce id 공급자가 설정 되었지만 아직 로그인 페이지에서 사용할 수 없습니다. 사용자 흐름에 Salesforce id 공급자를 추가 하려면 다음을 수행 합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Salesforce id 공급자를 추가 하려는 사용자 흐름을 클릭 합니다.
1. **소셜 id 공급자** 아래에서 **Salesforce** 를 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지 **에서 salesforce를 선택 하** 여 salesforce 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름** 을 입력합니다. 예들 들어 `SalesforceSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
9. **키 사용** 에서 `Signature`를 선택합니다.
10. **만들기** 를 클릭합니다.

## <a name="configure-salesforce-as-an-identity-provider"></a>Salesforce를 id 공급자로 구성

사용자가 Salesforce 계정을 사용 하 여 로그인 할 수 있도록 하려면 해당 계정을 사용 하 여 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 계정을 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Salesforce 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. **메타 데이터** 는 [Salesforce openid connect CONNECT 구성 문서의](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)URL로 설정 됩니다. 샌드박스에서 login.salesforce.com는 test.salesforce.com로 대체 됩니다. 커뮤니티의 경우 login.salesforce.com은 username.force.com/.well-known/openid-configuration과 같은 커뮤니티 URL로 바뀝니다. URL은 HTTPS 여야 합니다.
5. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
6. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지 **에서 salesforce를 선택 하** 여 salesforce 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.


::: zone-end

## <a name="next-steps"></a>다음 단계

[Salesforce 토큰을 응용 프로그램에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.
