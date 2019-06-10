---
title: 사용자 지정 정책-Azure Active Directory B2C를 사용 하 여 LinkedIn 계정을 사용 하 여 로그인 설정 | Microsoft Docs
description: 설정 사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 LinkedIn 계정으로 로그인 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b336428592a4897319725782c994c3fae26bfae0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510409"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 LinkedIn 계정으로 로그인하도록 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Azure Active Directory) B2C의 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 사용자가 LinkedIn 계정에서 로그인할 수 있도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다.
- LinkedIn 계정이 없는 경우 [LinkedIn 가입 페이지](https://www.linkedin.com/start/join)에서 계정을 생성하세요.
- LinkedIn 응용 프로그램에서는 응용 프로그램을 나타내는 80 X 80픽셀 로고를 제공해야 합니다.

## <a name="create-an-application"></a>애플리케이션 만들기

Azure AD B2C에서 ID 공급자로 LinkedIn을 사용하려면 LinkedIn 응용 프로그램을 만들어야 합니다.

1. LinkedIn 계정 자격 증명을 사용하여 [LinkedIn 애플리케이션 관리](https://www.linkedin.com/secure/developer?newapp=) 웹 사이트에 로그인합니다.
2. **응용 프로그램 만들기**를 선택합니다.
3. **회사 이름**, **응용 프로그램 이름** 및 **응용 프로그램 설명**을 입력합니다.
4. 작성한 **애플리케이션 로고**를 업로드합니다.
5. 제공된 목록에서 **애플리케이션 사용**을 선택합니다.
6. **웹 사이트 URL**에 `https://your-tenant.b2clogin.com`을 입력합니다.  `your-tenant`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 contoso.b2clogin.com을 입력합니다.
7. **회사 전자 메일** 주소 및 **회사 전화** 번호를 입력합니다.
8. 페이지 맨 아래에서 사용 약관을 읽고 동의한 다음 **제출**을 선택합니다.
9. **인증**을 선택한 다음 나중에 사용할 수 있도록 **클라이언트 ID** 및 **클라이언트 비밀** 값을 적어 둡니다.
10. **권한이 부여된 리디렉션 URL**에 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant`을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다. 
11. **업데이트**를 선택합니다.
12. **설정**을 선택하고, **애플리케이션 상태**를 **Live**로 변경한 다음, **업데이트**를 선택합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 암호를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키**, **추가**를 차례로 선택합니다.
6. **옵션**으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름**을 입력합니다. 예: `LinkedInSecret`. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 암호를 **비밀**에 입력합니다.
9. **키 사용**에서 `Signature`를 선택합니다.
10. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 LinkedIn 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다. 

정책의 확장 파일에서 **ClaimsProviders** 요소에 LinkedIn 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

4. **client_id**의 값을 이전에 기록해 둔 클라이언트 ID로 바꿉니다.
5. 파일을 저장합니다.

### <a name="add-the-claims-transformations"></a>클레임 변환 추가

LinkedIn 기술 프로필이 필요 합니다 **ExtractGivenNameFromLinkedInResponse** 하 고 **ExtractSurNameFromLinkedInResponse** 클레임 목록에 추가할 변환 ClaimsTransformations 합니다. 없는 경우는 **ClaimsTransformations** 파일에 정의 된 요소는 아래와 같이 부모 XML 요소를 추가 합니다. 새 클레임 유형을 정의 해야 이라는 클레임 변환 **nullStringClaim**합니다. 

합니다 **BuildingBlocks** 요소 파일의 위쪽에 추가 해야 합니다. 참조 된 *TrustframeworkBase.xml* 예입니다.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```
### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 LinkedIn 계정과 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **정책 업로드**를 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이제 ID 공급자는 설정되었지만 등록 또는 로그인 화면에서 사용할 수는 없는 상태입니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 LinkedIn ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. 예: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. LinkedIn 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `LinkedInExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에는 Azure AD B2C가 LinkedIn 계정과 통신하여 토큰을 받는 작업을 연결합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    **TechnicalProfileReferenceId**의 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다. 예: `LinkedIn-OAUTH`.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 테넌트에서 만드는 애플리케이션을 통해 수행됩니다. 이 섹션에는 아직 만들지 않은 경우 테스트 응용 프로그램을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션 이름(예: *testapp1*)을 입력합니다.
6. **웹앱/웹 API**에서 `Yes`를 선택하고 **회신 URL**에 `https://jwt.ms`를 입력합니다.
7. **만들기**를 클릭합니다.

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInLinkedIn.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. 예: `SignUpSignInLinkedIn`.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_linkedin`으로 업데이트할 수 있습니다.
4. 새로 만든 사용자 경험의 ID(SignUpSignLinkedIn)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음 목록에서 새 정책을 선택합니다.
6. **응용 프로그램 선택** 필드에서 직접 만든 Azure AD B2C 응용 프로그램이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.

## <a name="migration-from-v10-to-v20"></a>V1.0에서 v2.0로 마이그레이션

최근에 LinkedIn [해당 API의 v1.0에서 v2.0에 업데이트](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)합니다. 기존 구성을 새 구성으로 마이그레이션하려면 기술 프로필의 요소를 업데이트 하려면 다음 섹션의 정보를 사용 합니다.

### <a name="replace-items-in-the-metadata"></a>메타 데이터에 대 한 항목을으로 바꿉니다.

기존 **메타 데이터** 의 요소를 **TechnicalProfile**에 다음 업데이트 **항목** 요소에서:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

아래와 같이 변경합니다.

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>메타 데이터 항목 추가

에 **메타 데이터** 의 **TechnicalProfile**에 다음을 추가 합니다 **항목** 요소:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>업데이트 된 OutputClaims

기존 **OutputClaims** 의 합니다 **TechnicalProfile**에 다음 업데이트 **OutputClaim** 요소:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

아래와 같이 변경합니다.

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>새 OutputClaimsTransformation 요소를 추가 합니다.

에 **OutputClaimsTransformations** 의 **TechnicalProfile**에 다음을 추가 합니다 **OutputClaimsTransformation** 요소:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>새 클레임 변환을 정의 하 고 클레임 유형

마지막 단계에서 정의 해야 하는 새 클레임 변환 추가 했습니다. 목록에 추가 클레임 변환을 정의 합니다 **ClaimsTransformations**합니다. 없는 경우는 **ClaimsTransformations** 파일에 정의 된 요소는 아래와 같이 부모 XML 요소를 추가 합니다. 새 클레임 유형을 정의 해야 이라는 클레임 변환 **nullStringClaim**합니다. 

합니다 **BuildingBlocks** 요소 파일의 위쪽에 추가 해야 합니다. 참조 된 *TrustframeworkBase.xml* 예입니다.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>전자 메일 주소 받기

V2.0에 v1.0에서 LinkedIn 마이그레이션의 일부로 다른 API 추가로 호출 전자 메일 주소를 가져올 필요 합니다. 등록 하는 동안 전자 메일 주소를 가져오려면 다음을 수행 하는 경우:

1. 로그인 사용자에 게 LinkedIn와 페더레이션 할 Azure AD B2C를 허용 하려면 위의 단계를 완료 합니다. Azure AD B2C는 페더레이션의 일부로 LinkedIn에 대 한 액세스 토큰을 받습니다.
2. LinkedIn 액세스 토큰을 클레임으로 저장 합니다. [여기의 지침 참조](idp-pass-through-custom.md)합니다.
3. LinkedIn의에 요청을 수행 하는 클레임 공급자 추가 `/emailAddress` API. 이 요청에 권한을 부여 하기 위해 LinkedIn 액세스 토큰이 필요 합니다.

    ```XML
    <ClaimsProvider> 
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 사용자가 LinkedIn을 사용 하 여 로그인 할 때 API 클레임 공급자는 트리거할 수 있도록 사용자 경험에 다음 오케스트레이션 단계를 추가 합니다. 업데이트 해야 합니다 `Order` 숫자를 적절 하 게 합니다. LinkedIn 기술 프로필을 트리거하는 오케스트레이션 단계 바로 다음이 단계를 추가 합니다.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

등록 하는 동안 LinkedIn에서 전자 메일 주소를 가져오면 선택 사항입니다. LinkedIn에서 전자 메일을 가져올 구성 중 하나가 필요를 하지 않으려는 경우 사용자가 수동으로 전자 메일 주소를 입력 하 고 유효성을 검사 해야 합니다.

LinkedIn id 공급자를 사용 하는 정책의 전체 샘플을 보려면 합니다 [사용자 지정 정책 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)합니다.
