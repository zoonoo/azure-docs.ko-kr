---
title: 사용자 지정 정책별로 다중 테넌트 Azure AD에 대한 등록 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자를 추가합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678115"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure Active Directory에 대한 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C의 [사용자 지정 정책](custom-policy-overview.md)을 통해 Azure AD(Azure Active Directory)에 다중 테넌트 엔드포인트를 사용하여 사용자 로그인을 활성화하는 방법을 설명합니다. 이렇게 하면 각 테넌트에 대한 ID 공급자를 구성할 필요 없이 여러 Azure AD 테넌트의 사용자가 Azure AD B2C를 사용하여 로그인할 수 있습니다. 그러나 이러한 테넌트의 게스트 멤버는 로그인할 수 **없습니다**. 이렇게 하려면 [각 테넌트를 개별적으로 구성](identity-provider-azure-ad-single-tenant-custom.md)해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.

## <a name="register-an-application"></a>애플리케이션 등록

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테넌트(예: contoso.com)가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독 필터를** 선택한 다음 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 응용 프로그램의 **이름을** 입력합니다. `Azure AD B2C App`)을 입력합니다.
1. 이 응용 프로그램에 대한 **모든 조직 디렉터리에서 계정을** 선택합니다.
1. 리디렉션 **URI의**경우 **Web**값을 수락하고 모든 소문자에 다음 URL을 `your-B2C-tenant-name` 입력하며 여기서 Azure AD B2C 테넌트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`)을 입력합니다.

1. **등록**을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.
1. **인증서 & 비밀을**선택한 다음 새 클라이언트 **보안**입니다.
1. 비밀에 대한 **설명을** 입력하고 만료를 선택한 다음 **추가를**선택합니다. 이후 단계에서 사용할 비밀의 **값을** 기록합니다.

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 클레임 `given_name` 및 클레임을 얻으려면 Azure 포털 UI 또는 응용 프로그램 매니페스트에서 응용 프로그램에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [Azure AD 앱에 선택적 클레임을 제공하는 방법을 참조하세요.](../active-directory/develop/active-directory-optional-claims.md)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 앱 **등록을 선택합니다.**
1. 목록에서 선택적 클레임을 구성할 응용 프로그램을 선택합니다.
1. **관리** 섹션에서 토큰 **구성을**선택합니다.
1. **선택적 클레임 추가를**선택합니다.
1. 토큰 **유형의**경우 **ID를**선택합니다.
1. 추가할 선택적 `family_name` 클레임을 `given_name`선택하고 .
1. **추가**를 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독 필터를** 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **정책에서** **ID 환경 프레임워크를**선택합니다.
1. **정책 키를** 선택한 다음 **에 추가를**선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `AADAppSecret`)을 입력합니다.  접두사는 `B2C_1A_` 키를 만들 때 키 이름에 자동으로 추가되므로 다음 섹션의 XML에서 참조하는 것은 *B2C_1A_AADAppSecret.*
1. **비밀에서**이전에 기록한 클라이언트 비밀을 입력합니다.
1. **키 사용**에서 `Signature`를 선택합니다.
1. **만들기**를 선택합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인하게 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 Azure AD를 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
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
1. **TechnicalProfile** 요소에서 **DisplayName**에 대한 값을 업데이트합니다. `Contoso Employee` 이 값은 로그인 페이지의 로그인 단추에 표시됩니다.
1. 이전에 등록한 Azure AD 다중 테넌트 응용 프로그램의 응용 프로그램 ID로 **client_id** 설정합니다.
1. **암호화키에서** **StorageReferenceId** 값을 앞에서 만든 정책 키의 이름으로 업데이트합니다. `B2C_1A_AADAppSecret`)을 입력합니다.

### <a name="restrict-access"></a>액세스 제한

> [!NOTE]
> **ValidTokenIssuerPrefixes**의 값으로 `https://login.microsoftonline.com/`를 사용하면 모든 Azure AD 사용자가 애플리케이션에 로그인할 수 있습니다.

