---
title: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가 | Microsoft Docs
description: Azure Active Directory B2C에서 SAML 프로토콜 및 사용자 지정 정책을 사용하 여 ADFS 2016 설정
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669229"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD(Azure Active Directory) B2C의 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 ADFS 사용자 계정에 대한 로그인을 사용하도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C에 ADFS 계정 응용 프로그램 키 추가

ADFS 계정으로 페더레이션하려면 응용 프로그램 대신 Azure AD B2C를 신뢰하기 위해 이 계정에 대한 클라이언트 암호가 필요합니다. Azure AD B2C 테넌트에 ADFS 인증서를 저장해야 합니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 상단 모서리에서 전환하여 Azure AD B2C 테넌트가 있는 디렉터리를 사용하고 있는지 확인합니다. **디렉터리 전환**을 선택하고 만든 테넌트를 포함하는 디렉터리를 선택합니다. 이 자습서에서는 *contoso0522Tenant.onmicrosoft.com*라는 테넌트를 포함하는 *contoso* 디렉터리가 사용됩니다.

    ![디렉터리 전환](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다. 이제 해당 테넌트를 사용하게 됩니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키**를 선택하여 테넌트에 사용 가능한 키를 표시한 후 **추가**를 클릭합니다.
6. 옵션으로 **업로드**를 선택합니다.
7. 이름으로 `ADFSSamlCert`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
8. 개인 키가 있는 인증서 .pfx 파일을 찾아 선택합니다. 이 인증서(개인 키 포함)는 ADFS 신뢰 당사자에 발급되고 사용되는 것과 동일해야 합니다.
9. **만들기**를 클릭하고 `B2C_1A_ADFSSamlCert` 키를 만들었는지 확인합니다.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>확장 정책에서 클레임 공급자 추가

사용자가 ADFS 계정을 사용하여 로그인하도록 하려면 해당 계정을 클레임 공급자로 정의해야 합니다. Azure AD B2C가 통신하는 엔드포인트를 지정하여 이렇게 할 수 있습니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

확장 정책 파일에서 **ClaimsProvider** 요소를 추가하여 ADFS를 클레임 공급자로 정의합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 정책 파일을 엽니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).
2. **ClaimsProviders** 요소 아래에 다음 XML을 추가하고 **your-ADFS-domain**을 ADFS 도메인 이름으로 바꾸고, **identityProvider** 출력 클레임의 값을 DNS(도메인을 나타내는 임의 값)로 바꾼 후 파일을 저장합니다. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>등록 및 로그인을 위해 클레임 공급자 등록

ADFS 계정 ID 공급자를 등록 및 로그인 페이지에서 사용할 수 있도록 하려면 **SignUpOrSignIn** 사용자 경험에 추가해야 합니다. 

기존 템플릿 사용자 경험을 복사한 후 ADFS ID 공급자를 포함하도록 수정합니다.

>[!NOTE]
>이전에 **UserJourneys** 요소를 정책의 기본 파일에서 확장 파일(*TrustFrameworkExtensions.xml*)로 복사한 경우 이 섹션을 건너뛸 수 있습니다.

1. 정책의 기본 파일을 엽니다(예: *TrustFrameworkBase.xml*).
2. **UserJourneys** 요소의 전체 콘텐츠를 복사합니다.
3. 확장 파일(*TrustFrameworkExtensions.xml*)을 열고 복사한 **UserJourneys** 요소의 전체 콘텐츠를 확장 파일에 붙여넣습니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelections** 요소는 클레임 공급자 선택 목록과 해당 순서를 정의합니다.  **ClaimsProviderSelection** 요소는 등록 및 로그인 페이지에서 ID 공급자 단추를 사용하는 것과 유사합니다. ADFS 계정에 대해 **ClaimsProviderSelection** 요소를 추가하는 경우 사용자가 페이지를 볼 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1. 복사한 사용자 경험에서 식별자가 `SignUpOrSignIn`인 **UserJourney** 요소에서 `Order="1"`의 **OrchestrationStep** 요소를 찾습니다.
2. 다음 **ClaimsProviderSelection** 요소를 **ClaimsProviderSelections** 요소 아래에 추가합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 ADFS 계정과 통신하여 토큰을 수신할 수 있게 됩니다. ADFS 계정 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1. **UserJourney** 요소 아래에서 `Order="2"`의 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소 를 **ClaimsExchanges** 요소 아래에 추가합니다.

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * `Id`가 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값을 갖는지 확인합니다.
> * `TechnicalProfileReferenceId`가 이전에 만든 기술 프로필로(Contoso-SAML2)로 설정되어 있는지 확인합니다.


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[선택 사항] 프로필 편집에 대한 클레임 공급자 등록

또한 프로필 편집 사용자 경험에 ADFS 계정 ID 공급자를 추가하려고 합니다.

### <a name="display-the-button"></a>단추 표시

1. 정책의 확장 파일을 엽니다. 예: *TrustFrameworkExtensions.xml*
2. 복사한 사용자 경험에서 식별자가 `ProfileEdit`인 **UserJourney** 요소에서 `Order="1"`의 **OrchestrationStep** 요소를 찾습니다.
3. 다음 **ClaimsProviderSelection** 요소를 **ClaimsProviderSelections** 요소 아래에 추가합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

