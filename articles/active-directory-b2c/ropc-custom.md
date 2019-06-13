---
title: Azure Active Directory B2C에서 리소스 소유자 암호 자격 증명 흐름 구성 | Microsoft Docs
description: Azure Active Directory B2C에서 리소스 소유자 암호 자격 증명 흐름을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7a802354f20998c87ac8c2ef1f2c1dbdf47f6930
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733656"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 리소스 소유자 암호 자격 증명 흐름 구성

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD(Azure Active Directory) B2C에서 ROPC(리소스 소유자 암호 자격 증명) 흐름은 OAuth 표준 인증 흐름입니다. 이 흐름에서 신뢰 당사자라고도 하는 애플리케이션은 토큰에 유효한 자격 증명을 교환합니다. 자격 증명에는 사용자 ID 및 암호가 포함됩니다. 반환되는 토큰은 ID 토큰, 액세스 토큰 및 새로 고침 토큰입니다.

다음 옵션은 ROPC 흐름에서 지원됩니다.

- **네이티브 클라이언트** - 코드가 사용자 쪽 디바이스에서 실행될 때 인증 중에 사용자 상호 작용이 발생합니다.
- **공용 클라이언트 흐름** – 애플리케이션에서 수집한 사용자 자격 증명만 API 호출에서 전송됩니다. 애플리케이션의 자격 증명은 전송되지 않습니다.
- **새 클레임 추가** - ID 토큰 콘텐츠를 변경하여 새 클레임을 추가할 수 있습니다.

지원되지 않는 흐름은 다음과 같습니다.

- **서버 간** - ID 보호 시스템은 상호 작용의 일부인 호출자(네이티브 클라이언트)에서 수집한 신뢰할 수 있는 IP 주소가 필요합니다. 서버 쪽 API 호출에서는 서버의 IP 주소만 사용됩니다. 너무 많은 로그인이 실패하는 경우 ID 보호 시스템은 반복되는 IP 주소를 공격자로 간주할 수 있습니다.
- **단일 페이지 애플리케이션** - 주로 JavaScript에 작성되는 프런트 엔드 애플리케이션입니다. 애플리케이션은 대체로 AngularJS, Ember.js 또는 Durandal과 같은 프레임워크를 사용하여 작성됩니다.
- **기밀 클라이언트 흐름** - 애플리케이션 클라이언트 ID의 유효성은 검사하지만 애플리케이션 비밀의 유효성은 검사하지 않습니다.

## <a name="prerequisites"></a>필수 조건

[Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다.

## <a name="register-an-application"></a>애플리케이션 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션 이름(예: *ROPC_Auth_app*)을 입력합니다.
6. **웹앱/Web API**에 대해 **아니요**를 선택한 다음, **네이티브 클라이언트**에 대해 **예**를 선택합니다.
7. 다른 모든 값은 그대로 둔 다음, **만들기**를 선택합니다.
8. 새 애플리케이션을 선택하고, 나중에 사용할 수 있도록 애플리케이션 ID를 기록합니다.

##  <a name="create-a-resource-owner-policy"></a>리소스 소유자 정책 만들기

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
2. 아직 없는 경우 **ClaimsSchema** 요소와 해당 자식 요소를 **BuildingBlocks** 요소 아래의 첫 번째 요소로 추가합니다.

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. **ClaimsSchema** 뒤에 **ClaimsTransformations** 요소와 해당 자식 요소를 **BuildingBlocks** 요소에 추가합니다.

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. `Local Account SignIn`의 **DisplayName**이 있는 **ClaimsProvider** 요소를 찾아 다음 기술 프로필을 추가합니다.

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    대체는 **DefaultValue** 의 **client_id** 필수 구성 요소 자습서에서 만든 ProxyIdentityExperienceFramework 응용 프로그램의 응용 프로그램 id입니다. 그런 **DefaultValue** 의 **resource_id** 도 필수 구성 요소 자습서에서 만든 IdentityExperienceFramework 응용 프로그램의 응용 프로그램 id입니다.  

5. 다음 **ClaimsProvider** 요소를 기술 프로필과 함께 **ClaimsProviders** 요소에 추가합니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. **UserJourneys** 요소와 해당 자식 요소를 **TrustFrameworkPolicy** 요소에 추가합니다.

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
8. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
9. **업로드**를 클릭합니다.

## <a name="create-a-relying-party-file"></a>신뢰 당사자 파일 만들기

다음으로, 만든 사용자 경험을 시작하는 신뢰 당사자 파일을 업데이트합니다.

1. 작업 디렉터리에 *SignUpOrSignin.xml* 파일의 복사본을 만든 다음, 이름을 *ROPC_Auth.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 변경합니다. 정책 ID는 정책의 이름입니다. 예를 들어 **B2C_1A_ROPC_Auth**입니다.
3. **DefaultUserJourney**의 **ReferenceId** 특성 값을 `ResourceOwnerPasswordCredentials`로 변경합니다.
4. 다음 클레임만 포함되도록 **OutputClaims** 요소를 변경합니다.
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
6. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
7. **업로드**를 클릭합니다.

## <a name="test-the-policy"></a>정책 테스트

선호하는 API 개발 애플리케이션을 사용하여 API 호출을 생성하고, 응답을 검토하여 정책을 디버그합니다. 다음 정보를 POST 요청의 본문으로 사용하여 이 예제와 같은 호출을 생성합니다.

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
- `B2C_1A_ROPC_Auth`를 리소스 소유자 암호 자격 증명 정책의 전체 이름으로 바꿉니다.

| 키 | 값 |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | 토큰 id_token |

- `user-account`를 테넌트의 사용자 계정 이름으로 바꿉니다.
- `password1`을 사용자 계정의 암호로 바꿉니다.
- `application-id`를 *ROPC_Auth_app* 등록의 애플리케이션 ID로 바꿉니다.
- *Offline_access*는 새로 고침 토큰을 받고 싶은 경우에 사용하는 선택 사항입니다.

실제 POST 요청은 다음 예제와 같이 표시됩니다.

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

offline-access의 성공적인 응답은 다음 예제와 같습니다.

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>새로 고침 토큰 교환

여기에 표시된 것과 같은 POST 호출을 생성합니다. 다음 표의 정보를 요청의 본문으로 사용합니다.

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
- `B2C_1A_ROPC_Auth`를 리소스 소유자 암호 자격 증명 정책의 전체 이름으로 바꿉니다.

| 키 | 값 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- `application-id`를 *ROPC_Auth_app* 등록의 애플리케이션 ID로 바꿉니다.
- `refresh-token`을 이전 응답에서 다시 전송된 **refresh_token**으로 바꿉니다.

성공적인 응답은 다음 예제와 비슷합니다.

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>네이티브 SDK 또는 App-Auth 사용

Azure AD B2C는 공용 클라이언트 리소스 소유자 암호 자격 증명에 대한 OAuth 2.0 표준을 충족하며, 대부분의 클라이언트 SDK와 호환되어야 합니다. 최신 정보는 [모범 사례를 구현하는 OAuth 2.0 및 OpenID Connect용 네이티브 앱 SDK](https://appauth.io/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C 사용자 지정 정책 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc)에서 이 시나리오의 전체 예제를 참조하세요.
- [토큰 참조](active-directory-b2c-reference-tokens.md)에서 Azure Active Directory B2C에 사용되는 토큰에 대해 자세히 알아봅니다.
