---
title: Azure Active Directory B2C를 응용 프로그램에 대 한 SAML IdP 구성
title-suffix: Azure Active Directory B2C
description: 응용 프로그램 (서비스 공급자)에 SAML 프로토콜 어설션을 제공 하도록 Azure Active Directory B2C를 구성 하는 방법입니다. Azure AD B2C는 SAML 애플리케이션에 단일 IdP(ID 공급자)로 사용됩니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107515"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에 SAML 애플리케이션 등록

이 문서에서는 인증을 위해 SAML (Security Assertion Markup Language) 응용 프로그램 (서비스 공급자)을 Azure Active Directory B2C (Azure AD B2C)에 연결 하는 방법에 대해 알아봅니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>개요

Azure AD B2C를 고객 id 및 액세스 관리 솔루션으로 사용 하는 조직에서는 SAML 프로토콜을 사용 하 여 인증 하는 응용 프로그램과 통합 해야 할 수 있습니다. 다음 다이어그램에서는 IdP ( *id 공급자* )로 Azure AD B2C를 사용 하 여 SAML 기반 응용 프로그램으로 sso (single sign-on)를 수행 하는 방법을 보여 줍니다.

![왼쪽의 id 공급자 인 B2C 및 B2C as a service provider to right를 사용 하는 다이어그램입니다.](media/saml-service-provider/saml-service-provider-integration.png)

1. 응용 프로그램은 Azure AD B2c의 SAML 로그인 끝점으로 전송 되는 SAML 인증 요청을 만듭니다.
2. 사용자는 Azure AD B2C 로컬 계정이 나 기타 페더레이션 id 공급자 (구성 된 경우)를 사용 하 여 인증할 수 있습니다.
3. 사용자가 페더레이션 id 공급자를 사용 하 여 로그인 하는 경우 토큰 응답이 Azure AD B2C 전송 됩니다.
4. Azure AD B2C는 SAML 어설션을 생성 하 고 응용 프로그램에 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 문서에서 설명하는 사용자 지정 정책 시작 팩의 *SocialAndLocalAccounts* 사용자 지정 정책이 필요합니다.
* SAML 프로토콜에 대 한 기본적인 이해와 응용 프로그램의 SAML 구현에 대해 잘 알고 있어야 합니다.
* SAML 응용 프로그램으로 구성 된 웹 응용 프로그램입니다. 이 자습서에서는 Microsoft에서 제공하는 [SAML 테스트 애플리케이션][samltest]을 사용할 수 있습니다.

## <a name="components"></a>구성 요소

이 시나리오에는 다음과 같은 세 가지 주요 구성 요소가 필요합니다.

* Saml **응용 프로그램** 은 saml 인증 요청을 전송 하 고 AZURE AD B2C에서 saml 응답을 수신, 디코드 및 확인 하는 기능을 제공 합니다. SAML 응용 프로그램은 신뢰 당사자 응용 프로그램 또는 서비스 공급자 라고도 합니다.
* SAML 응용 프로그램의 공개적으로 사용 가능한 SAML **메타 데이터 끝점** 또는 XML 문서입니다.
* [Azure AD B2C 테 넌 트](tutorial-create-tenant.md)

SAML 응용 프로그램 및 연결 된 메타 데이터 끝점이 아직 없는 경우 테스트에 사용할 수 있도록 만든이 샘플 SAML 응용 프로그램을 사용할 수 있습니다.

[SAML 테스트 애플리케이션][samltest]

## <a name="set-up-certificates"></a>인증서 설정

응용 프로그램과 Azure AD B2C 간의 트러스트 관계를 빌드하려면 두 서비스 모두 서로의 서명을 만들고 유효성을 검사할 수 있어야 합니다. Azure AD B2C 및 응용 프로그램에서 X509 인증서 구성을 구성 합니다.

**애플리케이션 인증서**

| 사용량 | 필수 | 설명 |
| --------- | -------- | ----------- |
| SAML 요청 서명  | 아니요 | 웹 앱에 저장 된 개인 키가 포함 된 인증서입니다 .이 인증서는 응용 프로그램에서 Azure AD B2C에 전송 된 SAML 요청에 서명 하는 데 사용 됩니다. 웹 앱은 SAML 메타 데이터 끝점을 통해 공개 키를 노출 해야 합니다. Azure AD B2C는 응용 프로그램 메타 데이터의 공개 키를 사용 하 여 SAML 요청 서명의 유효성을 검사 합니다.|
| SAML 어설션 암호화  | 아니요 | 웹 앱에 저장 된 개인 키가 있는 인증서입니다. 웹 앱은 SAML 메타 데이터 끝점을 통해 공개 키를 노출 해야 합니다. Azure AD B2C 공개 키를 사용 하 여 응용 프로그램에 어설션을 암호화할 수 있습니다. 응용 프로그램은 개인 키를 사용 하 여 어설션을 해독 합니다.|

