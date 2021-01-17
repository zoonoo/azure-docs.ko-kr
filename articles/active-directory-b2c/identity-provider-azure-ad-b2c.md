---
title: 다른 Azure AD B2C 테 넌 트에서 Azure AD B2C 계정으로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램의 다른 테 넌 트에서 Azure AD B2C 계정을 사용 하 여 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a0d69ea57eb5b8b2a074c37d4798a99c576ce95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538168"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>다른 Azure AD B2C 테 넌 트에서 Azure AD B2C 계정으로 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>개요

이 문서에서는 다른 Azure AD B2C 테 넌 트를 사용 하 여 페더레이션을 설정 하는 방법을 설명 합니다. 응용 프로그램이 Azure AD B2C로 보호 되 면 다른 Azure AD B2c의의 사용자가 기존 계정으로 로그인 할 수 있습니다. 다음 다이어그램에서 사용자는 *Contoso* 의 Azure AD B2C에 의해 보호 되는 응용 프로그램에 로그인 할 수 있으며 *Fabrikam* 의 Azure AD B2C 테 넌 트에서 관리 되는 계정으로 로그인 할 수 있습니다. 

![다른 Azure AD B2C 테 넌 트와 Azure AD B2C 페더레이션](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

다른 Azure AD B2C 테 넌 트 (예: Fabrikam)의 계정이 있는 사용자에 대 한 로그인을 사용 하도록 설정 하려면 (예: Contoso) Azure AD B2C 합니다.

1. [사용자 흐름](tutorial-create-user-flows.md)또는 [사용자 지정 정책을](custom-policy-get-started.md)만듭니다.
1. 그런 다음이 섹션에 설명 된 대로 Azure AD B2C에서 응용 프로그램을 만듭니다. 

응용 프로그램을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 다른 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 (예: Fabrikam.com)를 사용 하 고 있는지 확인 합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들면 *ContosoApp* 입니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다.
1. **URI 리디렉션** 에서 **웹** 을 선택 하 고, 모든 소문자에 다음 URL을 입력 합니다. 여기서 `your-B2C-tenant-name` 은 Azure AD B2C 테 넌 트의 이름 (예: Contoso)으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예를 들어, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`를 입력합니다.

1. 사용 권한 아래에서 **openid connect 및 offline_access 권한에 관리자 동의 부여** 확인란을 선택 합니다.
1. **등록** 을 선택합니다.
1. **Azure AD B2C-앱 등록** 페이지에서 만든 응용 프로그램을 선택 합니다 (예: *ContosoApp*).
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID** 를 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.
1. 왼쪽 메뉴의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료** 에서 암호가 유효한 기간을 선택한 다음 **추가** 를 선택합니다.
1. 비밀의 **값** 을 기록합니다. 다음 섹션에서 ID 공급자를 구성할 때 필요합니다.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C을 id 공급자로 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 페더레이션을 구성 하려는 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 (예: Contoso)를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 (예: Contoso)를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Fabrikam* 을 입력 합니다.
1. **메타 데이터 url** 에 대해 `{tenant}` Azure AD B2C 테 넌 트의 도메인 이름으로 대체 하는 다음 url을 입력 합니다 (예: Fabrikam). 을 `{policy}` 다른 테 넌 트에서 구성한 정책 이름으로 바꿉니다.

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    예들 들어 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`입니다.

1. **Client ID** 에 대해 이전에 기록한 애플리케이션 ID를 입력합니다.
1. 이전에 기록해 두었던 클라이언트 암호를 **클라이언트 암호** 에 입력합니다.
1. **범위** 에 `openid`을 입력합니다.
1. **응답 유형** 및 **응답 모드** 에 대한 기본값을 그대로 둡니다.
1. 필드 **도메인 힌트** 의 경우 [직접 로그인](direct-signin.md#redirect-sign-in-to-a-social-provider)에 사용 하려는 도메인 이름을 입력 합니다. 예를 들면 *fabrikam.com* 입니다.
1. **ID 공급자 클레임 매핑** 에서 다음 클레임을 선택합니다.

    - **사용자 ID**: *sub*
    - **표시 이름**: *name*
    - **지정된 이름**: *given_name*
    - **성**: *family_name*
    - **전자 메일**: *전자 메일*

1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테 넌 트에서 만든 응용 프로그램 키를 저장 해야 합니다.

1. 페더레이션을 구성 하려는 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 (예: Contoso)를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 (예: Contoso)를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키** 를 선택 하 고 **추가** 를 선택 합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `FabrikamAppSecret`입니다.  접두사는 `B2C_1A_` 생성 될 때 키의 이름에 자동으로 추가 되므로 다음 섹션의 XML 참조는 *B2C_1A_FabrikamAppSecret* 하는 것입니다.
1. **비밀** 에서 이전에 기록한 클라이언트 암호를 입력 합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Fabrikam (다른 Azure AD B2C)을 사용 하 여 로그인 하도록 하려면 끝점을 통해 통신할 수 Azure AD B2C는 다른 Azure AD B2C를 클레임 공급자로 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProvider** 요소에 Azure AD B2C를 추가 하 여 Azure AD B2C를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Fabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 다음 XML 요소를 관련 값으로 업데이트 합니다.

    |XML 요소  |값  |
    |---------|---------|
    |ClaimsProvider\Domain  | [직접 로그인](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)에 사용 되는 도메인 이름입니다. 직접 로그인에 사용 하려는 도메인 이름을 입력 합니다. 예를 들면 *fabrikam.com* 입니다. |
    |TechnicalProfile\DisplayName|이 값은 로그인 화면의 로그인 단추에 표시됩니다. 예: *Fabrikam*. |
    |메타 데이터 \ client_id|ID 공급자의 애플리케이션 식별자입니다. 다른 Azure AD B2C 테 넌 트에서 이전에 만든 응용 프로그램 ID를 사용 하 여 클라이언트 ID를 업데이트 합니다.|
    |Metadata\METADATA|Openid connect 잘 알려진 구성 끝점이 라고도 하는 Openid connect Connect id 공급자 구성 문서를 가리키는 URL입니다. `{tenant}`다른 Fabrikam (Azure AD B2C 테 넌 트)의 도메인 이름으로 대체 하는 다음 URL을 입력 합니다. 을 `{tenant}` 다른 테 넌 트에서 구성한 정책 이름으로 바꾸고을 `{policy]` 정책 이름으로 바꿉니다 `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . 예를 들어, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`를 입력합니다.|
    |CryptographicKeys| **StorageReferenceId** 의 값을 이전에 만든 정책 키의 이름으로 업데이트 합니다. 예를 들어, `B2C_1A_FabrikamAppSecret`를 입력합니다.| 
    

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금은 Azure AD B2C 다른 Azure AD B2C 테 넌 트와 통신 하는 방법을 알 수 있도록 정책을 구성 했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책** 을 선택합니다.
1. **정책이 있는 경우 덮어쓰기** 를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
1. **업로드** 를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 id 공급자가 설정 되었지만 등록/로그인 페이지에는 아직 사용할 수 없습니다. 사용할 수 있도록 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD id 공급자도 포함 하도록 수정 합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml* 을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험 ID의 이름을 바꿉니다. 예들 들어 `SignUpSignInFabrikam`입니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록/로그인 페이지에서 id 공급자 단추와 유사 합니다. Azure AD B2C에 대 한 **ClaimsProviderSelection** 요소를 추가 하면 사용자가 페이지를 열면 새 단추가 표시 됩니다.

1.  `Order="1"` *TrustFrameworkExtensions.xml* 에서 만든 사용자 경험에 포함 된 OrchestrationStep 요소를 찾습니다.
1. **ClaimsProviderSelections** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `FabrikamExchange`)으로 설정합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="FabrikamExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우 작업은 Azure AD B2C 다른 Azure AD B2C와 통신 하 여 토큰을 수신 하는 데 사용 됩니다. Azure AD B2C 클레임 공급자의 기술 프로필을 연결 하 여 작업에 단추를 연결 합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep** 을 찾습니다.
1. **Targetclaimsexchangeid** 에 사용한 **Id** 에 동일한 값을 사용 하도록 하는 다음 **claim이상 변경** 요소를 추가 합니다.

    ```xml
    <ClaimsExchange Id="FabrikamExchange" TechnicalProfileReferenceId="Fabrikam-OpenIdConnect" />
    ```

    **TechnicalProfileReferenceId** 의 값을 이전에 만든 기술 프로필의 **Id** 로 업데이트 합니다. 예들 들어 `Fabrikam-OpenIdConnect`입니다.

1. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>사용자 흐름에 id 공급자 Azure AD B2C 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Azure AD B2C id 공급자를 추가 하려는 사용자 흐름을 클릭 합니다.
1. **소셜 id 공급자** 아래에서 **Fabrikam** 을 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭 합니다.
1. 등록 또는 로그인 페이지에서 *Fabrikam* 을 선택 하 여 다른 Azure AD B2C 테 넌 트로 로그인 합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml* 의 복사본을 만들고 이름을 바꿉니다. 예를 들어 *SignUpSignInFabrikam.xml* 로 이름을 바꿉니다.
1. 새 파일을 열고 **TrustFrameworkPolicy** 의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. 예들 들어 `SignUpSignInFabrikam`입니다.
1. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어, `http://contoso.com/B2C_1A_signup_signin_fabrikam`를 입력합니다.
1. **Defaultuserjourney** 에서 **ReferenceId** 특성의 값을 이전에 만든 사용자 경험의 ID와 일치 하도록 업데이트 합니다. 예를 들면 *SignUpSignInFabrikam* 입니다.
1. 변경 내용을 저장하고 파일을 업로드합니다.
1. **사용자 지정 정책** 아래의 목록에서 새 정책을 선택 합니다.
1. **응용 프로그램 선택** 드롭다운에서 이전에 만든 Azure AD B2C 응용 프로그램을 선택 합니다. 예를 들면 *testapp1* 과 같습니다.
1. **지금 실행** 을 선택 합니다. 
1. 등록 또는 로그인 페이지에서 *Fabrikam* 을 선택 하 여 다른 Azure AD B2C 테 넌 트로 로그인 합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[다른 Azure AD B2C 토큰을 응용 프로그램에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.