유효한 토큰 발급자 목록을 업데이트하고 로그인할 수 있는 특정 Azure AD 테넌트 사용자 목록으로 액세스를 제한해야 합니다.

값을 얻으려면 사용자가 로그인하도록 하려는 각 Azure AD 테넌트에 대한 OpenID Connect 검색 메타데이터를 살펴봅니다. 메타데이터 URL의 형식은 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`Azure `your-tenant` AD 테넌트 이름과 유사합니다. 예를 들어:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

로그인하는 데 사용해야 하는 각 Azure AD 테넌트에 대해 다음 단계를 수행합니다.

1. 브라우저를 열고 테넌트의 OpenID Connect 메타데이터 URL로 이동합니다. **발급자** 개체를 찾아 해당 값을 기록합니다. 와 비슷하게 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`보일 것입니다.
1. 값을 복사하여 **ValidTokenIssuerPrefixes** 키에 붙여넣습니다. 여러 발급자를 쉼표로 구분합니다. 두 발급자가 있는 예제가 `ClaimsProvider` 이전 XML 샘플에 나타납니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C가 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 선택합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자도 포함되도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInContoso`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록/로그인 화면의 ID 공급자 단추와 비슷합니다. Azure AD에 대해 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 방문할 때 새 단추가 표시됩니다.

1. *TrustFrameworkExtensions.xml에서*만든 사용자 여정에 포함된 `Order="1"` **오케스트레이션Step** 요소를 찾습니다.
1. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `AzureADExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Azure AD와 통신하여 토큰을 수신할 수 있게 됩니다. Azure AD 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. **Id**에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 **ID**로 업데이트합니다. `Common-AAD`)을 입력합니다.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록하는 응용 프로그램을 통해 발생합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

사용자가 만든 사용자 여정을 시작하는 RP(의존 자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어, 파일 이름을 *SignUpSignContoso.xml*로 바꿉니다.
1. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInContoso`)을 입력합니다.
1. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. `http://contoso.com/B2C_1A_signup_signin_contoso`)을 입력합니다.
1. **[DefaultUserJourney]에서** **ReferenceId** 특성값을 업데이트하여 이전에 만든 사용자 여정의 ID와 일치합니다. 예를 들어, *가입로그인 콘토소*.
1. 변경 내용을 저장하고 파일을 업로드합니다.
1. **사용자 지정 정책에서**목록에서 새 정책을 선택합니다.
1. 응용 **프로그램 드롭다운 선택에서** 이전에 만든 Azure AD B2C 응용 프로그램을 선택합니다. 예를 들어, *testapp1*.
1. 지금 **실행 끝점을** 복사하고 개인 브라우저 창에서 열, 예를 들어, 구글 크롬의 시크릿 모드 또는 마이크로 소프트 에지의 개인 창. 개인 브라우저 창에서 열면 현재 캐시된 Azure AD 자격 증명을 사용하지 않고 전체 사용자 여정을 테스트할 수 있습니다.
1. *Contoso Employee*와 같은 Azure AD 로그인 단추를 선택한 다음 Azure AD 조직 테넌드 중 하나에 사용자에 대한 자격 증명을 입력합니다. 응용 프로그램에 권한을 부여한 다음 프로필에 대한 정보를 입력하라는 메시지가 표시됩니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며 Azure AD B2C에서 반환된 토큰의 내용을 표시합니다.

다중 테넌트 로그인 기능을 테스트하려면 다른 Azure AD 테넌트가 있는 사용자의 자격 증명을 사용하여 마지막 두 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 정책으로 작업할 때 개발 중에 정책을 문제 해결할 때 추가 정보가 필요할 수 있습니다.

문제를 진단하는 데 도움이 되려면 정책을 일시적으로 "개발자 모드"로 전환하고 Azure Application Insights를 사용하여 로그를 수집할 수 있습니다. Azure Active [Directory B2C: 로그 수집](troubleshoot-with-application-insights.md)에서 방법을 알아보십시오.