**Azure AD B2C 인증서**

| 사용량 | 필수 | 설명 |
| --------- | -------- | ----------- |
| SAML 응답 서명 | 예 | Azure AD B2C에 저장 된 개인 키가 있는 인증서입니다. 이 인증서는 Azure AD B2C에서 응용 프로그램에 전송 된 SAML 응답에 서명 하는 데 사용 됩니다. 응용 프로그램은 Azure AD B2C metadata 공개 키를 읽어 SAML 응답 서명의 유효성을 검사 합니다. |

프로덕션 환경에서는 공용 인증 기관에서 발급 한 인증서를 사용 하는 것이 좋습니다. 그러나 자체 서명 된 인증서를 사용 하 여이 절차를 완료할 수도 있습니다.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>SAML 응답 서명에 대해 자체 서명 된 인증서 준비

응용 프로그램이 Azure AD B2C에서 어설션을 신뢰할 수 있도록 SAML 응답 서명 인증서를 만들어야 합니다.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>SAML 응용 프로그램에 연결 하는 정책 사용

SAML 응용 프로그램에 연결 하려면 Azure AD B2C SAML 응답을 만들 수 있어야 합니다.

사용자 지정 정책 시작 팩에서 `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 을 엽니다.

섹션을 찾아 `<ClaimsProviders>` 다음 XML 코드 조각을 추가 하 여 SAML 응답 생성기를 구현 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>SAML 응답의 IssuerUri 구성

`IssuerUri`SAML 토큰 발급자 기술 프로필에서 메타 데이터 항목의 값을 변경할 수 있습니다. 이 변경 내용은 `issuerUri` Azure AD B2C의 SAML 응답에서 반환 된 특성에 반영 됩니다. SAML 응답 유효성 검사 중에 동일한 것을 허용 하도록 응용 프로그램을 구성 해야 합니다 `issuerUri` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Azure AD B2C IdP SAML 메타 데이터에 서명 합니다 (선택 사항).

응용 프로그램에 필요한 경우 SAML IdP 메타 데이터 문서에 서명 하도록 Azure AD B2C에 지시할 수 있습니다. 이렇게 하려면 [saml 응답 서명에 대해 자체 서명 된 인증서 준비](#prepare-a-self-signed-certificate-for-saml-response-signing)에 표시 된 대로 saml IdP 메타 데이터 서명 정책 키를 생성 하 고 업로드 합니다. 그런 다음 `MetadataSigning` SAML 토큰 발급자 기술 프로필에서 메타 데이터 항목을 구성 합니다. 는 `StorageReferenceId` 정책 키 이름을 참조 해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Azure AD B2C IdP SAML response 요소에 서명 합니다 (선택 사항).

SAML 메시지에 서명 하는 데 사용할 인증서를 지정할 수 있습니다. 메시지는 `<samlp:Response>` 응용 프로그램에 전송 된 SAML 응답 내의 요소입니다.

인증서를 지정 하려면 [SAML 응답 서명에 대해 자체 서명 된 인증서 준비](#prepare-a-self-signed-certificate-for-saml-response-signing)에 표시 된 대로 정책 키를 생성 하 고 업로드 합니다. 그런 다음 `SamlMessageSigning` SAML 토큰 발급자 기술 프로필에서 메타 데이터 항목을 구성 합니다. 는 `StorageReferenceId` 정책 키 이름을 참조 해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>SAML 응답을 발급 하도록 정책 구성

이제 정책에서 SAML 응답을 만들 수 있으므로 응용 프로그램에 대 한 기본 JWT 응답 대신 SAML 응답을 발급 하도록 정책을 구성 해야 합니다.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML에 대해 구성 된 등록 또는 로그인 정책 만들기

1. 시작 팩 작업 디렉터리에 *SignUpOrSignin.xml* 파일의 복사본을 만들고 새 이름으로 저장합니다. 예: *SignUpOrSigninSAML.xml*. 이 파일은 신뢰 당사자 정책 파일이 며, 기본적으로 JWT 응답을 실행 하도록 구성 되어 있습니다.

1. 원하는 편집기에서 *SignUpOrSigninSAML.xml* 파일을 엽니다.

1. 아래와 같이 정책의 `PolicyId` 및 `PublicPolicyUri`를 _B2C_1A_signup_signin_saml_ 및 `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`로 변경합니다.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 사용자 경험의 끝에는 Azure AD B2C 단계가 포함 되어 있습니다 `SendClaims` . 이 단계에서는 토큰 발급자 기술 프로필을 참조 합니다. 기본 JWT 응답이 아닌 SAML 응답을 발급 하려면 `SendClaims` 새 Saml 토큰 발급자 기술 프로필을 참조 하는 단계를 수정 `Saml2AssertionIssuer` 합니다.

요소 바로 앞에 다음 XML 코드 조각을 추가 합니다 `<RelyingParty>` . 이 XML은 _Signu폰 로그인_ 사용자 경험에서 오케스트레이션 단계 번호 7을 덮어씁니다. 시작 팩의 다른 폴더에서 시작한 경우 또는 오케스트레이션 단계를 추가 또는 제거 하 여 사용자 경험을 사용자 지정한 경우 `order` 요소의 숫자가 토큰 발급자 단계의 사용자 경험에 지정 된 수에 해당 하는지 확인 합니다. 예를 들어 다른 스타터 팩 폴더에서 해당 단계 번호는에 대해 4,의 경우 `LocalAccounts` 6,의 경우 `SocialAccounts` 9입니다 `SocialAndLocalAccountsWithMfa` .

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

신뢰 당사자 요소는 응용 프로그램에서 사용 하는 프로토콜을 결정 합니다. 기본값은 `OpenId`입니다. `Protocol`요소는로 변경 해야 합니다 `SAML` . 출력 클레임은 SAML 어설션에 대 한 클레임 매핑을 만듭니다.

`<RelyingParty>` 요소의 전체 `<TechnicalProfile>` 요소를 다음 기술 프로필 XML로 바꿉니다. `tenant-name`을 Azure AD B2C 테넌트의 이름으로 업데이트합니다.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

최종 신뢰 당사자 정책 파일은 다음 XML 코드와 같이 표시 됩니다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> 이와 동일한 프로세스를 수행 하 여 다른 유형의 사용자 흐름 (예: 로그인, 암호 재설정 또는 프로필 편집 흐름)을 구현할 수 있습니다.

### <a name="upload-your-policy"></a>정책 업로드

변경 내용을 저장 하 고 새 **TrustFrameworkExtensions.xml** 및 **SignUpOrSigninSAML.xml** 정책 파일을 Azure Portal에 업로드 합니다.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML 메타 데이터 테스트

정책 파일이 업로드 된 후 Azure AD B2C 구성 정보를 사용 하 여 응용 프로그램에서 사용할 id 공급자의 SAML 메타 데이터 문서를 생성 합니다. SAML 메타 데이터 문서에는 로그인 및 로그 아웃 방법, 인증서 등과 같은 서비스 위치가 포함 되어 있습니다.

Azure AD B2C 정책 메타 데이터는 다음 URL에서 사용할 수 있습니다.

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

을 `<tenant-name>` Azure AD B2C 테 넌 트의 이름으로 바꾸고 `<policy-name>` ,을 정책의 이름 (ID)으로 바꿉니다. 예를 들면 다음과 같습니다.

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에서 SAML 응용 프로그램 등록

응용 프로그램을 신뢰 하는 Azure AD B2C 응용 프로그램의 메타 데이터 끝점과 같은 구성 정보를 포함 하는 Azure AD B2C 응용 프로그램 등록을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예: *SAMLApp1*.
1. **지원되는 계정 유형** 에서 **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음, `https://localhost`를 입력합니다. 나중에 응용 프로그램 등록의 매니페스트에서이 값을 수정 합니다.
1. **등록** 을 선택합니다.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Azure AD B2C에서 응용 프로그램 구성

