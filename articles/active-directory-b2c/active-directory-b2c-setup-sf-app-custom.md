---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하여 Salesforce SAML 공급자 추가 | Microsoft Docs"
description: "Azure Active Directory B2C 사용자 지정 정책에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dec042efe30b80d68b3f200a4aad4fd5d24cd158
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: SAML을 통해 Salesforce 계정을 사용하여 로그인

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Salesforce 조직의 사용자가 로그인할 수 있도록 허용하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-ad-b2c-setup"></a>Azure AD B2C 설정

[사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)하는 방법을 설명하는 단계를 모두 완료했는지 확인합니다.

다음 내용이 포함됩니다.

1. Azure AD B2C 테넌트 만들기
1. Azure AD B2C 응용 프로그램 만들기
1. 두 개의 정책 엔진 응용 프로그램 등록
1. 키 설정
1. 시작 팩 설정

### <a name="salesforce-setup"></a>Salesforce 설정

이 자습서에서는 사용자가 이미 다음을 보유하고 있다고 가정합니다.

1. Salesforce 계정 등록. [Developer Edition 평가판](https://developer.salesforce.com/signup)에 등록할 수 있습니다.
1. Salesforce 조직의 [내 도메인을 설정](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)합니다.

## <a name="configure-salesforce-to-allow-users-to-federate"></a>사용자 페더레이션을 허용하도록 Salesforce 구성

Azure AD B2C가 Salesforce와 통신하도록 지원하려면 Salesforce 메타데이터 URL을 얻어야 합니다.

### <a name="enable-salesforce-as-an-identity-provider"></a>Salesforce를 ID 공급자로 사용

>[!NOTE]
> 이 자습서는 사용자가 [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ)를 사용한다고 가정합니다.

1. [Salesforce에 로그인](https://login.salesforce.com/) 
1. 왼쪽 메뉴의 **설정** 아래에서 **ID**를 확장하고 **ID 공급자**를 클릭합니다.
1. **Enable Identity Provider**(ID 공급자 사용)를 클릭합니다.
1. Salesforce가 Azure AD B2C와 통신할 때 사용할 **인증서를 선택**하고 **저장**을 클릭합니다. 기본 인증서를 사용할 수 있습니다.

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce에서 연결된 앱 만들기

1. **ID 공급자** 페이지에서 **서비스 공급자** 섹션으로 스크롤합니다.
1. **Service Providers are now created via Connected Apps. Click here.**(서비스 공급자가 연결된 앱을 통해 생성됩니다. 여기를 클릭하세요.)를 클릭합니다.
1. 연결된 앱에 대한 필수 **기본 정보**를 제공합니다.
1. **웹앱 설정** 섹션에서 **SAML 사용**을 선택합니다.
1. **엔터티 ID** 필드에 다음 URL을 입력하고 `tenantName`을 적절하게 바꿉니다.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
1. **ACS URL** 필드에 다음 URL을 입력하고 `tenantName`을 적절하게 바꿉니다.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
1. 다른 모든 설정은 기본값으로 유지합니다.
1. 맨 아래까지 스크롤한 다음 **저장** 단추를 클릭합니다.

### <a name="get-the-metadata-url"></a>메타데이터 URL 가져오기

1. 연결된 앱의 개요 페이지에서 **관리**를 클릭합니다.
1. **메타데이터 검색 끝점**을 복사하고 나중을 위해 저장합니다.

### <a name="enable-salesforce-users-to-federate"></a>Salesforce 사용자가 페더레이션하도록 허용

1. 연결된 앱의 "관리" 페이지에서 **프로필** 섹션까지 아래로 스크롤합니다.
1. **프로필 관리**를 클릭합니다.
1. Azure AD B2C와 페더레이션할 수 있도록 허용할 프로필(또는 사용자 그룹)을 선택합니다. 시스템 관리자는 Salesforce 계정을 사용하여 페더레이션할 수 있도록 **시스템 관리자** 확인란을 선택해야 합니다.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Azure AD B2C에 대한 서명 인증서 생성

Azure AD B2C는 Salesforce에 전송되는 요청에 서명해야 합니다. 서명 인증서를 생성하려면 PowerShell을 열고 다음 명령을 실행합니다.

**맨 위 두 줄에서 테넌트 이름 및 암호를 업데이트해야 합니다.**

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Azure AD B2C에 SAML 서명 인증서 추가

서명 인증서를 Azure AD B2C 테넌트에 업로드해야 합니다. 다음을 수행합니다.

1. Azure AD B2C 테넌트로 이동하고 B2C **설정 > ID 경험 프레임워크 > 정책 키**를 엽니다.
1. **+추가**를 클릭합니다.
    * **옵션 > 업로드**
    * **이름**을 입력합니다(예: `SAMLSigningCert`). 키의 이름에 B2C_1A_ 접두사가 자동으로 추가됩니다.
    * **업로드 파일 제어**를 사용하여 인증서를 선택합니다.
    * PowerShell 스크립트에서 설정한 인증서 암호를 입력합니다.
1. **만들기**를 클릭합니다.
1. 키(예: `B2C_1A_SAMLSigningCert`)가 생성되었는지 확인합니다. B2C_1A_를 포함한 전체 이름을 기록해 두세요. 나중에 정책에서 참조할 때 사용하게 될 것입니다.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>기본 정책에서 Salesforce SAML 클레임 공급자 만들기

사용자가 Salesforce를 사용하여 로그인할 수 있도록 허용하려면 Salesforce를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신할 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 *클레임*을 *제공*하여 특정 사용자가 인증했는지 확인합니다. 정책의 확장 파일에서 Salesforce에 대한 `<ClaimsProvider>`를 추가하여 수행할 수 있습니다.

1. 작업 디렉터리(TrustFrameworkExtensions.xml)에서 확장 파일을 엽니다.
1. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
1. 다음과 같이 새로운 `<ClaimsProvider>`을 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

`<ClaimsProvider>` 노드 아래에서:

* `<Domain>` 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
* `<DisplayName>` 값을 클레임 공급자 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Salesforce에서 SAML 토큰을 가져오기 위해 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. 이 작업은 `<ClaimsProvider>`의 `<TechnicalProfile>` 요소 내부에서 수행됩니다.

1. `<TechnicalProfile>` 노드의 ID를 업데이트합니다. 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 이 ID를 사용합니다.
1. `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. `<Description>` 값을 업데이트합니다.
1. Salesforce는 SAML 2.0 프로토콜을 사용하므로 `<Protocol>`은 "SAML2"가 됩니다.

위의 XML에서 `<Metadata>` 섹션을 업데이트하여 특정 Salesforce 계정의 구성 설정을 반영해야 합니다. XML에서 다음과 같이 메타데이터 값을 업데이트합니다.

1. `<Item Key="PartnerEntity">` 값을 앞에서 복사한 Salesforce 메타데이터 URL로 업데이트합니다. `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml` 형식입니다.

1. 위 XML의 `<CryptographicKeys>` 섹션에서 두 `StorageReferenceId`의 값을 모두 서명 인증서(예: B2C_1A_SAMLSigningCert)의 키 이름으로 업데이트합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

이제 Azure AD B2C에서 Salesforce와 통신하는 방법을 알 수 있도록 정책이 구성되었습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다. 이렇게 하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **모든 정책** 블레이드로 이동합니다.
1. **있는 경우 정책 덮어쓰기** 확인란을 선택합니다.
1. 확장 파일(TrustFrameworkExtensions.xml)을 업로드하고 유효성 검사가 실패하지 않도록 합니다.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>사용자 경험에 Salesforce SAML 클레임 공급자 등록

이제 사용자 경험 중 하나에 Salesforce SAML ID 공급자를 추가해야 합니다. 이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 그렇게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자가 포함되도록 수정합니다.

1. 정책의 기본 파일 열기(예: TrustFrameworkBase.xml)
1. Id=”SignUpOrSignIn”을 사용하여 `<UserJourneys>` 요소를 찾고 전체 `<UserJourney>`를 복사합니다.
1. 확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 전체 `<UserJourney>`을 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
1. 새 `<UserJourney>` ID의 이름을 바꿉니다(즉, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>"단추" 표시

`<ClaimsProviderSelection>` 요소는 등록/로그인 화면에서 ID 공급자 단추를 사용하는 것과 유사합니다. Salesforce에 `<ClaimsProviderSelection>` 요소를 추가하여 사용자가 페이지를 열면 새 단추를 표시합니다. 다음을 수행합니다.

1. 방금 만든 `<UserJourney>`에서 `Order="1"`와 `<OrchestrationStep>`을 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. `TargetClaimsExchangeId`을 적절한 값으로 설정합니다. 다른 값과 동일한 규칙인 *\[ClaimProviderName\]Exchange*를 사용하는 것이 좋습니다.

### <a name="link-the-button-to-an-action"></a>작업에 "단추" 연결

이제 "단추"를 만들었으므로 작업에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Salesforce와 통신하여 SAML 토큰을 수신할 수 있게 됩니다. 이렇게 하려면 Salesforce SAML 클레임 공급자의 기술 프로필을 연결합니다.

1. `<UserJourney>` 노드에서 `Order="2"`와 `<OrchestrationStep>` 찾기
1. 다음을 추가합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. `Id`을 위에 `TargetClaimsExchangeId`와 동일한 값으로 업데이트합니다.
1. `TechnicalProfileReferenceId`을 이전에 만든 기술 프로필의 `Id`로 업데이트합니다(예: ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>업데이트된 확장 파일 업로드

확장 파일을 수정하는 작업을 완료했습니다. 이 파일을 저장하고 업로드한 다음 모든 유효성 검사가 성공하도록 합니다.

### <a name="update-the-rp-file"></a>RP 파일 업데이트

이제 방금 만든 사용자 경험을 시작하는 RP 파일을 업데이트해야 합니다.

1. 작업 디렉터리에서 SignUpOrSignIn.xml의 복사본을 만들고 이름을 바꿉니다(예: SignUpOrSignInWithAAD.xml).
1. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값으로 업데이트합니다. 이것이 정책의 이름입니다(예: SignUpOrSignInWithAAD).
1. `<DefaultUserJourney>`에서 `ReferenceId` 특성을 수정하여 만든 새 사용자 경험의 ID와 일치시킵니다(예: SignUpOrSignUsingContoso).
1. 변경 내용을 저장하고 파일을 업로드합니다.

## <a name="testing-and-troubleshooting"></a>테스트 및 문제 해결

해당 블레이드를 열고 "지금 실행"을 클릭하여 방금 업로드한 사용자 지정 정책을 테스트합니다. 문제가 발생하는 경우 [문제를 해결](active-directory-b2c-troubleshoot-custom.md)하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 대한 피드백을 제공합니다.
