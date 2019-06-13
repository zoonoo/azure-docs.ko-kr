---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경 구성 | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 사용자가 암호를 변경할 수 있게 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a49f62b6fc1ea00084266d4c5405f8bf96d034cb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509250"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 변경 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory(Azure AD) B2C에서는 로컬 계정으로 로그인한 사용자가 메일 확인을 통해 신뢰성을 증명할 필요 없이 암호를 변경할 수 있게 설정할 수 있습니다. 사용자가 암호 변경 흐름에 도달하는 시점에 세션이 만료되는 경우 다시 로그인하라는 메시지가 표시됩니다. 이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)에서 암호 변경을 구성하는 방법을 보여 줍니다. 사용자 흐름에 대한 [셀프 서비스 암호 재설정](active-directory-b2c-reference-sspr.md)을 구성할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

[Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](active-directory-b2c-get-started-custom.md)에 있는 단계를 완료합니다.

## <a name="add-the-elements"></a>요소 추가 

1. TrustframeworkExtensions.xml 파일을 열고 `oldPassword` 식별자를 사용하여 다음 **ClaimType** 요소를 [ClaimsSchema](claimsschema.md) 요소에 추가합니다.  

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. [ClaimsProvider](claimsproviders.md) 요소에는 사용자를 인증하는 기술 프로필이 포함됩니다. **ClaimsProviders** 요소에 다음 클레임 공급자를 추가합니다.

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    `IdentityExperienceFrameworkAppId`를 필수 구성 요소 자습서에서 만든 IdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다. `ProxyIdentityExperienceFrameworkAppId`를 이전에 만든 ProxyIdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.

3. [UserJourney](userjourneys.md) 요소는 애플리케이션을 조작할 때 사용자가 사용하는 경로를 정의합니다. `PasswordChange`로 식별된 **UserJourney**에 이 요소가 없는 경우 **UserJourneys** 요소를 추가합니다.

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. *TrustFrameworkExtensions.xml* 정책 파일을 저장합니다.
5. 시작 팩과 함께 다운로드한 *ProfileEdit.xml* 파일을 복사하고 이름을 *ProfileEditPasswordChange.xml*로 변경합니다.
6. 새 파일을 열고 **PolicyId** 특성을 고유한 값으로 업데이트합니다. 이 값은 정책 이름입니다. 예를 들면, *B2C_1A_profile_edit_password_change*입니다.
7. 만든 새 사용자 경험의 ID와 일치하도록 `<DefaultUserJourney>`의 **ReferenceId** 특성을 수정합니다. 예를 들면, *PasswordChange*입니다.
8. 변경 내용을 저장합니다.

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)에서 샘플 정책을 찾을 수 있습니다. 

## <a name="test-your-policy"></a>정책 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토할 수 있도록 Azure AD B2C 토큰이 `https://jwt.ms`에 반환되도록 하는 것이 유용할 수 있습니다.

### <a name="upload-the-files"></a>파일 업로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드**를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기**를 선택한 후 TrustFrameworkExtensions.xml 파일을 찾아서 선택합니다. 
7. **업로드**를 클릭합니다.
8. 신뢰 당사자 파일(예: *ProfileEditPasswordChange.xml*)에 대해 5~7단계를 반복합니다.

### <a name="run-the-policy"></a>정책 실행

1. 변경한 정책을 엽니다. 예를 들면, *B2C_1A_profile_edit_password_change*입니다.
2. **애플리케이션**은 이전에 등록한 애플리케이션을 선택합니다. 토큰을 보려면 **회신 URL**에 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행**을 클릭합니다. 이전에 만든 계정으로 로그인합니다. 이제 암호를 변경할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 암호 복잡성을 구성](active-directory-b2c-reference-password-complexity-custom.md)하는 방법을 알아봅니다. 