SAML 앱의 경우 응용 프로그램 등록의 매니페스트에서 여러 속성을 구성 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서, 이전 섹션에서 만든 애플리케이션 등록으로 이동합니다.
1. **관리** 에서 **매니페스트** 를 선택 하 여 매니페스트 편집기를 연 후 다음 섹션에서 설명 하는 속성을 수정 합니다.

#### <a name="add-the-identifier"></a>식별자 추가

SAML 응용 프로그램이 Azure AD B2C에 대 한 요청을 만들면 SAML 인증 요청에는 `Issuer` 일반적으로 응용 프로그램의 메타 데이터와 같은 값을 포함 하는 특성이 포함 됩니다 `entityID` . Azure AD B2C는이 값을 사용 하 여 디렉터리에서 응용 프로그램 등록을 조회 하 고 구성을 읽습니다. 이 조회가 성공 하려면 `identifierUri` 응용 프로그램 등록의를 특성과 일치 하는 값으로 채워야 합니다 `Issuer` .

등록 매니페스트에서 `identifierURIs` 매개 변수를 찾아 적절 한 값을 추가 합니다. 이 값은 응용 프로그램의 EntityId에 대 한 SAML 인증 요청 및 `entityID` 응용 프로그램의 메타 데이터에 있는 값에 구성 된 값과 동일 합니다.

