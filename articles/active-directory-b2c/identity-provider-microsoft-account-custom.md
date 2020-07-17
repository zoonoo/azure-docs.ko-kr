---
title: 사용자 지정 정책을 사용하여 Microsoft 계정을 통한 로그인 설정
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용하여 OIDC(OpenID Connect) 프로토콜을 사용하는 ID 공급자로 MSA(Microsoft 계정)를 설정하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db81f8b60cf4883223f6fc084c19c8da1d07bc9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388105"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Microsoft 계정을 통한 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)의 [사용자 지정 정책](custom-policy-overview.md)을 사용하여 사용자가 Microsoft 계정에서 로그인할 수 있도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.
- Microsoft 계정이 아직 없으면 [https://www.live.com/](https://www.live.com/)에서 만듭니다.

## <a name="register-an-application"></a>애플리케이션 등록

Microsoft 계정을 통한 사용자 로그인을 사용하도록 설정하려면 Azure AD 테넌트 내에서 애플리케이션을 등록해야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원되는 계정 유형**에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다.
1. **리디렉션 URI(선택 사항)** 에서 **웹**을 선택하고 텍스트 상자에 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp`를 입력합니다. `<tenant-name>`을 Azure AD B2C 테넌트 이름으로 바꿉니다.
1. **등록**을 선택합니다.
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID**를 기록합니다. 이후 섹션에서 클레임 공급자를 구성할 때 필요합니다.
1. **인증서 및 암호**를 선택합니다.
1. **새 클라이언트 암호**를 클릭합니다.
1. 암호에 대한 **설명**(예: MSA 애플리케이션 클라이언트 암호)을 입력한 다음 **추가**를 클릭합니다.
1. **값** 열에 표시된 애플리케이션 암호를 기록합니다. 이 값은 다음 섹션에서 사용됩니다.

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 및 `given_name` 클레임을 가져오려는 경우 Azure Portal UI 또는 애플리케이션 매니페스트에서 애플리케이션에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법](../active-directory/develop/active-directory-optional-claims.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록**을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성(미리 보기)** 을 선택합니다.
1. **선택적 클레임 추가**를 선택합니다.
1. 구성하려는 토큰 유형을 선택합니다.
1. 추가할 선택적 클레임을 선택합니다.
1. **추가**를 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이제 Azure AD 테넌트에서 애플리케이션을 만들었으므로 Azure AD B2C 테넌트에 해당 애플리케이션의 클라이언트 암호를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. 예들 들어 `MSASecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **암호**에 이전 섹션에서 기록한 클라이언트 암호를 입력합니다.
1. **키 사용**에서 `Signature`를 선택합니다.
1. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Microsoft 계정을 사용하여 로그인하도록 설정하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 정책 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. **client_id**의 값을 이전에 기록한 Azure AD 애플리케이션의 애플리케이션(클라이언트) ID로 바꿉니다.
1. 파일을 저장합니다.

이제 Azure AD B2C가 Azure AD의 Microsoft 계정 애플리케이션과 통신하는 방법을 알 수 있도록 정책을 구성했습니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

계속하기 전에 수정된 정책을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure Portal에서 Azure AD B2C 테넌트로 이동하고 **Identity Experience Framework**를 선택합니다.
1. **사용자 지정 정책** 페이지에서 **사용자 지정 정책 업로드**를 선택합니다.
1. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
1. **업로드**를 클릭합니다.

포털에 오류가 표시되지 않으면 다음 섹션을 계속 진행합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이제 ID 공급자는 설정했지만 등록 또는 로그인 화면에서 사용할 수는 없는 상태입니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Microsoft 계정 ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험 ID의 이름을 바꿉니다. 예들 들어 `SignUpSignInMSA`입니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. Microsoft 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 앞에서 만든 사용자 경험의 `Order="1"`이 포함된 **OrchestrationStep** 요소를 *TrustFrameworkExtensions.xml* 파일에서 찾습니다.
1. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `MicrosoftAccountExchange`)으로 설정합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C가 Microsoft 계정과 통신하여 토큰을 수신할 수 있게 됩니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
1. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    **TechnicalProfileReferenceId**의 값을 이전에 추가한 클레임 공급자의 **TechnicalProfile** 요소에 있는 `Id` 값과 일치하도록 업데이트합니다. 예들 들어 `MSA-OIDC`입니다.

1. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에서 등록하는 애플리케이션을 통해 수행됩니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInMSA.xml*로 바꿉니다.
1. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. 예들 들어 `SignUpSignInMSA`입니다.
1. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_msa`으로 업데이트할 수 있습니다.
1. 앞서 만든 사용자 경험의 ID(SignUpSignInMSA)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
1. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
1. 이전 섹션에서 만든(또는 필수 구성 요소(예: *webapp1* 또는 *testapp1*)를 완료하여) Azure AD B2C 애플리케이션이 **애플리케이션 선택** 필드에서 선택되어 있는지 확인한 다음 **지금 실행**을 클릭하여 테스트합니다.
1. **Microsoft 계정** 단추를 선택하고 로그인합니다.

    로그인 작업이 성공하면 다음과 같이 디코딩된 토큰을 표시하는 `jwt.ms`로 리디렉션됩니다.

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://<tenant-name>.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
