---
title: Azure AD B2C를 응용 프로그램에 SAML IdP로 구성
title-suffix: Azure AD B2C
description: 응용 프로그램(서비스 공급자)에 SAML 프로토콜 어설션을 제공하도록 Azure AD B2C를 구성하는 방법. Azure AD B2C는 SAML 응용 프로그램에 대한 단일 ID 공급자(IdP) 역할을 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051613"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에서 SAML 응용 프로그램 등록

이 문서에서는 Azure Active Directory B2C(Azure AD B2C)를 구성하여 SAML(보안 어설션 태그 언어) ID공급자(IdP)로 작동하도록 응용 프로그램에 대해 알아봅니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>시나리오 개요

Azure AD B2C를 고객 ID 및 액세스 관리 솔루션으로 사용하는 조직은 SAML 프로토콜을 사용하여 인증하도록 구성된 ID 공급자 또는 응용 프로그램과의 상호 작용이 필요할 수 있습니다.

Azure AD B2C는 다음 두 가지 방법 중 하나로 SAML 상호 운용성을 달성합니다.

* *IDP(IDP)* 역할을 하고 SAML 기반 서비스 공급자(애플리케이션)와 단일 사인온(SSO)을 달성함으로써
* *SP(서비스 공급자)* 역할을 하고 Salesforce 및 ADFS와 같은 SAML 기반 ID 공급자와 상호 작용하여

![왼쪽에 ID 공급자로 B2C를, 오른쪽에 서비스 공급자로 B2C를 다이어그램으로](media/saml-identity-provider/saml-idp-diagram-01.jpg)

SAML을 통해 두 가지 비독점 코어 시나리오를 요약합니다.

| 시나리오 | Azure AD B2C 역할 | 방법 |
| -------- | ----------------- | ------- |
| 내 응용 프로그램에서 SAML 어설션이 인증을 완료할 것으로 예상합니다. | **Azure AD B2C가 ID 공급자(IdP) 역할을 합니다.**<br />Azure AD B2C는 응용 프로그램에 대한 SAML IdP 역할을 합니다. | 이 문서의 내용: |
| 내 사용자는 ADFS, Salesforce 또는 Shibboleth와 같은 SAML 호환 ID 공급자를 사용한 단일 사인온이 필요합니다.  | **Azure AD B2C가 SP(서비스 공급자) 역할을 합니다.**<br />Azure AD B2C는 SAML ID 공급자에 연결할 때 서비스 공급자 역할을 합니다. 응용 프로그램과 SAML ID 공급자 간의 페더레이션 프록시입니다.  | <ul><li>[사용자 지정 정책을 사용하여 ADFS를 SAML IdP로 사용하여 로그인 설정](identity-provider-adfs2016-custom.md)</li><li>[사용자 지정 정책을 사용하여 Salesforce SAML 공급자로 로그인 설정](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C에서 사용자 지정 정책 으로 시작하기 의](custom-policy-get-started.md)단계를 완료합니다. 이 문서에서 설명하는 사용자 지정 정책 시작 팩의 *SocialAndLocalAccounts* 사용자 지정 정책이 필요합니다.
* SAML(보안 어설션 태그 언어) 프로토콜에 대한 기본 이해입니다.
* SAML 서비스 공급자(SP)로 구성된 웹 응용 프로그램입니다. 이 자습서에서는 제공하는 [SAML 테스트 응용 프로그램을][samltest] 사용할 수 있습니다.

## <a name="components-of-the-solution"></a>솔루션의 구성 요소

이 시나리오에는 세 가지 주요 구성 요소가 필요합니다.

* Azure AD B2C에서 SAML 어설션을 보내고 수신, 디코딩 및 응답할 수 있는 SAML **서비스 공급자입니다.** 이것은 또한 의존 당사자로 알려져 있습니다.
* 서비스 공급자에 대해 공개적으로 사용할 수 있는 SAML **메타데이터 끝점입니다.**
* [Azure AD B2C 테넌트](tutorial-create-tenant.md)

아직 SAML 서비스 공급자와 연결된 메타데이터 끝점이 없는 경우 테스트에 사용할 수 있도록 한 이 샘플 SAML 응용 프로그램을 사용할 수 있습니다.

[SAML 테스트 응용 프로그램][samltest]

## <a name="1-set-up-certificates"></a>1. 인증서 설정

서비스 공급자와 Azure AD B2C 간의 트러스트 관계를 구축하려면 웹 앱 X509 인증서를 제공해야 합니다.

