---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자에 대한 로그인 설정 | Microsoft Docs
description: 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자 추가 - Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 72d01d6927ee421d01a831244acf65c44a084354
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508659"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 다중 테넌트 Azure Active Directory에 대한 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C의 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 통해 Azure AD(Azure Active Directory)에 다중 테넌트 엔드포인트를 사용하여 사용자 로그인을 활성화하는 방법을 설명합니다. 이렇게 하면 각 테넌트에 대한 기술 공급자를 구성하지 않아도 여러 Azure AD 테넌트의 사용자가 Azure AD B2C에 로그인할 수 있습니다. 그러나 이러한 테넌트의 게스트 멤버는 로그인할 수 **없습니다**. 이렇게 하려면 [각 테넌트를 개별적으로 구성](active-directory-b2c-setup-aad-custom.md)해야 합니다.

>[!NOTE]
>다음 지침에서는 조직의 Azure AD 테넌트로 `Contoso.com`을 사용하고, Azure AD B2C 테넌트로 `fabrikamb2c.onmicrosoft.com`을 사용합니다.

## <a name="prerequisites"></a>필수 조건

[Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다.

## <a name="register-an-application"></a>애플리케이션 등록

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 맨 위 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 해당 테넌트가 포함된 디렉터리를 선택하여 조직 Azure AD 테넌트(contoso.com)를 포함하는 디렉터리를 사용합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **새 애플리케이션 등록**을 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예: `Azure AD B2C App`.
6. **애플리케이션 종류**에서 `Web app / API`를 선택합니다.
7. **Sign-On URL**의 경우, 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-tenant`는 Azure AD B2C 테넌트(fabrikamb2c.onmicrosoft.com)의 이름으로 바뀝니다.

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. **만들기**를 클릭합니다. 나중에 사용할 **애플리케이션 ID**를 복사합니다.
9. 애플리케이션을 선택하고 **설정**을 선택합니다.
10. **키**를 선택하고 키 설명을 입력하고 기간을 선택한 다음, **저장**을 클릭합니다. 표시되는 키의 값을 나중에 사용하기 위해 복사합니다.
11. **설정**에서 **속성**을 선택하고, **다중 테넌트**를 `Yes`로 설정한 다음, **저장**을 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. 개요 페이지에서 **ID 경험 프레임워크 - 미리 보기**를 선택합니다.
4. **정책 키**, **추가**를 차례로 선택합니다.
5. **옵션**으로는 `Manual`을 선택합니다.
6. 정책 키의 **이름**을 입력합니다. 예: `ContosoAppSecret`.  `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
7. 이전에 기록한 애플리케이션 키를 **비밀**에 입력합니다.
8. **키 사용**에서 `Signature`를 선택합니다.
9. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인하게 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 Azure AD를 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다. 

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

4. **ClaimsProvider** 요소 아래의 **Domain** 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
5. **TechnicalProfile** 요소 아래의 **DisplayName** 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
6. **client_id**를 Azure AD 다중 테넌트 앱 등록의 애플리케이션 ID로 설정합니다.

### <a name="restrict-access"></a>액세스 제한

> [!NOTE]
> **ValidTokenIssuerPrefixes**의 값으로 `https://sts.windows.net`를 사용하면 모든 Azure AD 사용자가 애플리케이션에 로그인할 수 있습니다.

유효한 토큰 발급자 목록을 업데이트하고 로그인할 수 있는 특정 Azure AD 테넌트 사용자 목록으로 액세스를 제한해야 합니다. 값을 구하려면 사용자가 로그인하게 하려는 특정 Azure AD 테넌트 각각에 대한 메타데이터를 확인해야 합니다. 데이터의 형식은 `https://login.windows.net/your-tenant/.well-known/openid-configuration`과 유사하며 여기서 `your-tenant`는 Azure AD 테넌트 이름(contoso.com 또는 다른 Azure AD 테넌트)입니다.

1. 브라우저를 열고 **METADATA** URL로 이동한 다음, **issuer** 개체를 찾아 해당 값을 복사합니다. `https://sts.windows.net/tenant-id/`과 같아야 합니다.
2. **ValidTokenIssuerPrefixes** 키의 값을 복사하여 붙여넣습니다. 여러 값은 쉼표를 사용하여 구분해 추가할 수 있습니다. 위의 샘플 XML의 주석 처리를 예로 들 수 있습니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **정책 업로드**를 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자도 포함되도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. 예: `SignUpSignInContoso`.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록/로그인 화면의 ID 공급자 단추와 비슷합니다. Azure AD에 대해 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 방문할 때 새 단추가 표시됩니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `AzureADExchange`)으로 설정합니다.

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
    
    **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 **ID**로 업데이트합니다. 예: `Common-AAD`.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 테넌트에서 만드는 애플리케이션을 통해 수행됩니다. 이 섹션에는 아직 만들지 않은 경우 테스트 응용 프로그램을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션 이름(예: *testapp1*)을 입력합니다.
6. **웹앱/웹 API**에서 `Yes`를 선택하고 **회신 URL**에 `https://jwt.ms`를 입력합니다.
7. **만들기**를 클릭합니다.

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어, 파일 이름을 *SignUpSignContoso.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. 예: `SignUpSignInContoso`.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예: `http://contoso.com/B2C_1A_signup_signin_contoso`
4. 새로 만든 사용자 경험의 ID(SignUpSignContoso)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
6. **응용 프로그램 선택** 필드에서 직접 만든 Azure AD B2C 응용 프로그램이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.