1. **UserJourney** 요소 아래에서 `Order="2"`의 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소 를 **ClaimsExchanges** 요소 아래에 추가합니다.

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>테넌트에 정책 업로드

1. Azure Portal에서 **모든 정책**을 선택합니다.
2. **정책 업로드**를 선택합니다.
3. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정합니다.
4. *TrustFrameworkExtensions.xml* 정책 파일을 찾아 선택한 후 **업로드**를 선택합니다. 유효성 검사가 성공인지 확인합니다.


## <a name="configure-an-adfs-relying-party-trust"></a>ADFS 신뢰 당사자 트러스트 구성

Azure AD B2C에서 ADFS를 ID 공급자로 사용하려면 Azure AD B2C SAML 메타데이터를 사용하여 ADFS 신뢰 당사자 트러스트를 만들어야 합니다. 다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타데이터에 대한 URL 주소를 보여 줍니다.

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

다음 값을 바꿉니다.

- **your-tenant**를 테넌트 이름(예: your-tenant.onmicrosoft.com)으로
- **your-policy**를 정책 이름으로. SAML 공급자 기술 프로필을 구성한 정책 또는 해당 정책에서 상속하는 정책을 사용합니다.
- **your-technical-profile**을 SAML ID 공급자 기술 프로필의 이름으로
 
브라우저를 열고 URL로 이동합니다. 올바른 URL을 입력했는지와 XML 메타데이터 파일에 대한 액세스 권한이 있는지 확인합니다.

ADFS 관리 스냅인을 사용하여 새 신뢰 당사자 트러스트를 추가하고 설정을 수동으로 구성하려면 페더레이션 서버에서 다음 절차를 수행합니다. 로컬 컴퓨터에서 **관리자**또는 동등한 멤버 자격은 최소한 이 절차를 완료해야 합니다. [로컬 및 도메인 기본 그룹](http://go.microsoft.com/fwlink/?LinkId=83477)에서 적절한 계정 및 그룹 멤버 자격을 사용하는 방법에 대한 세부 정보를 검토합니다.

1. 서버 관리자에서 **도구**를 선택하고 **ADFS 관리**를 선택합니다.
2. **신뢰 당사자 트러스트 추가**를 선택합니다.
3. **시작** 페이지에서 **클레임 인식**을 클릭하고 **시작**을 선택합니다.
4. **데이터 원본 선택** 페이지에서 **Import data about the relying party publish online or on a local network**(신뢰 당사자 온라인 또는 로컬 네트워크 게시에 대한 정보 가져오기)를 선택하고 Azure AD B2C 메타데이터 URL을 제공한 후 **다음**을 클릭합니다.
5. **표시 이름 지정** 페이지에서 **표시 이름**을 입력하고 **메모** 아래에 이 신뢰 당사자 트러스트에 대한 설명을 입력한 후 **다음**을 클릭합니다.
6. **액세스 제어 정책 선택** 페이지에서 정책을 선택하고 **다음**을 클릭합니다.
7. **트러스트 추가 준비 완료** 페이지에서 설정을 검토하고 **다음**을 클릭하여 신뢰 당사자 트러스트 정보를 저장합니다.
8. **마침** 페이지에서 **닫기**를 클릭하면 이 작업이 **클레임 규칙 편집** 대화 상자를 자동으로 표시합니다.
9. **규칙 추가**를 선택합니다.  
10. **클레임 규칙 템플릿**에서 **LDAP 특성을 클레임으로 전송**을 선택합니다.
11. **클레임 규칙 이름**을 제공합니다. **특성 저장소**에서 **Active Directory 선택**을 선택합니다. 다음과 같은 클레임을 추가한 후 **마침** 및 **확인**을 클릭합니다.

    ![규칙 속성 설정](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  인증서 유형에 따라 해시 알고리즘을 설정해야 할 수 있습니다. 신뢰 당사자 트러스트(B2C 데모) 속성 창에서 **고급** 탭을 선택하고 **보안 해시 알고리즘**을 `SHA-1` 또는 `SHA-256`으로 설정하고 **확인**을 클릭합니다.  

### <a name="update-the-relying-party-metadata"></a>신뢰 당사자 메타데이터 업데이트

SAML 기술 프로필을 변경하려면 업데이트된 메타데이터 버전으로 ADFS를 업데이트해야 합니다. 신뢰 당사자 응용 프로그램을 만들 때는 메타데이터를 업데이트할 필요가 없지만 변경할 때는 ADFS의 메타데이터를 업데이트합니다.

1. 서버 관리자에서 **도구**를 선택하고 **ADFS 관리**를 선택합니다.
2. 만든 신뢰 당사자 트러스트를 선택하고 **페더레이션 메타데이터에서 업데이트**를 선택한 후 **업데이트**를 클릭합니다. 

### <a name="test-the-policy-by-using-run-now"></a>지금 실행을 사용하여 정책 테스트

1.  **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.
2.  업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_ProfileEdit**를 엽니다. **지금 실행**을 선택합니다. ADFS 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="download-the-complete-policy-files"></a>완성 정책 파일 다운로드

선택 사항: [사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료한 후에 고유한 사용자 지정 정책 사용하여 시나리오를 빌드하는 것이 좋습니다. 예제 파일에 대해서는 [참조 전용 정책 샘플 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)을 참조하세요.
