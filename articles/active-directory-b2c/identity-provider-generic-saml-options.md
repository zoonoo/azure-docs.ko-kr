---
title: SAML id 공급자 옵션을 사용 하 여 로그인 설정
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C에서 로그인 SAML id 공급자 (IdP) 옵션을 구성 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488876"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 SAML id 공급자 옵션 구성

Azure Active Directory B2C (Azure AD B2C)는 SAML 2.0 id 공급자와의 페더레이션을 지원 합니다. 이 문서에서는 SAML id 공급자를 사용 하 여 로그인 할 때 사용할 수 있는 구성 옵션을 설명 합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>클레임 매핑

**Outputclaims** 요소는 SAML id 공급자가 반환 하는 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 id 공급자에 정의 된 이름에 매핑해야 합니다. Id 공급자에 게 클레임 (어설션) 목록이 있는지 확인 합니다. Id 공급자가 반환 하는 SAML 응답의 콘텐츠를 확인할 수도 있습니다. 자세한 내용은 [SAML 메시지 디버그](#debug-saml-protocol)를 참조 하세요. 클레임을 추가 하려면 먼저 [클레임을 정의한](claimsschema.md)다음, 클레임을 출력 클레임 컬렉션에 추가 합니다.

`DefaultValue` 특성만 설정하면, ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다. 기본값은 [컨텍스트 클레임](#enable-use-of-context-claims)을 사용 하 여 정적 또는 동적일 수 있습니다.

출력 클레임 요소는 다음 특성을 포함 합니다.

- **ClaimTypeReferenceId** 는 클레임 유형에 대한 참조입니다. 
- 지 속성은 SAML 어설션이 표시 되는 속성의 **이름입니다.** 
- **DefaultValue** 는 미리 정의 된 기본값입니다. 클레임이 비어 있으면 기본값이 사용 됩니다. 상관 관계 ID 또는 사용자 IP 주소와 같은 컨텍스트 값을 사용 하 여 [클레임 해결 프로그램](claim-resolver-overview.md) 을 사용할 수도 있습니다.

### <a name="subject-name"></a>주체 이름

**주체의** SAML assertion **NameId** 를 정규화 **된 클레임으로 읽으려면 클레임** 특성 값을 특성 값으로 설정 합니다 `SPNameQualifier` . `SPNameQualifier`특성이 표시 되지 않으면 특성의 값으로 클레임을  설정 합니다 `NameQualifier` .

SAML 어설션:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

출력 클레임:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

`SPNameQualifier`또는 특성이 모두 `NameQualifier` SAML 어설션에 표시 되지 않는 경우 클레임 대상 **claimtype** 을로 설정 `assertionSubjectName` 합니다. **NameId** 가 어설션 XML의 첫 번째 값인지 확인합니다. 둘 이상의 어설션을 정의하면 Azure AD B2C가 마지막 어설션에서 주체 값을 선택합니다.


## <a name="configure-saml-protocol-bindings"></a>SAML 프로토콜 바인딩 구성

SAML 요청은 id 공급자의 metadata 요소에 지정 된 id 공급자에 게 전송 됩니다 `SingleSignOnService` . 대부분의 id 공급자 권한 부여 요청은 메시지가 상대적으로 짧기 때문에 HTTP GET 요청의 URL 쿼리 문자열에서 직접 전달 됩니다. 두 SAML 요청에 대 한 바인딩을 구성 하는 방법은 id 공급자 설명서를 참조 하세요.

다음은 두 개의 바인딩이 있는 Azure AD 메타 데이터 Single Sign-On 서비스의 예제입니다. 는 `HTTP-Redirect` `HTTP-POST` SAML id 공급자 메타 데이터에 먼저 표시 되기 때문에 보다 우선적으로 적용 됩니다.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

SAML 응답은 HTTP POST 바인딩을 통해 Azure AD B2C 전송 됩니다. Azure AD B2C 정책 메타 데이터는 `AssertionConsumerService` 바인딩을로 설정 `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` 합니다.

다음은 Azure AD B2C 정책 메타 데이터 Assertion Consumer Service 요소에 대 한 예입니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>SAML 요청 서명 구성

**SamlMessageSigning** 암호화 키를 사용 하 여 나가는 모든 인증 요청을 서명 Azure AD B2C 합니다. SAML 요청 서명을 사용 하지 않도록 설정 하려면 **WantsSignedRequests** 을로 설정 `false` 합니다. 메타 데이터를로 설정 하면 `false` **SigAlg** 및 **Signature** 매개 변수 (쿼리 문자열 또는 post 매개 변수)가 요청에서 생략 됩니다.

또한이 메타 데이터는 id 공급자와 공유 되는 Azure AD B2C 기술 프로필의 메타 데이터에 포함 된 **Authnrequestssigned** 특성을 제어 합니다. 기술 프로필 메타 데이터의 **WantsSignedRequests** 값이로 설정 되 `false` 고 id 공급자 메타 데이터 **WantAuthnRequestsSigned** 가로 설정 `false` 되었거나 지정 되지 않은 경우 Azure AD B2C는 요청에 서명 하지 않습니다.

다음 예제에서는 SAML 요청에서 서명을 제거 합니다.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>서명 알고리즘

Azure AD B2C는를 사용 하 여 `Sha1` SAML 요청을 서명 합니다. **XmlSignatureAlgorithm** 메타 데이터를 사용 하 여 사용할 알고리즘을 구성 합니다. 가능한 값은 `Sha256` , `Sha384` , `Sha512` 또는 `Sha1` (기본값)입니다. 이 메타데이터는 SAML 요청의 **SigAlg** 매개 변수(쿼리 문자열 또는 게시 매개 변수) 값을 제어합니다. 양쪽의 서명 알고리즘을 같은 값으로 구성해야 합니다. 인증서가 지원하는 알고리즘만 사용하세요.

### <a name="include-key-info"></a>키 정보 포함

Id 공급자가 Azure AD B2C 바인딩이로 설정 된 것으로 표시 되 면 `HTTP-POST` SAML 요청의 본문에 시그니처와 알고리즘이 포함 Azure AD B2C. 바인딩이로 설정 된 경우 인증서의 공개 키를 포함 하도록 Azure AD를 구성할 수도 있습니다 `HTTP-POST` . **Includekeyinfo** 메타 데이터를 `true` , 또는에 사용 `false` 합니다. 다음 예제에서는 Azure AD에 인증서의 공개 키가 포함 되어 있지 않습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML 요청 이름 ID 구성

SAML 권한 부여 요청 `<NameID>` 요소는 saml 이름 식별자 형식을 나타냅니다. 이 섹션에서는 기본 구성과 이름 ID 요소를 사용자 지정 하는 방법을 설명 합니다.

## <a name="preferred-username"></a>기본 사용자 이름

로그인 사용자 경험 중에는 신뢰 당사자 응용 프로그램이 특정 사용자를 대상으로 할 수 있습니다. Azure AD B2C를 사용 하 여 SAML id 공급자에 기본 사용자 이름을 보낼 수 있습니다. **Inputclaims** 요소는 SAML 권한 부여 요청 **주체** 내에서 **NameId** 를 보내는 데 사용 됩니다.

권한 부여 요청 내에 주체 이름 ID를 포함 하려면 다음 요소를 `<InputClaims>` 바로 뒤에 추가 합니다 `<CryptographicKeys>` . 대상 **claimtype** 은로 설정 해야 합니다 `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

이 예제에서 Azure AD B2C는 SAML 권한 부여 요청 **주체** 내에서 **NameId** 를 사용 하 여 **signInName** 클레임 값을 보냅니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

과 같은 [컨텍스트 클레임](claim-resolver-overview.md)을 사용 하 여 `{OIDC:LoginHint}` 클레임 값을 채울 수 있습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>이름 ID 정책 형식

기본적으로 SAML 권한 부여 요청은 정책을 지정 합니다 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` . 이는 요청 된 주체에 대해 id 공급자가 지 원하는 모든 형식의 식별자를 사용할 수 있음을 나타냅니다.

이 동작을 변경 하려면 id 공급자 설명서를 참조 하 여 지원 되는 이름 ID 정책에 대 한 지침을 참조 하세요. 그런 다음 `NameIdPolicyFormat` 해당 정책 형식으로 메타 데이터를 추가 합니다. 예를 들면 다음과 같습니다.

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

다음 SAML 권한 부여 요청은 이름 ID 정책을 포함 합니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>새 계정 만들기 허용

[이름 id 정책 형식을](#name-id-policy-format)지정 하는 경우 `AllowCreate` **NameIDPolicy** 의 속성을 지정 하 여 id 공급자가 로그인 흐름 중에 새 계정을 만들 수 있는지 여부를 나타낼 수도 있습니다. 지침은 id 공급자 설명서를 참조 하세요.

Azure AD B2C은 `AllowCreate` 기본적으로 속성을 생략 합니다. 메타 데이터를 사용 하 여이 동작을 변경할 수 있습니다 `NameIdPolicyAllowCreate` . 이 메타 데이터의 값은 `true` 또는 `false` 입니다.

다음 예제에서는의 속성을로 설정 하는 방법을 보여 줍니다 `AllowCreate` `NameIDPolicy` `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


다음 예제에서는 권한 부여 요청에서 **NameIDPolicy** 요소의 **allowcreate** 를 사용 하 여 권한 부여 요청을 보여 줍니다.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>인증 컨텍스트 클래스 참조 포함

SAML 권한 부여 요청에는 인증 요청 컨텍스트를 지정 하는 **Authncontext** 요소가 포함 될 수 있습니다. 요소는 인증 컨텍스트 클래스 참조를 포함할 수 있습니다 .이 참조는 SAML id 공급자에 게 사용자에 게 제공할 인증 메커니즘을 알려 줍니다.

인증 컨텍스트 클래스 참조를 구성 하려면 **Includeauthncontextclassreferences** 메타 데이터를 추가 합니다. 값에서 인증 컨텍스트 클래스를 식별 하는 URI 참조를 하나 이상 지정 합니다. 여러 Uri를 쉼표로 구분 된 목록으로 지정 합니다. 지원 되는 **Authncontextclassref** uri에 대 한 지침은 id 공급자 설명서를 참조 하세요.

다음 예에서는 사용자 이름과 암호를 모두 사용 하 여 로그인 하 고 보호 된 세션 (SSL/TLS)을 통해 사용자 이름 및 암호를 사용 하 여 로그인 할 수 있습니다.

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

다음 SAML 권한 부여 요청은 인증 컨텍스트 클래스 참조를 포함 합니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>권한 부여 요청에 사용자 지정 데이터 포함

필요에 따라 Azure AD BC와 id 공급자 모두에 게 동의한 프로토콜 메시지 확장 요소를 포함할 수 있습니다. 확장은 XML 형식으로 표시됩니다. CDATA 요소 내에 XML 데이터를 추가 하 여 확장 요소를 포함 `<![CDATA[Your IDP metadata]]>` 합니다. ID 공급자의 설명서를 확인하여 확장 요소가 지원되는지 검토합니다.

다음 예에서는 확장 데이터를 사용 하는 방법을 보여 줍니다.

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

SAML 프로토콜 메시지 확장을 사용 하는 경우 SAML 응답은 다음 예제와 같습니다.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>서명 된 SAML 응답 필요

Azure AD B2C에는 들어오는 모든 어설션이 서명 되어야 합니다. **WantsSignedAssertions** 을로 설정 하 여이 요구 사항을 제거할 수 있습니다 `false` . 이 경우 id 공급자는 어설션에 서명 하지 않아야 하지만, 그렇지 않은 경우에도 Azure AD B2C 서명의 유효성을 검사 하지 않습니다.

**WantsSignedAssertions** 메타 데이터는 id 공급자와 공유 되는 Azure AD B2C 기술 프로필의 메타 데이터에 포함 된 SAML 메타 데이터 플래그 **WantAssertionsSigned** 를 제어 합니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

어설션 유효성 검사를 사용 하지 않도록 설정 하는 경우 응답 메시지 서명 유효성 검사를 사용 하지 않도록 설정할 수도 있습니다. **ResponsesSigned** 메타 데이터를로 설정 `false` 합니다. 이 경우 id 공급자는 SAML 응답 메시지에 서명 하지 않아야 합니다. 단,이 경우에도 Azure AD B2C 서명의 유효성을 검사 하지 않습니다.

다음 예제에서는 메시지와 어설션 시그니처를 모두 제거 합니다.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>암호화 된 SAML 응답 필요

들어오는 모든 어설션을 암호화 하도록 요구 하려면 **WantsEncryptedAssertions** 메타 데이터를 설정 합니다. 암호화가 필요한 경우 id 공급자는 Azure AD B2C 기술 프로필에서 암호화 인증서의 공개 키를 사용 합니다. Azure AD B2C는 암호화 인증서의 비공개 부분을 사용 하 여 응답 어설션을 해독 합니다.

어설션 암호화를 사용 하도록 설정 하는 경우 응답 서명 유효성 검사를 사용 하지 않도록 설정 해야 할 수도 있습니다. 자세한 내용은 [서명 된 SAML 응답 필요](#require-signed-saml-responses)를 참조 하세요.

**WantsEncryptedAssertions** 메타 데이터를로 설정 하면 `true` Azure AD B2C 기술 프로필의 메타 데이터에 **암호화** 섹션이 포함 됩니다. ID 공급자는 메타데이터를 읽고 Azure AD B2C 기술 프로필의 메타데이터에서 제공되는 공개 키를 사용하여 SAML 응답 어설션을 암호화합니다.

다음 예에서는 암호화에 사용 되는 SAML 메타 데이터의 키 설명자 섹션을 보여 줍니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

SAML 응답 어설션을 암호화하려면 다음 단계를 수행합니다.

1. 고유 식별자를 사용 하 여 [정책 키를 만듭니다](identity-provider-generic-saml.md#create-a-policy-key) . 예: `B2C_1A_SAMLEncryptionCert`
2. SAML 기술 프로필 **CryptographicKeys** 컬렉션에 있습니다. **StorageReferenceId** 를 첫 번째 단계에서 만든 정책 키의 이름으로 설정 합니다. `SamlAssertionDecryption`ID는 암호화 키를 사용 하 여 SAML 응답의 어설션을 암호화 하 고 암호 해독 하는 것을 나타냅니다.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. 기술 프로필 메타데이터 **WantsEncryptedAssertions** 를 `true`로 설정합니다.
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. 새 Azure AD B2C 기술 프로필 메타 데이터를 사용 하 여 id 공급자를 업데이트 합니다. 인증서의 공개 키를 포함하며 **use** 속성이 `encryption`으로 설정된 **KeyDescriptor** 가 표시되어야 합니다.

## <a name="enable-use-of-context-claims"></a>컨텍스트 클레임 사용 사용

입력 및 출력 클레임 컬렉션에서 특성을 설정 하는 동안에는 id 공급자가 반환 하지 않은 클레임을 포함할 수 있습니다 `DefaultValue` . [컨텍스트 클레임](claim-resolver-overview.md) 을 사용 하 여 기술 프로필에 포함할 수도 있습니다. 컨텍스트 클레임을 사용 하려면:

1. [BuildingBlocks](buildingblocks.md)내의 [ClaimsSchema](claimsschema.md) 요소에 클레임 유형을 추가 합니다.
2. 입력 또는 출력 컬렉션에 출력 클레임을 추가 합니다. 다음 예에서는 첫 번째 클레임이 id 공급자의 값을 설정 합니다. 두 번째 클레임은 사용자 IP 주소 [컨텍스트 클레임](claim-resolver-overview.md)을 사용 합니다.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>단일 로그 아웃 사용 안 함

응용 프로그램 로그 아웃 요청 시 Azure AD B2C는 SAML id 공급자에서 로그 아웃 하려고 시도 합니다. 자세한 내용은 [Azure AD B2C 세션 로그 아웃](session-behavior.md#sign-out)을 참조 하세요.  단일 로그 아웃 동작을 사용 하지 않도록 설정 하려면 **Singlelogoutenabled** 메타 데이터를로 설정 합니다 `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>SAML 프로토콜 디버그

Saml id 공급자와의 페더레이션을 구성 하 고 디버그 하는 데 도움이 되도록 saml 프로토콜에 대 한 브라우저 확장을 사용할 수 있습니다. 예를 들어 Chrome 용 [Saml DevTools 확장](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) , FireFox 용 [saml 추적 기능](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 또는에 [지 또는 IE 개발자 도구](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)를 사용할 수 있습니다.

이러한 도구를 사용 하 여 Azure AD B2C와 SAML id 공급자 간의 통합을 확인할 수 있습니다. 예를 들면 다음과 같습니다.

* SAML 요청에 서명이 포함 되어 있는지 확인 하 고 권한 부여 요청에 로그인 하는 데 사용 되는 알고리즘을 결정 합니다.
* 섹션 아래에서 클레임 (어설션)을 가져옵니다 `AttributeStatement` .
* Id 공급자에서 오류 메시지를 반환 하는지 확인 합니다.
* 어설션 섹션이 암호화 되어 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Application Insights](troubleshoot-with-application-insights.md)를 사용 하 여 사용자 지정 정책을 사용 하 여 문제를 진단 하는 방법을 알아봅니다. 

::: zone-end
