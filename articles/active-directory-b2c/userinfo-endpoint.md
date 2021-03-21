---
title: UserInfo 끝점 | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책에 사용자 정보 끝점을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500583"
---
# <a name="userinfo-endpoint"></a>UserInfo 엔드포인트

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

사용자 정보 끝점은 oidc ( [Openid connect Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) ) 사양의 일부 이며 인증 된 사용자에 대 한 클레임을 반환 하도록 설계 되었습니다. UserInfo 끝점은 [끝점](relyingparty.md#endpoints) 요소를 사용 하 여 신뢰 당사자 정책에 정의 됩니다.  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>UserInfo 끝점 개요

사용자 정보 UserJourney은 다음을 지정 합니다.

- **권한 부여**: UserInfo 끝점은 전달자 토큰을 사용 하 여 보호 됩니다. 발급 된 액세스 토큰은 권한 부여 헤더에서 UserInfo 끝점으로 제공 됩니다. 정책은 들어오는 토큰의 유효성을 검사 하 고 사용자의 objectId와 같은 클레임을 추출 하는 기술 프로필을 지정 합니다. 사용자의 objectId는 사용자 정보 끝점의 응답에서 반환할 클레임을 검색 하는 데 사용 됩니다. 
- **오케스트레이션 단계**: 
  - 오케스트레이션 단계는 사용자에 대 한 정보를 수집 하는 데 사용 됩니다. 사용자 경험에서는 들어오는 액세스 토큰 내의 클레임에 따라 사용자에 대 한 데이터 (예: objectId를 사용 하 여 사용자 읽기)를 검색 하는 [Azure Active Directory 기술 프로필](active-directory-technical-profile.md) 을 호출 합니다. 
  - **선택적 오케스트레이션 단계** -사용자에 대 한 자세한 정보를 검색 하기 위해 REST API 기술 프로필과 같은 오케스트레이션 단계를 더 추가할 수 있습니다. 
  - **Userinfo Issuer** -userinfo 끝점이 반환 하는 클레임 목록을 지정 합니다.

## <a name="create-a-userinfo-endpoint"></a>UserInfo 끝점 만들기

### <a name="1-add-the-token-issuer-technical-profile"></a>1. 토큰 발급자 기술 프로필 추가

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
1. 아직 존재 하지 않는 경우 ClaimsProvider 요소 및 해당 자식 요소를 BuildingBlocks 요소 아래의 첫 번째 요소로 추가 합니다.
1. 다음 클레임 공급자를 추가 합니다.

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. **Userinfoissuer** 기술 프로필 내의 inputclaims 섹션은 반환 하려는 특성을 지정 합니다. UserInfoIssuer 기술 프로필은 사용자 경험의 끝에서 호출 됩니다. 
1. **Userinfoauthorization** 기술 프로필은 서명, 발급자 이름 및 토큰 대상의 유효성을 검사 하 고 인바운드 토큰에서 클레임을 추출 합니다. 다음 메타 데이터를 변경 하 여 환경을 반영 합니다.
    1. **issuer** -이 값은 `iss` 액세스 토큰 클레임 내의 클레임과 동일 해야 합니다. Azure AD B2C에서 발급 한 토큰은 형식의 발급자를 사용 `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` 합니다. [토큰 사용자 지정](configure-tokens.md)에 대해 자세히 알아보세요.
    1. **Idtokenaudience** - `aud` 액세스 토큰 클레임 내의 클레임과 동일 해야 합니다. Azure AD B2C 클레임은 `aud` 신뢰 당사자 응용 프로그램의 ID입니다. 이 값은 컬렉션 이며 쉼표 구분 기호를 사용 하 여 여러 값을 지원 합니다.

        다음 액세스 토큰에서 `iss` 클레임 값은 `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` 입니다. `aud`클레임 값이 인 경우 `22222222-2222-2222-2222-222222222222`

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  **Userinfoauthorization** 기술 프로필의 outputclaims 요소는 액세스 토큰에서 읽으려는 특성을 지정 합니다. **ClaimTypeReferenceId** 는 클레임 형식에 대 한 참조입니다. 선택적 항목 **claimtype** 은 액세스 토큰에 정의 된 클레임의 이름입니다.



### <a name="2-add-the-userjourney-element"></a>2. UserJourney 요소 추가 

[UserJourney](userjourneys.md) 요소는 애플리케이션을 조작할 때 사용자가 사용하는 경로를 정의합니다. `UserInfoJourney`로 식별된 **UserJourney** 에 이 요소가 없는 경우 **UserJourneys** 요소를 추가합니다.

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. 신뢰 당사자 정책에 끝점 포함

신뢰 당사자 응용 프로그램에 UserInfo 끝점을 포함 하려면 *Socialandlocalaccounts/SignUpOrSignIn.xml* 파일에 [끝점](relyingparty.md#endpoints) 요소를 추가 합니다. 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

완료 된 신뢰 당사자 요소는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. 파일을 업로드 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. **사용자 지정 정책** 페이지에서 **사용자 지정 정책 업로드** 를 선택합니다.
1. **이미 있는 경우 사용자 지정 정책 덮어쓰기** 를 선택 하 고 *TrustframeworkExtensions.xml* 파일을 검색 한 다음 선택 합니다.
1. **업로드** 를 클릭합니다.
1. 신뢰 당사자 파일(예: SignUpOrSignIn.xml)에 대해 5~7단계를 반복합니다.

## <a name="calling-the-userinfo-endpoint"></a>UserInfo 끝점 호출

사용자 정보 끝점은 응용 프로그램에 대 한 토큰을 가져올 때 받은 액세스 토큰을 사용 하 여 호출 되는 표준 OAuth2 전달자 토큰 API를 사용 합니다. 사용자에 대 한 클레임이 포함 된 JSON 응답을 반환 합니다. UserInfo 끝점은 Azure AD B2C에서 호스팅됩니다.

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.Well-known configure 끝점 (Openid connect Connect 검색 문서)은 필드를 나열 합니다 `userinfo_endpoint` . 에서/.well-known configure 끝점을 사용 하 여 사용자 정보 끝점을 프로그래밍 방식으로 검색할 수 있습니다. 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>정책 테스트

1. **사용자 지정 정책에서 사용자** 정보 끝점을 통합 한 정책을 선택 합니다. 예를 들어 *B2C_1A_SignUpOrSignIn* 합니다.
1. **지금 실행** 을 선택합니다. 
1. **응용 프로그램 선택** 에서 이전에 등록 한 응용 프로그램을 선택 합니다. **회신 Url 선택** 에 대해를 선택 `https://jwt.ms` 합니다. 자세한 내용은 [Azure Active Directory B2C에서 웹 응용 프로그램 등록](tutorial-register-applications.md)을 참조 하세요.
1. 전자 메일 주소 또는 소셜 계정으로 등록 하거나 로그인 합니다.
1. 웹 사이트에서 인코딩된 형식으로 id_token를 복사 [https://jwt.ms](https://jwt.ms) 합니다. 이를 사용 하 여 사용자 정보 끝점에 GET 요청을 제출 하 고 사용자 정보를 검색할 수 있습니다.
1. GET 요청을 UserInfo 끝점에 제출 하 고 사용자 정보를 검색 합니다.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

성공적인 응답은 다음과 같습니다.

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint)에 대 한 사용자 정보 끝점 정책의 예를 찾을 수 있습니다.

::: zone-end
