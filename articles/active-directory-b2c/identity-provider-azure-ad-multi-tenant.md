---
title: 사용자 지정 정책으로 다중 테 넌 트 Azure AD에 대 한 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 다중 테 넌 트 Azure AD id 공급자를 추가 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f6e932cb6a6086e4cea6f474f296ca086e48c75e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448459"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure Active Directory에 대한 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

이 문서에서는 Azure Active Directory (Azure AD)에 대 한 다중 테 넌 트 끝점을 사용 하 여 사용자에 대 한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다. 각 테 넌 트에 대해 id 공급자를 구성 하지 않고도 Azure AD B2C를 사용 하 여 여러 Azure AD 테 넌 트의 사용자가 로그인 할 수 있도록 허용 합니다. 그러나 이러한 테넌트의 게스트 멤버는 로그인할 수 **없습니다**. 이렇게 하려면 [각 테넌트를 개별적으로 구성](identity-provider-azure-ad-single-tenant.md)해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>애플리케이션 등록

Azure Active Directory B2C (Azure AD B2C)에서 Azure AD 계정을 사용 하 여 사용자에 대 한 로그인을 사용 하도록 설정 하려면 [Azure Portal](https://portal.azure.com)에서 응용 프로그램을 만들어야 합니다. 자세한 내용은 [Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](../active-directory/develop/quickstart-register-app.md)을 참조 하세요.


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테 넌 트를 포함 하는 디렉터리 (예: contoso.com)를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독 필터** 를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예들 들어 `Azure AD B2C App`입니다.
1. 이 응용 프로그램에 대 한 **모든 조직 디렉터리 (모든 AZURE AD 디렉터리 – 다중 테 넌 트)의 계정을** 선택 합니다.
1. **리디렉션 URI** 의 경우 **웹** 의 값을 그대로 사용 하 고 다음 URL을 소문자로 입력 합니다 `your-B2C-tenant-name` . 여기서은 Azure AD B2C 테 넌 트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    예들 들어 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`입니다.

1. **등록** 을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID** 를 기록합니다.
1. **인증서 & 암호** 를 선택 하 고 **새 클라이언트 암호** 를 선택 합니다.
1. 비밀에 대 한 **설명을** 입력 하 고 만료를 선택한 다음 **추가** 를 선택 합니다. 이후 단계에서 사용할 암호의 **값** 을 기록 합니다.

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

`family_name` `given_name` Azure AD에서 및 클레임을 가져오려면 Azure Portal UI 또는 응용 프로그램 매니페스트에서 응용 프로그램에 대 한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법](../active-directory/develop/active-directory-optional-claims.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록** 을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성** 을 선택합니다.
1. **선택적 클레임 추가** 를 선택합니다.
1. **토큰 형식** 에서 **ID** 를 선택 합니다.
1. , 및를 추가할 선택적 클레임을 선택 `family_name` `given_name` 합니다.
1. **추가** 를 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독 필터** 를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키** 를 선택 하 고 **추가** 를 선택 합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `AADAppSecret`입니다.  접두사는 `B2C_1A_` 생성 될 때 키의 이름에 자동으로 추가 되므로 다음 섹션의 XML 참조는 *B2C_1A_AADAppSecret* 하는 것입니다.
1. **비밀** 에서 이전에 기록한 클라이언트 암호를 입력 합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="configure-azure-ad-as-an-identity-provider"></a>테넌트에서 Azure AD를 ID 공급자로 구성

사용자가 Azure AD 계정을 사용 하 여 로그인 할 수 있도록 하려면 Azure AD를 끝점을 통해 통신할 수 Azure AD B2C 하는 클레임 공급자로 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **ClaimsProvider** 요소 아래의 **Domain** 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
1. **TechnicalProfile** 요소 아래에서 **DisplayName** 의 값을 업데이트 합니다 (예:) `Contoso Employee` . 이 값은 로그인 페이지의 로그인 단추에 표시됩니다.
1. **Client_id** 를 이전에 등록 한 Azure AD 다중 테 넌 트 응용 프로그램의 응용 프로그램 id로 설정 합니다.
1. **CryptographicKeys** 에서 **StorageReferenceId** 의 값을 이전에 만든 정책 키의 이름으로 업데이트 합니다. 예들 들어 `B2C_1A_AADAppSecret`입니다.

### <a name="restrict-access"></a>액세스 제한

**ValidTokenIssuerPrefixes** 의 값으로 `https://login.microsoftonline.com/`를 사용하면 모든 Azure AD 사용자가 애플리케이션에 로그인할 수 있습니다. 유효한 토큰 발급자 목록을 업데이트 하 고 로그인 할 수 있는 Azure AD 테 넌 트 사용자의 특정 목록에 대 한 액세스를 제한 합니다.

값을 얻으려면 사용자가 로그인 할 각 Azure AD 테 넌 트에 대 한 Openid connect Connect 검색 메타 데이터를 확인 합니다. 메타 데이터 URL의 형식은와 유사 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` `your-tenant` 합니다. 여기서는 Azure AD 테 넌 트 이름입니다. 다음은 그 예입니다.

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

로그인 하는 데 사용 해야 하는 각 Azure AD 테 넌 트에 대해 다음 단계를 수행 합니다.

1. 브라우저를 열고 테 넌 트에 대 한 Openid connect Connect 메타 데이터 URL로 이동 합니다. **발급자** 개체를 찾아 해당 값을 기록 합니다. 와 유사 하 게 표시 됩니다 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. 값을 복사 하 여 **ValidTokenIssuerPrefixes** 키에 붙여 넣습니다. 여러 발급자를 쉼표로 구분 합니다. 두 발급자의 예가 이전 XML 샘플에 표시 됩니다 `ClaimsProvider` .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지에서 **COMMON AAD** 를 선택 하 여 Azure AD 계정으로 로그인 합니다.

다중 테 넌 트 로그인 기능을 테스트 하려면 다른 Azure AD 테 넌 트에 존재 하는 사용자의 자격 증명을 사용 하 여 마지막 두 단계를 수행 합니다. **지금 실행 끝점** 을 복사 하 여 개인 브라우저 창에서 엽니다 (예: Google Chrome의 Incognito 모드 또는 Microsoft Edge의 InPrivate 창). 개인 브라우저 창에서 열면 현재 캐시 된 Azure AD 자격 증명을 사용 하지 않고 전체 사용자 경험을 테스트할 수 있습니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 정책을 사용 하는 경우 개발 하는 동안 정책의 문제를 해결할 때 추가 정보가 필요할 수 있습니다.

문제를 진단 하는 데 도움이 되도록 정책을 일시적으로 "개발자 모드"에 배치 하 고 Azure 애플리케이션 Insights로 로그를 수집할 수 있습니다. [Azure Active Directory B2C: 로그 수집](troubleshoot-with-application-insights.md)방법을 알아보세요.

::: zone-end
