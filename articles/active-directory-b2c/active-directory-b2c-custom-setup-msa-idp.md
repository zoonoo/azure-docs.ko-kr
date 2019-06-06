---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 ID 공급자로 MSA(Microsoft 계정) 추가 | Microsoft Docs
description: OIDC(Microsoft OpenID 연결) 프로토콜을 사용하는 ID 공급자로 Microsoft를 사용하는 샘플
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d4b1f65daf271de88ce7167da6866e1890602fd4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510333"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Microsoft 계정을 통한 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Azure Active Directory) B2C의 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 사용자가 Microsoft 계정에서 로그인할 수 있도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다.
- Microsoft 계정이 아직 없으면 [https://www.live.com/](https://www.live.com/)에서 만듭니다.

## <a name="add-an-application"></a>애플리케이션 추가

Azure AD B2C에서 ID 공급자로 Microsoft 계정을 사용하려면 Microsoft 계정 응용 프로그램을 추가해야 합니다.

1. Microsoft 계정 자격 증명을 사용하여 [Microsoft 애플리케이션 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에 로그인합니다.
2. 오른쪽 위 모서리에서 **앱 추가**를 선택합니다.
3. **응용 프로그램 이름**을 입력하고 **만들기**를 클릭합니다. 
4. **새 암호 생성**을 선택하고 ID 공급자를 구성할 때 사용한 암호를 복사합니다. 복사 id입니다. 응용 프로그램 
5. **리디렉션 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다.
6. **저장**을 선택합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

생성하여 이전에 Azure AD B2C 테넌트에 기록했던 암호를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크 - 미리 보기**를 선택합니다.
5. **정책 키**, **추가**를 차례로 선택합니다.
6. **옵션**으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름**을 입력합니다. 예: `MSASecret`. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 암호를 **비밀**에 입력합니다.
9. **키 사용**에서 `Signature`을 선택합니다.
10. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Microsoft 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다. 

정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

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
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

4.  **client_id**의 값을 이전에 기록해 둔 응용 프로그램 ID로 바꿉니다.
5.  파일을 저장합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Microsoft 계정과 통신하는 방법을 확인할 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **정책 업로드**를 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이제 ID 공급자는 설정되었지만 등록 또는 로그인 화면에서 사용할 수는 없는 상태입니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Microsoft 계정 ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. 예: `SignUpSignInMSA`.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. Microsoft 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 앞에서 만든 사용자 경험의 `Order="1"`이 포함된 **OrchestrationStep** 요소를 *TrustFrameworkExtensions.xml* 파일에서 찾습니다.
2. **ClaimsProviderSelects** 아래에 다음 요소를 추가 합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `MicrosoftAccountExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에는 Azure AD B2C가 Twitter 계정과 통신하여 토큰을 받는 작업을 연결합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    **TechnicalProfileReferenceId**의 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다. 예: `MSA-OIDC`.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 테넌트에 만드는 응용 프로그램을 통해 수행됩니다. 이 섹션에는 아직 만들지 않은 경우 테스트 응용 프로그램을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션 이름(예: *testapp1*)을 입력합니다.
6. **웹앱/웹 API**에서 `Yes`를 선택하고 **회신 URL**에 `https://jwt.ms`를 입력합니다.
7. **만들기**를 클릭합니다.

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInMSA.xml*로 바꿉니다.
2. 새 파일을 열고, **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. 예: `SignUpSignInMSA`.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 업데이트합니다(예: `http://contoso.com/B2C_1A_signup_signin_msa`).
4. 새로 만든 사용자 경험의 ID(SignUpSignInMSA)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음 목록에서 새 정책을 선택합니다.
6. **응용 프로그램 선택** 필드에서 직접 만든 Azure AD B2C 응용 프로그램이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.
