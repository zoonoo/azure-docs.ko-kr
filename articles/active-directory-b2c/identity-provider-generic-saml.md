---
title: SAML id 공급자를 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C에서 SAML id 공급자 (IdP)를 사용 하 여 등록 및 로그인을 설정 합니다.
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
ms.openlocfilehash: 357ea903ed4bbc87717dfefc1c542722f5bd40c0
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448407"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 SAML id 공급자로 등록 및 로그인 설정

Azure Active Directory B2C (Azure AD B2C)는 SAML 2.0 id 공급자와의 페더레이션을 지원 합니다. 이 문서에서는 사용자가 [ADFS](identity-provider-adfs2016-custom.md) 및 [Salesforce](identity-provider-salesforce-saml.md)와 같은 기존 소셜 또는 엔터프라이즈 id를 사용 하 여 로그인 할 수 있도록 허용 하 여 SAML id 공급자 사용자 계정으로 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>시나리오 개요

사용자가 외부 소셜 또는 엔터프라이즈 SAML id 공급자 (IdP)의 자격 증명을 사용 하 여 응용 프로그램에 로그인 할 수 있도록 Azure AD B2C를 구성할 수 있습니다. Saml id 공급자를 사용 하 여 페더레이션를 Azure AD B2C 하는 경우 saml **id 공급자** 에 대 한 saml 요청을 시작 하 고 saml 응답을 기다리는 **서비스 공급자** 역할을 합니다. 다음 다이어그램에서:

1. 응용 프로그램이 Azure AD B2C 권한 부여 요청을 시작 합니다. 응용 프로그램은 [OAuth 2.0](protocols-overview.md) 또는 [openid connect Connect](openid-connect.md) 응용 프로그램 또는 [SAML 서비스 공급자](saml-service-provider.md)일 수 있습니다. 
1. Azure AD B2C 로그인 페이지에서 사용자는 SAML id 공급자 계정 (예: *Contoso*)을 사용 하 여 로그인 하도록 선택 합니다. Azure AD B2C는 SAML 권한 부여 요청을 시작 하 고 사용자를 SAML id 공급자로 이동 하 여 로그인을 완료 합니다.
1. Saml id 공급자는 SAML 응답을 반환 합니다.
1. Azure AD B2C는 SAML 토큰의 유효성을 검사 하 고, 클레임을 추출 하 고, 자체 토큰을 발급 하 고, 사용자가 응용 프로그램으로 다시 이동 합니다.  

