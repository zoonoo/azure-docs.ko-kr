---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하여 Salesforce SAML 공급자 추가 | Microsoft Docs"
description: "Azure Active Directory B2C 사용자 지정 정책에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 1d97c75f3130ea6fdacbc6335b6e70677b4d226e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: SAML을 통해 Salesforce 계정을 사용하여 로그인

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Salesforce 조직의 사용자에게 로그인할 수 있도록 하는 방법을 설명합니다.

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

## <a name="get-the-salesforce-saml-metadata"></a>Salesforce SAML 메타데이터 가져오기
>[!NOTE]
> 이 자습서는 사용자가 [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ)를 사용한다고 가정합니다.

1. [Salesforce에 로그인](https://login.salesforce.com/) 
1. 왼쪽 메뉴의 **설정** 아래에서 **ID**를 확장하고 **ID 공급자**를 클릭합니다.
1. **Enable Identity Provider**(ID 공급자 사용)를 클릭합니다.
1. Salesforce가 Azure AD B2C와 통신할 때 사용할 **인증서를 선택**하고 **저장**을 클릭합니다. 기본 인증서를 사용할 수 있습니다.
1. 이제 제공되는 **메타데이터 다운로드** 단추를 클릭하고 이후 단계에서 사용할 메타데이터를 저장합니다.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>SAML 서명 인증서를 Azure AD B2C에 추가
SAML 요청에 서명할 때 사용할 SAML 인증서를 Azure AD B2C 테넌트에 업로드해야 합니다. 다음을 수행합니다.

1. Azure AD B2C 테넌트로 이동하고 B2C **설정 > ID 경험 프레임워크 > 정책 키**를 엽니다.
1. **+추가**를 클릭합니다.
1. 옵션:
 * **옵션 > 업로드**
 * **이름**: > `ContosoIdpSamlCert`를 선택합니다.  키의 이름에 B2C_1A_ 접두사가 자동으로 추가됩니다. B2C_1A_를 포함한 전체 이름을 기록해 두세요. 나중에 정책에서 참조할 때 사용하게 될 것입니다.
 * **파일 업로드 제어**를 사용하여 인증서를 선택하고 해당하는 경우 인증서의 암호를 입력합니다.
1. **만들기**
1. `B2C_1A_ContosoIdpSamlCert` 키가 생성되었는지 확인합니다.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>기본 정책에서 Salesforce SAML 클레임 공급자 만들기

사용자가 Salesforce를 사용하여 로그인할 수 있으려면 Salesforce를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신할 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 *클레임*을 *제공*하여 특정 사용자가 인증했는지 확인합니다. 정책의 확장 파일에서 Salesforce에 대한 `<ClaimsProvider>`를 추가하여 수행할 수 있습니다.

1. 작업 디렉터리(TrustFrameworkExtensions.xml)에서 확장 파일을 엽니다.
1. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
1. 다음과 같이 새로운 `<ClaimsProvider>`을 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
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

1. `<ClaimsProvider>` 노드 아래에서 `<Domain>` 값을 다른 ID 공급자를 구분하기 위해 사용할 수 있는 고유한 값으로 업데이트합니다.
1. `<ClaimsProvider>` 노드 아래에서 `<DisplayName>` 값을 클레임 공급자에게 친숙한 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Salesforce에서 SAML 토큰을 가져오기 위해 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. 이 작업은 `<ClaimsProvider>`의 `<TechnicalProfile>` 요소 내부에서 수행됩니다.

1. `<TechnicalProfile>` 노드의 ID를 업데이트합니다. 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 이 ID를 사용합니다.
1. `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. `<Description>` 값을 업데이트합니다.
1. Azure AD는 OpenID Connect 프로토콜을 사용하므로 `<Protocol>`이 "SAML2"이어야 합니다.

위의 XML에서 `<Metadata>` 섹션을 업데이트하여 특정 Azure AD 테넌트의 구성 설정을 반영해야 합니다. XML에서 다음과 같이 메타데이터 값을 업데이트합니다.

1. Salesforce에서 다운로드한 Metadata.xml의 내용과 함께 `<Item Key="PartnerEntity">`의 갑을 업데이트합니다. **이것을 <![CDATA[ …metadata… ]]>로 캡슐화해야 합니다**.

1. 앞서 XML의 `<CryptographicKeys>` 섹션에서 두 `StorageReferenceId` 값을 정의한 인증서 ID(예: ContosoSalesforceCert)로 업데이트합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다. 이렇게 하려면 다음을 수행합니다.

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

## <a name="create-a-connected-app-in-salesforce"></a>Salesforce에서 연결된 앱 만들기
Salesforce에서 Azure AD B2C를 연결된 앱으로 등록해야 합니다.

1. [Salesforce에 로그인](https://login.salesforce.com/) 
1. 왼쪽 메뉴의 **설정** 아래에서 **ID**를 확장하고 **ID 공급자**를 클릭합니다.
1. 맨 아래 **서비스 공급자** 섹션에서 **Service Providers are now created via Connected Apps. Click here.**(서비스 공급자가 연결된 앱을 통해 생성됩니다. 여기를 클릭하세요.)를 클릭합니다.
1. 연결된 앱에 대한 필수 **기본 정보**를 제공합니다.
1. 이제 **Web App 설정** 섹션에서 다음을 수행합니다.
    1. **SAML 사용**을 선택합니다.
    1. **엔터티 ID** 필드에 다음 URL을 입력하고 `tenantName`을 해당 내용으로 바꿉니다. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
        ```

    1. **ACS URL** 필드에 다음 URL을 입력하고 `tenantName`을 해당 내용으로 바꿉니다. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
        ```

    1. 다른 모든 설정은 기본값으로 유지합니다.
1. 맨 아래 끝까지 스크롤하고 **저장** 단추를 클릭합니다.


## <a name="troubleshooting"></a>문제 해결

해당 블레이드를 열고 "지금 실행"을 클릭하여 방금 업로드한 사용자 지정 정책을 테스트합니다. 문제가 발생하는 경우 [문제를 해결](active-directory-b2c-troubleshoot-custom.md)하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계
 
[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 대한 피드백을 제공합니다.