* **서비스 공급자 인증서**
  * 웹 앱에 개인 키가 저장된 인증서입니다. 이 인증서는 서비스 공급자가 Azure AD B2C로 전송된 SAML 요청에 서명하는 데 사용됩니다. Azure AD B2C는 서비스 공급자 메타데이터에서 공개 키를 읽고 서명의 유효성을 검사합니다.
  * (선택 사항) 웹 앱에 개인 키가 저장된 인증서입니다. Azure AD B2C는 SAML 어설션을 암호화하기 위해 서비스 공급자 메타데이터에서 공개 키를 읽습니다. 그런 다음 서비스 공급자는 개인 키를 사용하여 어설션을 해독합니다.
* **Azure AD B2C 인증서**
  * Azure AD B2C에서 개인 키가 있는 인증서입니다. 이 인증서는 Azure AD B2C에서 서비스 공급자에게 보낸 SAML 응답에 서명하는 데 사용됩니다. 서비스 공급자는 Azure AD B2C 메타데이터 공개 키를 읽고 SAML 응답의 서명을 확인합니다.

공용 인증 기관에서 발급한 인증서를 사용하거나 이 자습서에서는 자체 서명된 인증서를 사용할 수 있습니다.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 자체 서명된 인증서 준비

인증서가 아직 없는 경우 이 자습서에 대해 자체 서명된 인증서를 사용할 수 있습니다. Windows에서 PowerShell의 [새 자체 서명인증서](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성할 수 있습니다.

1. 이 PowerShell 명령을 실행하여 자체 서명된 인증서를 생성합니다. 응용 `-Subject` 프로그램 및 Azure AD B2C 테넌트 이름에 맞게 인수를 수정합니다. 인증서에 `-NotAfter` 대한 다른 만료를 지정하도록 날짜를 조정할 수도 있습니다.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. 열기 **사용자 인증서 현재** > **사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com*
1. **모든** > **작업** > **내보내기에** > 인증서 선택
1. **예** > **다음** > **예 선택, 개인 키** > **내보내기 다음**
1. **파일 형식 내보내기의** 기본값 수락
1. 인증서에 대한 암호 제공

### <a name="12-upload-the-certificate"></a>1.2 인증서 업로드

다음으로 SamL 어설션 및 응답 서명 인증서를 Azure AD B2C에 업로드합니다.

1. [Azure 포털에](https://portal.azure.com) 로그인하고 Azure AD B2C 테넌트를 찾아봅습니다.
1. **정책에서** **ID 환경 프레임워크를** 선택한 다음 **정책 키를**선택합니다.
1. **추가를**선택한 다음 **옵션** > **업로드**를 선택합니다.
1. **예를**들어 *SamlIdpCert*. 키의 이름에 *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. 업로드 파일 컨트롤을 사용하여 인증서를 업로드합니다.
1. 인증서의 암호를 입력합니다.
1. **만들기**를 선택합니다.
1. 키가 예상대로 나타나는지 확인합니다. 예를 들어, *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. 정책 준비

### <a name="21-create-the-saml-token-issuer"></a>2.1 SAML 토큰 발급자 만들기

이제 [SAML 토큰 발급자](saml-issuer-technical-profile.md) 및 [SAML 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) 기술 프로필을 사용하여 테넌트가 SAML 토큰을 발급할 수 있는 기능을 추가합니다.

사용자 `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** 지정 정책 시작 팩에서 엽니다.

`<ClaimsProviders>` 섹션을 찾아 다음 XML 스니펫을 추가합니다.

`IssuerUri` 메타데이터의 값을 변경할 수 있습니다. Azure AD B2C의 SAML 응답에서 반환되는 발급자 URI입니다. SAML 어설션 유효성 검사 중에 발급자 URI를 수락하도록 신뢰할 수 있는 파티 응용 프로그램을 구성해야 합니다.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. SAML 의존 파티 정책 추가

테넌트가 SAML 어설션을 발행할 수 있으므로 SAML 사용 파티 정책을 만들고 JWT 대신 SAML 어설션을 발행하도록 사용자 이동을 수정해야 합니다.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 가입 또는 로그인 정책 만들기

1. 시작 팩 작업 디렉토리에 *SignUpOrSignin.xml* 파일의 복사본을 만들고 새 이름으로 저장합니다. 예를 들어, *가입OrSigninSAML.xml*. 이것은 당신의 의존 파티 정책 파일입니다.

1. 선호하는 편집기에서 *SignUpOrSigninSAML.xml* 파일을 엽니다.

1. 아래와 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` 같이 B2C_1A_signup_signin_saml 정책의 및 변경합니다. _B2C_1A_signup_signin_saml_ `PolicyId` `PublicPolicyUri`

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 요소 바로 앞에 다음 XML `<RelyingParty>` 스니펫을 추가합니다. 이 XML은 _SignUpOrSignIn_ 사용자 여정의 오케스트레이션 단계 7을 덮어씁니다. 시작 팩의 다른 폴더에서 시작하거나 오케스트레이션 단계를 추가하거나 제거하여 사용자 여정을 사용자 `order` 지정한 경우 토큰 발급자 단계에 대한 사용자 여정에 지정된 숫자(요소)가 정렬되어 있는지 확인합니다(예: `LocalAccounts`다른 `SocialAccounts` 스타터 팩 `SocialAndLocalAccountsWithMfa`폴더의 경우 4단계 , 6에 대 한 및 9).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 요소의 `<TechnicalProfile>` 전체 요소를 `<RelyingParty>` 다음 기술 프로필 XML로 바꿉습니다.

    ```XML
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

1. Azure `tenant-name` AD B2C 테넌트의 이름으로 업데이트합니다.

최종 의존 파티 정책 파일은 다음과 같아야 합니다.

```XML
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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 정책 메타데이터 업로드 및 테스트

변경 내용을 저장하고 새 정책 파일을 업로드합니다. 두 정책(확장명 및 의존 당사자 파일)을 모두 업로드한 후 웹 브라우저를 열고 정책 메타데이터로 이동합니다.

Azure AD B2C 정책 IDP 메타데이터는 SAML ID 공급자의 구성을 노출하기 위해 SAML 프로토콜에 사용되는 정보입니다. 메타데이터는 로그인/로그아웃, 인증서, 로그인 방법 등과 같은 서비스의 위치를 정의합니다. Azure AD B2C 정책 메타데이터는 다음 URL에서 사용할 수 있습니다. Azure `tenant-name` AD B2C 테넌트의 이름과 `policy-name` 정책의 이름(ID)으로 바꿉니다.

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

이제 사용자 지정 정책 및 Azure AD B2C 테넌트가 준비되었습니다. 다음으로 Azure AD B2C에서 응용 프로그램 등록을 만듭니다.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Azure AD B2C 디렉터리에서 설치 응용 프로그램

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Azure 활성 디렉터리에서 응용 프로그램 등록

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어, *SAMLApp1*.
1. **지원되는 계정 유형에서** **이 조직 디렉터리에서 계정만** 선택합니다.
1. **URI 리디렉션에서** **웹을**선택한 다음 `https://localhost`을 입력합니다. 응용 프로그램 등록의 매니페스트에서 나중에 이 값을 수정합니다.
1. **등록을**선택합니다.

### <a name="42-update-the-app-manifest"></a>4.2 앱 매니페스트 업데이트

SAML 앱의 경우 응용 프로그램 등록 의 매니페스트에서 구성해야 하는 몇 가지 속성이 있습니다.

1. Azure [포털에서](https://portal.azure.com)이전 섹션에서 만든 응용 프로그램 등록으로 이동합니다.
1. **관리**에서 **매니페스트를** 선택하여 매니페스트 편집기를 엽니다. 다음 섹션에서 여러 속성을 수정합니다.

#### <a name="identifieruris"></a>identifierUris

는 `identifierUris` Azure AD B2C 테넌트 내에서 웹 앱을 고유하게 식별하는 사용자 정의 URI를 포함하는 문자열 모음입니다. 서비스 공급자는 SAML 요청의 `Issuer` 요소에 이 값을 설정해야 합니다.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

이 속성은 서비스 공급자의 공개적으로 사용 가능한 메타데이터 URL을 나타냅니다. 메타데이터 URL은 익명으로 액세스할 수 있는 모든 끝점(예: Blob storage)에 업로드된 메타데이터 파일을 가리킬 수 있습니다.

메타데이터는 SAML 프로토콜에서 서비스 공급자와 같은 SAML 파티의 구성을 노출하는 데 사용되는 정보입니다. 메타데이터는 로그인 및 로그아웃, 인증서, 로그인 방법 등과 같은 서비스의 위치를 정의합니다. Azure AD B2C는 서비스 공급자 메타데이터를 읽고 그에 따라 작동합니다. 메타데이터가 필요하지 않습니다. 앱 매니페스트에서 직접 회신 URI 및 로그아웃 URI와 같은 일부 특성을 지정할 수도 있습니다.

SAML 메타데이터 URL과 응용 프로그램 등록의 매니페스트에 *모두* 지정된 속성이 있으면 **병합됩니다.** 메타데이터 URL에 지정된 속성이 먼저 처리되고 우선합니다.

SAML 테스트 응용 프로그램을 사용하는 이 자습서의 경우 `samlMetadataUrl`다음 값에 대해 다음 값을 사용합니다.

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>회신UrlsWithType (선택 사항)

메타데이터 URI를 제공하지 않는 경우 회신 URL을 명시적으로 지정할 수 있습니다. 이 선택적 `AssertionConsumerServiceUrl` 속성은`SingleSignOnService` (서비스 공급자 메타데이터의 `BindingType` URL)을 `HTTP POST`나타내며.

서비스 공급자 메타데이터를 사용하지 않고 응용 프로그램 매니페스트에서 회신 URL 및 로그아웃 URL을 구성하도록 선택한 경우 Azure AD B2C는 SAML 요청 시그니처의 유효성을 검사하지 않으며 SAML 응답을 암호화하지 않습니다.

SAML 테스트 응용 프로그램을 사용하는 이 자습서의 `url` 경우 `replyUrlsWithType` 다음 JSON 코드 조각에 표시된 값으로 속성을 설정합니다.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>로그아웃Url(선택 사항)

이 `Logout` 선택적 속성은`SingleLogoutService` URL(의존 당사자 메타데이터의 URL)을 나타내며, 에 `BindingType` 대한 정보는 `Http-Redirect`로 간주됩니다.

SAML 테스트 응용 프로그램을 사용하는 이 `logoutUrl` 자습서의 `https://samltestapp2.azurewebsites.net/logout`경우 다음으로 설정된 상태로 둡니다.

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 응용 프로그램 코드 업데이트

마지막 단계는 SamL 의존 자 응용 프로그램에서 Azure AD B2C를 SAML IdP로 사용하도록 설정하는 것입니다. 응용 프로그램마다 다르며 단계는 다양합니다. 자세한 내용은 앱 설명서를 참조하십시오.

일반적으로 다음과 같은 일부 또는 전부가 필요합니다.

* **메타데이터**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **발급자**: 메타데이터 파일에 entityID사용
* **로그인 URL/SAML 끝점/SAML URL**: 메타데이터 파일의 값 확인
* **인증서**: 이것은 *B2C_1A_SamlIdpCert*있지만 개인 키가 없습니다. 인증서의 공개 키를 얻으려면 다음을 수행하십시오.

    1. 위에 지정된 메타데이터 URL로 이동합니다.
    1. 요소의 값을 `<X509Certificate>` 복사합니다.
    1. 텍스트 파일에 붙여넣습니다.
    1. 텍스트 파일을 *.cer* 파일로 저장합니다.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 SAML 테스트 앱으로 테스트(선택 사항)

우리의 [SAML 테스트 응용 프로그램을][samltest]사용하여이 자습서를 완료하려면 :

* 테넌트 이름 업데이트
* 정책 이름(예: *B2C_1A_signup_signin_saml)* 업데이트
* 이 발급자 URI를 지정합니다.`https://contoso.onmicrosoft.com/app-name`

**로그인을** 선택하면 사용자 로그인 화면이 표시됩니다. 로그인하면 SAML 어설션이 샘플 응용 프로그램에 다시 발행됩니다.

## <a name="sample-policy"></a>샘플 정책

SAML 테스트 앱으로 테스트하는 데 사용할 수 있는 완벽한 샘플 정책을 제공합니다.

1. [SAML-SP 시작 로그인 샘플 정책](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) 다운로드
1. 테넌트 이름과 일치하도록 업데이트(예: `TenantId` *contoso.b2clogin.com*
1. B2C_1A_SAML2_signup_signin 정책 이름 *B2C_1A_SAML2_signup_signin* 유지

## <a name="supported-and-unsupported-saml-modalities"></a>지원 및 지원되지 않는 SAML 양식

다음 SAML 의존 자(RP) 시나리오는 사용자 고유의 메타데이터 끝점을 통해 지원됩니다.

* 응용 프로그램/서비스 주 개체의 로그아웃 URL에 대한 여러 로그아웃 URL 또는 POST 바인딩입니다.
* 응용 프로그램/서비스 주 개체에서 RP 요청을 확인 하려면 서명 키를 지정 합니다.
* 응용 프로그램/서비스 주 개체에 토큰 암호화 키를 지정합니다.
* ID 공급자가 시작한 로그인은 현재 미리 보기 릴리스에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- SAML 프로토콜에 대한 자세한 내용은 [OASIS 웹 사이트에서](https://www.oasis-open.org/)확인할 수 있습니다.
- [Azure AD B2C GitHub 커뮤니티 리포지토리에서](https://github.com/azure-ad-b2c/saml-sp-tester)SAML 테스트 웹 앱을 가져옵니다.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