![SAML id 공급자 흐름을 사용 하 여 로그인](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>솔루션의 구성 요소

이 시나리오에 필요한 구성 요소는 다음과 같습니다.

* Azure AD B2C에서 SAML 요청을 받고 디코드 하 고 응답 하는 기능이 있는 SAML **id 공급자** 입니다.
* Id 공급자에 대해 공개적으로 사용할 수 있는 SAML **메타 데이터 끝점** 입니다.
* [Azure AD B2C 테 넌 트](tutorial-create-tenant.md)입니다.
 

## <a name="create-a-policy-key"></a>정책 키 만들기

Azure AD B2C와 SAML id 공급자 간의 신뢰를 설정 하려면 개인 키가 있는 유효한 X509 인증서를 제공 해야 합니다. Azure AD B2C는 인증서의 개인 키를 사용 하 여 SAML 요청을 서명 합니다. Id 공급자는 인증서의 공개 키를 사용 하 여 요청의 유효성을 검사 합니다. 공개 키는 기술 프로필 메타 데이터를 통해 액세스할 수 있습니다. .cer 파일을 SAML ID 공급자에 수동으로 업로드할 수도 있습니다.

대부분의 시나리오에서는 자체 서명된 인증서를 사용하면 됩니다. 프로덕션 환경에서는 인증 기관에서 발급한 X509 인증서를 사용하는 것이 좋습니다. 또한 이 문서 뒷부분에 설명되어 있는 것처럼 비프로덕션 환경에서는 양쪽에서 모두 SAML 서명을 사용하지 않도록 설정할 수 있습니다.

### <a name="obtain-a-certificate"></a>인증서 받기

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>인증서 업로드

Azure AD B2C 테넌트에 인증서를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Upload`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `SAMLSigningCert`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 프라이빗 키가 있는 인증서 .pfx 파일을 찾아 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-the-saml-technical-profile"></a>SAML 기술 프로필 구성

정책의 확장 파일에서 **ClaimsProviders** 요소에 SAML id 공급자를 추가 하 여 정의 합니다. 클레임 공급자는 SAML id 공급자와 통신 하는 데 필요한 끝점 및 프로토콜을 결정 하는 SAML 기술 프로필을 포함 합니다. SAML 기술 프로필을 사용 하 여 클레임 공급자를 추가 하려면:

1. *TrustFrameworkExtensions.xml* 을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

다음 XML 요소를 관련 값으로 업데이트 합니다.

|XML 요소  |값  |
|---------|---------|
|ClaimsProvider\Domain  | [직접 로그인](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)에 사용 되는 도메인 이름입니다. 직접 로그인에 사용 하려는 도메인 이름을 입력 합니다. 예를 들면 *Contoso.com* 입니다. |
|TechnicalProfile\DisplayName|이 값은 로그인 화면의 로그인 단추에 표시됩니다. 예: *Contoso*. |
|Metadata\PartnerEntity|SAML ID 공급자의 메타데이터 URL입니다. 또는 id 공급자 메타 데이터를 복사 하 여 CDATA 요소 내에 추가할 수 있습니다 `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>클레임 매핑

**Outputclaims** 요소는 SAML id 공급자가 반환 하는 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 id 공급자에 정의 된 어설션 이름에 매핑합니다. Id 공급자에 게 클레임 (어설션) 목록이 있는지 확인 합니다. 자세한 내용은 [클레임 매핑](identity-provider-generic-saml-options.md#claims-mapping)을 참조 하세요.

위의 예에서 *Contoso-SAML2* 는 SAML id 공급자에서 반환 된 클레임을 포함 합니다.

* **IssuerUserId** 클레임이 **assertionSubjectName** 클레임에 매핑됩니다.
* **first_name** 클레임은 **givenName** 클레임에 매핑됩니다.
* **last** 클레임은 **surname** 클레임에 매핑됩니다.
* **DisplayName** 클레임이 클레임에 매핑됩니다 `http://schemas.microsoft.com/identity/claims/displayname` .
* 이름 매핑이 없는 **email** 클레임입니다.

기술 프로필은 ID 공급자가 반환하지 않은 클레임도 반환합니다.

* ID 공급자의 이름을 포함하는 **identityProvider** 클레임입니다.
* 기본값이 **socialIdpAuthentication** 인 **authenticationSource** 클레임입니다.
 
### <a name="add-the-saml-session-technical-profile"></a>SAML 세션 기술 프로필 추가

`SM-Saml-idp`SAML 세션 기술 프로필이 아직 없는 경우 확장 정책에 하나를 추가 합니다. `<ClaimsProviders>` 섹션을 찾은 후 다음 XML 코드 조각을 추가합니다. 정책에 이미 `SM-Saml-idp` 기술 프로필이 포함되어 있는 경우 다음 단계로 건너뜁니다. 자세한 내용은 [Single Sign-On 세션 관리](custom-policy-reference-sso.md)를 참조하세요.

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>SAML id 공급자 구성

정책이 구성 된 후 saml id 공급자 Azure AD B2C SAML 메타 데이터를 구성 해야 합니다. SAML 메타 데이터는 정책의 구성, **서비스 공급자** 를 노출 하기 위해 saml 프로토콜에 사용 되는 정보입니다. 로그인 및 로그 아웃, 인증서, 로그인 방법 등의 서비스 위치를 정의 합니다.

각 SAML id 공급자에는 서비스 공급자를 설정 하는 다른 단계가 있습니다. 일부 SAML id 공급자는 Azure AD B2C 메타 데이터를 요청 하 고, 다른 일부는 메타 데이터 파일을 수동으로 수행 하 고 정보를 제공 하도록 요구 합니다. 지침은 id 공급자 설명서를 참조 하세요.

다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타 데이터에 대 한 URL 주소를 보여 줍니다.

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

다음 값을 바꿉니다.

- 테 넌 트 이름 (예: your-tenant.onmicrosoft.com **)**
- **your-policy** 를 정책 이름으로. 예를 들어 B2C_1A_signup_signin_adfs로 바꿉니다.
- **-기술 프로필** 은 SAML id 공급자 기술 프로필의 이름입니다. 예를 들어 Contoso-SAML2로 바꿉니다.

브라우저를 열고 URL로 이동합니다. 올바른 URL을 입력했는지와 XML 메타데이터 파일에 대한 액세스 권한이 있는지 확인합니다.

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Id 경험 프레임 워크** 를 선택 합니다.
1. 신뢰 당사자 정책을 선택 합니다 (예:) `B2C_1A_signup_signin` .
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 등록 또는 로그인 페이지 **에서 contoso를 선택 하** 여 contoso 계정으로 로그인 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C를 사용 하 여 SAML id 공급자 옵션 구성](identity-provider-generic-saml-options.md)

::: zone-end