다음 예제에서는 `entityID` SAML 메타 데이터의를 보여 줍니다.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`속성은 도메인에서 url만을 허용 합니다 `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하 여 응용 프로그램의 메타 데이터 공유

에서 응용 프로그램 등록을 로드 한 후에는 `identifierUri` 응용 프로그램의 메타 데이터를 사용 하 여 SAML 인증 요청의 유효성을 검사 하 고 응답 방법을 결정 Azure AD B2C.

응용 프로그램에서 공개적으로 액세스할 수 있는 메타 데이터 끝점을 노출 하는 것이 좋습니다.

SAML 메타 데이터 URL과 응용 프로그램 등록 매니페스트에 *모두* 지정 된 속성이 있으면 *병합* 됩니다. 메타데이터 URL에 지정된 속성이 먼저 처리되고 우선 적용됩니다.

예를 들어 SAML 테스트 응용 프로그램을 사용 하 여 응용 프로그램 매니페스트에서에 대해 다음 값을 사용 합니다 `samlMetadataUrl` .

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>어설션 소비자 URL을 재정의 하거나 설정 합니다 (선택 사항).

Azure AD B2C에서 SAML 응답을 보낼 회신 URL을 구성할 수 있습니다. 응용 프로그램 매니페스트 내에서 회신 Url을 구성할 수 있습니다. 이 구성은 응용 프로그램에서 공개적으로 액세스할 수 있는 메타 데이터 끝점을 노출 하지 않는 경우에 유용 합니다.

SAML 응용 프로그램의 회신 URL은 응용 프로그램에서 SAML 응답을 받을 것으로 예상 하는 끝점입니다. 응용 프로그램은 일반적으로 `AssertionConsumerServiceUrl` 아래와 같이 특성 아래의 메타 데이터 문서에서이 URL을 제공 합니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

특성에 제공 된 메타 데이터를 재정의 `AssertionConsumerServiceUrl` 하거나 url이 메타 데이터 문서에 없는 경우 속성의 매니페스트에서 url을 구성할 수 있습니다 `replyUrlsWithType` . 는 `BindingType` 로 설정 됩니다 `HTTP POST` .

SAML 테스트 응용 프로그램을 예로 사용 하 여 `url` 의 속성을 `replyUrlsWithType` 다음 JSON 코드 조각에 표시 된 값으로 설정 합니다.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>로그 아웃 URL을 재정의 하거나 설정 합니다 (선택 사항).

로그 아웃 요청 후 Azure AD B2C 사용자를 전송 하는 로그 아웃 URL을 구성할 수 있습니다. 응용 프로그램 매니페스트 내에서 회신 Url을 구성할 수 있습니다.

특성에 제공 된 메타 데이터를 재정의 하려는 경우 `SingleLogoutService` 또는 메타 데이터 문서에 URL이 없으면 매니페스트에서 속성 아래에 있는 메타 데이터를 구성할 수 있습니다 `Logout` . 는 `BindingType` 로 설정 됩니다 `Http-Redirect` .

응용 프로그램은 일반적으로 `AssertionConsumerServiceUrl` 아래와 같이 특성 아래의 메타 데이터 문서에서이 URL을 제공 합니다.

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

