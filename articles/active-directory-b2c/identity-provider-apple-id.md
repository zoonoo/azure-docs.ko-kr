---
title: Apple ID를 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용 하 여 응용 프로그램에서 Apple ID를 사용 하 여 고객에 게 등록 및 로그인을 제공 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc6801de858c72a703317805d00f8e50cae69bbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054717"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Azure Active Directory B2C (미리 보기)를 사용 하 여 Apple ID로 등록 및 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Apple ID 응용 프로그램 만들기

Azure Active Directory B2C (Azure AD B2C)에서 Apple ID를 사용 하는 사용자에 대 한 로그인을 사용 하도록 설정 하려면에서 응용 프로그램을 만들어야 [https://developer.apple.com](https://developer.apple.com) 합니다. 자세한 내용은 [Apple을 사용 하 여 로그인](https://developer.apple.com/sign-in-with-apple/get-started/)을 참조 하세요. Apple developer 계정이 아직 없는 경우 [Apple 개발자 프로그램](https://developer.apple.com/programs/enroll/)에서 등록할 수 있습니다.

1. 계정 자격 증명을 사용 하 여 [Apple 개발자 포털](https://developer.apple.com/account) 에 로그인 합니다.
1. 메뉴에서 **인증서, id, & 프로필** 을 선택 하 고 **(+)** 를 선택 합니다.
1. **새 식별자 등록** 에 대해 **앱 id** 를 선택 하 고 **계속** 을 선택 합니다.
1. **유형 선택** 에 대해 **앱** 을 선택 하 고 **계속** 을 선택 합니다.
1. **앱 ID 등록**:
    1. **설명** 입력 
    1. **번들 ID**(예:)를 입력 합니다 `com.contoso.azure-ad-b2c` . 
    1. **기능** 에 대 한 기능 목록에서 **Apple으로 로그인** 을 선택 합니다. 
    1. 이 단계에서 앱 ID 접두사 (팀 ID)를 기록해 둡니다. 나중에 필요합니다.
    1. **계속** 과 **등록** 을 차례로 선택합니다.
1. 메뉴에서 **인증서, id, & 프로필** 을 선택 하 고 **(+)** 를 선택 합니다.
1. **새 식별자 등록** 에 대해 **서비스 id** 를 선택 하 고 **계속** 을 선택 합니다.
1. **서비스 등록 ID**:
    1. **설명을** 입력 하십시오. 이 설명은 동의 화면에서 사용자에 게 표시 됩니다.
    1. **식별자** 를 입력 합니다 (예:) `com.consoto.azure-ad-b2c-service` . 식별자는 Openid connect Connect 흐름의 클라이언트 ID입니다.
    1. **계속** 을 선택 하 고 **등록** 을 선택 합니다.
1. **식별자** 에서 만든 식별자를 선택 합니다.
1. **Apple에서 로그인** 을 선택 하 고 **구성** 을 선택 합니다.
    1. Apple을 사용 하 여 로그인을 구성 하려는 **기본 앱 ID** 를 선택 합니다.
    1. **도메인 및 하위 도메인** 에를 입력 `your-tenant-name.b2clogin.com` 합니다. your-tenant-name을 테넌트 이름으로 바꿉니다.
    1. **반환 url** 에을 입력 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 합니다. your-tenant-name을 테넌트 이름으로 바꿉니다.
    1. **다음** 을 선택한 후 **완료** 를 선택 합니다.
    1. 팝업 창이 닫히면 **계속** 을 선택한 다음 **저장** 을 선택 합니다.

## <a name="creating-an-apple-client-secret"></a>Apple 클라이언트 암호 만들기

1. Apple 개발자 포털 메뉴에서 **키** 를 선택 하 고 **(+)** 를 선택 합니다.
1. **새 키를 등록** 하려면:
    1. **키 이름을** 입력 합니다.
    1. **Apple에서 로그인** 을 선택 하 고 **구성** 을 선택 합니다.
    1. **기본 앱 ID** 에 대해 이전에 만든 앱을 선택 하 고 **저장** 을 선택 합니다.
    1. **구성** 을 선택한 다음 **등록** 을 선택 하 여 키 등록 프로세스를 완료 합니다.
1. **키를 다운로드** 하려면 **다운로드** 를 선택 하 여 키를 포함 하는 p8 파일을 다운로드 합니다.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Apple을 id 공급자로 구성

1. [Azure Portal](https://portal.azure.com/) 에 Azure AD B2C 테 넌 트의 전역 관리자로 로그인 합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택 합니다. 또는 검색 상자를 사용 하 여 **Azure AD B2C** 를 찾고 선택 합니다.
1. **Id 공급자** 를 선택한 다음 **Apple (미리 보기)** 을 선택 합니다.
1. **이름** 을 입력합니다. 예: *Apple*.
1. **Apple 개발자 id (팀 id)** 를 입력 합니다.
1. **Apple 서비스 id (클라이언트 id)** 를 입력 합니다.
1. **Apple 키 ID** 를 입력 합니다.
1. **Apple 인증서 데이터** 를 선택 하 고 업로드 합니다.
1. **저장** 을 선택합니다.


> [!IMPORTANT] 
> - Apple을 사용 하 여 로그인 하려면 관리자가 6 개월 마다 클라이언트 암호를 갱신 해야 합니다. 
> - 이 기능의 공개 미리 보기 중에는 Apple 클라이언트 암호가 만료 되 면 수동으로 갱신 해야 합니다. Apple id 공급자 구성 소셜 IDP 페이지에서 경고가 미리 표시 되지만 사용자 고유의 미리 알림을 설정 하는 것이 좋습니다. 
> - 비밀을 갱신 해야 하는 경우 Azure Portal에서 Azure AD B2C을 열고 **id 공급자**  >  **Apple** 으로 이동한 다음 **비밀 갱신** 을 선택 합니다.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>사용자 흐름에 Apple id 공급자 추가

사용자가 Apple ID를 사용 하 여 로그인 할 수 있도록 하려면 Apple id 공급자를 사용자 흐름에 추가 해야 합니다. Apple로 로그인은 **권장 되** 는 버전의 사용자 흐름에 대해서만 구성할 수 있습니다. 사용자 흐름에 Apple id 공급자를 추가 하려면 다음을 수행 합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Apple id 공급자를 추가 하려는 사용자 흐름을 선택 합니다. 
1. **소셜 id 공급자** 에서 **Apple (미리 보기)** 을 선택 합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트 하려면 **사용자 흐름 실행** 을 선택 합니다.
1. **응용 프로그램** 의 경우 이전에 등록 한 *testapp1-development* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>클라이언트 암호 서명

이전에 다운로드 한 p8 파일을 사용 하 여 클라이언트 암호를 JWT 토큰에 서명 합니다. JWT를 만들고 서명할 수 있는 많은 라이브러리가 있습니다. [토큰을 만드는 Azure 함수](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) 를 사용 합니다. 

1. [Azure 함수](../azure-functions/functions-create-function-app-portal.md)를 만듭니다.
1. **개발자** 에서 **코드 + 테스트** 를 선택 합니다. 
1. [실행. csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) 파일의 내용을 복사 하 여 편집기에 붙여넣습니다.
1. **저장** 을 선택합니다.
1. HTTP `POST` 요청을 만들고 다음 정보를 제공 합니다.

    - **appleTeamId**: Apple 개발자 팀 ID
    - **appleServiceId**: APPLE 서비스 id (클라이언트 id 이기도)
    - **p8key**: PEM 서식 키입니다. 텍스트 편집기에서 p8 파일을 열고 줄 바꿈 사이의 모든 항목을 복사 하 여이를 가져올 수 있습니다 `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` .
 
다음 json은 Azure function 호출의 예입니다.

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure function은 응답에서 적절 하 게 형식이 지정 되 고 서명 된 클라이언트 암호 JWT로 응답 합니다. 예를 들면 다음과 같습니다.

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택 합니다. 또는 검색 상자를 사용 하 여 **Azure AD B2C** 를 찾고 선택 합니다.
1. **개요** 페이지에서 **Id 경험 프레임 워크** 를 선택 합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에 대해 **수동** 을 선택 합니다.
1. 정책 키의 **이름** 을 입력합니다. 예를 들면 "사과 Esecret"입니다. "B2C_1A_" 접두사는 키 이름에 자동으로 추가 됩니다.
1. **암호** 에 Azure 함수 (JWT 토큰)에서 반환 된 토큰의 값을 입력 합니다.
1. **키 사용** 으로는 **서명** 을 선택합니다.
1. **만들기** 를 선택합니다.

> [!IMPORTANT] 
> - Apple을 사용 하 여 로그인 하려면 관리자가 6 개월 마다 클라이언트 암호를 갱신 해야 합니다.
> - Apple 클라이언트 암호가 만료 되 면 수동으로 갱신 하 고 정책 키에 새 값을 저장 해야 합니다.
> - 새 클라이언트 암호를 생성 하려면 6 개월 이내에 고유한 미리 알림을 설정 하는 것이 좋습니다. 

## <a name="configure-apple-as-an-identity-provider"></a>Apple을 id 공급자로 구성

사용자가 Apple ID를 사용 하 여 로그인 할 수 있도록 하려면 해당 계정을 통해 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 계정을 정의 해야 합니다. 끝점은 Azure AD B2C에서 특정 사용자가 인증 되었는지 확인 하는 데 사용 하는 클레임 집합을 제공 합니다.

정책 확장 파일의 **ClaimsProviders** 요소에 Apple ID를 추가 하 여 해당 ID를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. **Client_id** 를 서비스 식별자로 설정 합니다. 예들 들어 `com.consoto.azure-ad-b2c-service`입니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