SAML 테스트 응용 프로그램을 사용 하 여 다음과 같이 설정 된 채로 둡니다 `logoutUrl` `https://samltestapp2.azurewebsites.net/logout` .

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> 속성을 통해 응용 프로그램의 메타 데이터 끝점을 채우지 않고 응용 프로그램 매니페스트에서 회신 URL 및 로그 아웃 URL을 구성 하도록 선택 하는 경우 `samlMetadataUrl` Azure AD B2C는 saml 요청 서명의 유효성을 검사 하지 않으며 saml 응답을 암호화 하지도 않습니다.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>SAML 응용 프로그램에서 Azure AD B2C를 SAML IdP 구성

마지막 단계는 SAML 응용 프로그램에서 SAML IdP Azure AD B2C를 사용 하도록 설정 하는 것입니다. 각 응용 프로그램은 서로 다르며 단계가 달라 집니다. 자세한 내용은 앱 설명서를 참조하세요.

메타 데이터는 응용 프로그램에서 *정적 메타* 데이터 또는 *동적 메타 데이터로* 구성할 수 있습니다. 정적 모드의 경우 Azure AD B2C 정책 메타 데이터에서 메타 데이터의 전체 또는 일부를 복사 합니다. 동적 모드에서 메타 데이터에 대 한 URL을 제공 하 고 응용 프로그램에서 메타 데이터를 동적으로 읽을 수 있도록 합니다.

일반적으로 다음 중 일부 또는 전부가 필요합니다.

* **메타 데이터**: 형식을 사용 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` 합니다.
* **발급자**: SAML 요청 `issuer` 값은 `identifierUris` 응용 프로그램 등록 매니페스트의 요소에 구성 된 uri 중 하 나와 일치 해야 합니다. SAML 요청 `issuer` 이름이 요소에 없으면 `identifierUris` [응용 프로그램 등록 매니페스트에 추가](#add-the-identifier)합니다. 예: `https://contoso.onmicrosoft.com/app-name` 
* **로그인 Url/saml 끝점/Saml Url**: XML 요소에 대 한 Azure AD B2C SAML 정책 메타 데이터 파일의 값을 확인 합니다 `<SingleSignOnService>` .
* **인증서**:이 인증서는 *B2C_1A_SamlIdpCert* 되지만 개인 키가 없습니다. 인증서의 퍼블릭 키를 가져오려면:

    1. 위에서 지정한 메타데이터 URL로 이동합니다.
    1. `<X509Certificate>` 요소에서 값을 복사합니다.
    1. 텍스트 파일에 붙여넣습니다.
    1. 텍스트 파일을 *.cer* 파일로 저장합니다.

### <a name="test-with-the-saml-test-app"></a>SAML 테스트 앱을 사용 하 여 테스트

[SAML 테스트 응용 프로그램][samltest] 을 사용 하 여 구성을 테스트할 수 있습니다.

* 테 넌 트 이름을 업데이트 합니다.
* 정책 이름 (예: *B2C_1A_signup_signin_saml* 을 업데이트 합니다.
* 이 발급자 URI를 지정 합니다. `identifierUris`응용 프로그램 등록 매니페스트의 요소에 있는 uri 중 하나를 사용 합니다 (예:) `https://contoso.onmicrosoft.com/app-name` .

**로그인** 을 선택하면 사용자 로그인 화면이 표시됩니다. 로그인 하면 샘플 응용 프로그램에 대 한 SAML 응답이 다시 발급 됩니다.

## <a name="supported-and-unsupported-saml-modalities"></a>지원되는 SAML 형식 및 지원되지 않는 SAML 형식

사용자 고유의 메타 데이터 끝점을 통해 지원 되는 SAML 응용 프로그램 시나리오는 다음과 같습니다.

* 응용 프로그램/서비스 사용자 개체의 로그 아웃 URL에 대 한 여러 로그 아웃 url 또는 POST 바인딩입니다.
* 응용 프로그램/서비스 주체 개체에서 RP (신뢰 당사자) 요청을 확인할 서명 키를 지정 합니다.
* 응용 프로그램/서비스 주체 개체에 토큰 암호화 키를 지정 합니다.
* Id 공급자가 시작 된 로그온으로, id 공급자가 Azure AD B2C 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C GitHub 커뮤니티 리포지토리](https://github.com/azure-ad-b2c/saml-sp-tester)에서 SAML 테스트 웹앱을 가져옵니다.
- [Azure AD B2C에서 SAML 응용 프로그램을 등록 하는 옵션](saml-service-provider-options.md) 을 참조 하세요.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
