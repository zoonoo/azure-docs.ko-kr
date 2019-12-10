---
title: 사용자 지정 정책에서 SAML 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 SAML 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74666b1dc2ba4fac25aff0a56a52d048d746d465
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950938"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 SAML 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 SAML 2.0 id 공급자에 대 한 지원을 제공 합니다. 이 문서에서는 표준화 된 프로토콜을 지 원하는 클레임 공급자와 상호 작용 하기 위한 기술 프로필의 구체적인 사항을 설명 합니다. SAML 기술 프로필을 사용 하 여 [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) 및 [SALESFORCE](active-directory-b2c-setup-sf-app-custom.md)와 같은 saml 기반 id 공급자와 페더레이션 할 수 있습니다. 이 페더레이션을 통해 사용자는 기존 소셜 또는 엔터프라이즈 id를 사용 하 여 로그인 할 수 있습니다.

## <a name="metadata-exchange"></a>메타 데이터 교환

메타 데이터는 saml 프로토콜에서 서비스 공급자 또는 id 공급자와 같은 SAML 파티의 구성을 노출 하는 데 사용 되는 정보입니다. 메타 데이터는 로그인, 로그 아웃, 인증서, 로그인 방법 등의 서비스 위치를 정의 합니다. Id 공급자는 메타 데이터를 사용 하 여 Azure AD B2C와 통신 하는 방법을 알아봅니다. 메타 데이터는 XML 형식으로 구성 되며, 다른 파티가 메타 데이터의 무결성을 확인할 수 있도록 디지털 서명으로 서명할 수 있습니다. SAML id 공급자를 사용 하 여 페더레이션를 Azure AD B2C 하는 경우 saml 요청을 시작 하 고 SAML 응답을 기다리는 서비스 공급자 역할을 합니다. 또한 일부 경우에는 id 공급자가 시작 하는 것이 라고도 하는 원치 않는 SAML 인증을 허용 합니다.

메타 데이터는 두 파티 모두에 "정적 메타 데이터" 또는 "동적 메타 데이터"로 구성할 수 있습니다. 정적 모드에서는 한 파티에서 전체 메타 데이터를 복사 하 고 다른 파티에 설정 합니다. 동적 모드에서는 다른 당사자가 구성을 동적으로 읽는 동안 메타 데이터에 대 한 URL을 설정 합니다. 원칙은 동일 합니다. id 공급자의 Azure AD B2C 기술 프로필에 대 한 메타 데이터를 설정 하 고 Azure AD B2C에서 id 공급자의 메타 데이터를 설정 합니다.

각 SAML id 공급자에는 서비스 공급자를 표시 하 고 설정 하는 서로 다른 단계가 있습니다 .이 경우 Azure AD B2C 하 고 id 공급자에서 Azure AD B2C 메타 데이터를 설정 합니다. 이 작업을 수행 하는 방법에 대 한 지침은 id 공급자 설명서를 참조 하세요.

다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타 데이터에 대 한 URL 주소를 보여 줍니다.

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

다음 값을 바꿉니다.

- 테 **넌 트** 이름 (예: fabrikam.b2clogin.com)
- 정책 이름이 포함 된 **정책** 입니다. SAML 공급자 기술 프로필을 구성 하는 정책 또는 해당 정책에서 상속 되는 정책을 사용 합니다.
- SAML id 공급자 기술 프로필 이름을 사용 하는 **-기술 프로필** .

## <a name="digital-signing-certificates-exchange"></a>디지털 서명 인증서 교환

Azure AD B2C와 SAML id 공급자 간의 신뢰를 구축 하려면 개인 키가 있는 유효한 X509 인증서를 제공 해야 합니다. 개인 키 (.pfx 파일)를 사용 하 여 인증서를 Azure AD B2C 정책 키 저장소에 업로드 합니다. Azure AD B2C는 사용자가 제공 하는 인증서를 사용 하 여 SAML 로그인 요청에 디지털 서명 합니다.

인증서는 다음과 같은 방법으로 사용 됩니다.

- Azure AD B2C는 인증서의 Azure AD B2C 개인 키를 사용 하 여 SAML 요청을 생성 하 고 서명 합니다. SAML 요청은 인증서의 Azure AD B2C 공개 키를 사용 하 여 요청의 유효성을 검사 하는 id 공급자에 게 전송 됩니다. Azure AD B2C 공용 인증서는 기술 프로필 메타 데이터를 통해 액세스할 수 있습니다. 또는 .cer 파일을 SAML id 공급자에 수동으로 업로드할 수 있습니다.
- Id 공급자는 인증서의 id 공급자의 개인 키를 사용 하 여 Azure AD B2C에 전송 된 데이터에 서명 합니다. Azure AD B2C는 id 공급자의 공용 인증서를 사용 하 여 데이터의 유효성을 검사 합니다. 각 id 공급자에는 설정에 대 한 다양 한 단계가 있습니다 .이 작업을 수행 하는 방법에 대 한 지침은 id 공급자 설명서를 참조 하세요. Azure AD B2C에서 정책은 id 공급자의 메타 데이터를 사용 하 여 인증서 공개 키에 대 한 액세스 권한이 필요 합니다.

대부분의 시나리오에는 자체 서명 된 인증서를 사용할 수 있습니다. 프로덕션 환경의 경우 인증 기관에서 발급 한 X509 인증서를 사용 하는 것이 좋습니다. 또한이 문서의 뒷부분에서 설명 하는 것 처럼 비프로덕션 환경에서는 두 쪽 모두에서 SAML 서명을 사용 하지 않도록 설정할 수 있습니다.

다음 다이어그램은 메타 데이터 및 인증서 교환을 보여 줍니다.

![메타 데이터 및 인증서 교환](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>디지털 암호화

SAML 응답 어설션을 암호화 하기 위해 id 공급자는 항상 Azure AD B2C 기술 프로필에서 암호화 인증서의 공개 키를 사용 합니다. Azure AD B2C는 데이터의 암호를 해독 해야 하는 경우 암호화 인증서의 비공개 부분을 사용 합니다.

SAML 응답 어설션을 암호화 하려면 다음을 수행 합니다.

1. 개인 키가 있는 유효한 X509 인증서 (.pfx 파일)를 Azure AD B2C 정책 키 저장소에 업로드 합니다.
2. `SamlAssertionDecryption` 식별자가 포함 된 **CryptographicKey** 요소를 기술 프로필 **CryptographicKeys** 컬렉션에 추가 합니다. **StorageReferenceId** 을 1 단계에서 만든 정책 키의 이름으로 설정 합니다.
3. 기술 프로필 메타 데이터 **WantsEncryptedAssertions** 을 `true`로 설정 합니다.
4. 새 Azure AD B2C 기술 프로필 메타 데이터를 사용 하 여 id 공급자를 업데이트 합니다. 인증서의 공개 키를 포함 하는 `encryption` **사용** 속성이 설정 된 **keydescriptor** 가 표시 되어야 합니다.

다음 예에서는 메타 데이터의 Azure AD B2C 기술 프로필 암호화 섹션을 보여 줍니다.

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol (Protokoll)

프로토콜 요소의 **Name** 특성을 `SAML2`으로 설정 해야 합니다.

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 `AttributeStatement` 섹션의 SAML id 공급자가 반환 하는 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 id 공급자에 정의 된 이름에 매핑해야 할 수 있습니다. `DefaultValue` 특성을 설정 하는 동안에는 id 공급자가 반환 하지 않은 클레임을 포함할 수도 있습니다.

정규화 된 클레임으로 **주체** 에서 SAML 어설션 **namedid** 을 읽으려면 클레임 대상 **claimtype** 을 `assertionSubjectName`로 설정 합니다. **NameId** 가 assertion XML의 첫 번째 값 인지 확인 합니다. 둘 이상의 어설션을 정의 하는 경우 Azure AD B2C 마지막 어설션의 주체 값을 선택 합니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **OutputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

다음 예에서는 Facebook id 공급자에서 반환 하는 클레임을 보여 줍니다.

- **IssuerUserId** 클레임이 **assertionSubjectName** 클레임에 매핑됩니다.
- **First_name** 클레임은 **givenName** 클레임에 매핑됩니다.
- **Last_name** 클레임은 **성** 클레임에 매핑됩니다.
- 이름 매핑이 없는 **displayName** 클레임입니다.
- 이름 매핑이 없는 **전자 메일** 클레임입니다.

또한 기술 프로필은 id 공급자가 반환 하지 않는 클레임을 반환 합니다.

- Id 공급자의 이름을 포함 하는 **identityProvider** 클레임입니다.
- **Authenticationsource** 클레임은 **기본값을 사용 합니다.**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| 파트너 엔터티 | Igen | SAML id 공급자의 메타 데이터 URL입니다. Id 공급자 메타 데이터를 복사 하 여 CDATA 요소 내에 추가 `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nem | 기술 프로필에 모든 나가는 인증 요청을 서명 해야 하는지 여부를 나타냅니다. 가능한 값: `true` 또는 `false`. 기본값은 `true`입니다. 값이 `true`로 설정 된 경우 **SamlMessageSigning** 암호화 키를 지정 해야 하며 모든 나가는 인증 요청을 서명 해야 합니다. 값이 `false`로 설정 된 경우 **SigAlg** 및 **Signature** 매개 변수 (쿼리 문자열 또는 post 매개 변수)는 요청에서 생략 됩니다. 또한이 메타 데이터는 id 공급자와 공유 되는 Azure AD B2C 기술 프로필의 메타 데이터에 출력 되는 메타 데이터 **Authnrequestssigned** 특성을 제어 합니다. 기술 프로필 메타 데이터의 **WantsSignedRequests** 값이 `false`으로 설정 되 고 id 공급자 메타 데이터 **WantAuthnRequestsSigned** 이 `false` 또는 지정 되지 않음으로 설정 된 경우 Azure AD B2C는 요청에 서명 하지 않습니다. |
| XmlSignatureAlgorithm | Nem | Azure AD B2C에서 SAML 요청에 서명 하는 데 사용 하는 메서드입니다. 이 메타 데이터는 SAML 요청에서 **SigAlg** 매개 변수 (쿼리 문자열 또는 post 매개 변수)의 값을 제어 합니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512`또는 `Sha1`입니다. 동일한 값을 사용 하 여 양쪽에 서명 알고리즘을 구성 해야 합니다. 인증서가 지 원하는 알고리즘만 사용 합니다. |
| WantsSignedAssertions | Nem | 기술 프로필에 들어오는 모든 어설션이 서명 되어야 하는지 여부를 나타냅니다. 가능한 값: `true` 또는 `false`. 기본값은 `true`입니다. 값이 `true`로 설정 된 경우 id 공급자가 보낸 모든 어설션 `saml:Assertion` 섹션을 Azure AD B2C 서명 해야 합니다. 값이 `false`로 설정 된 경우 id 공급자는 어설션을 서명 하지 않아야 합니다. 그러나 그렇지 않은 경우에도 Azure AD B2C 서명의 유효성을 검사 하지 않습니다. 또한이 메타 데이터는 id 공급자와 공유 되는 Azure AD B2C 기술 프로필의 메타 데이터에 출력 되는 메타 데이터 플래그 **WantsAssertionsSigned**를 제어 합니다. 어설션 유효성 검사를 사용 하지 않도록 설정 하는 경우 응답 서명 유효성 검사를 사용 하지 않도록 설정할 수도 있습니다 (자세한 내용은 **ResponsesSigned**참조). |
| ResponsesSigned | Nem | 가능한 값: `true` 또는 `false`. 기본값은 `true`입니다. 값이 `false`로 설정 된 경우 id 공급자는 SAML 응답에 서명 하지 않아야 합니다. 단,이 경우에도 Azure AD B2C 서명의 유효성을 검사 하지 않습니다. 값이 `true`로 설정 되 면 id 공급자가 Azure AD B2C에 보낸 SAML 응답이 서명 되 고 유효성을 검사 해야 합니다. SAML 응답 유효성 검사를 사용 하지 않도록 설정한 경우에도 어설션 서명 유효성 검사를 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 **WantsSignedAssertions**를 참조 하세요. |
| WantsEncryptedAssertions | Nem | 기술 프로필에서 들어오는 모든 어설션이 암호화 되어야 하는지 여부를 나타냅니다. 가능한 값: `true` 또는 `false`. 기본값은 `false`입니다. 값이 `true`로 설정 된 경우 id 공급자에서 Azure AD B2C로 보낸 어설션이 서명 되어야 하 고 **SamlAssertionDecryption** 암호화 키를 지정 해야 합니다. 값이 `true`로 설정 된 경우 Azure AD B2C 기술 프로필의 메타 데이터에는 **암호화** 섹션이 포함 됩니다. Id 공급자는 메타 데이터를 읽고 Azure AD B2C 기술 프로필의 메타 데이터에 제공 된 공개 키를 사용 하 여 SAML 응답 어설션을 암호화 합니다. 어설션 암호화를 사용 하도록 설정 하는 경우 응답 서명 유효성 검사를 사용 하지 않도록 설정 해야 할 수도 있습니다 (자세한 내용은 **ResponsesSigned**참조). |
| IdpInitiatedProfileEnabled | Nem | SAML id 공급자 프로필에 의해 시작 된 Single Sign-On 세션 프로필을 사용할 수 있는지 여부를 나타냅니다. 가능한 값: `true` 또는 `false`. A mező alapértelmezett értéke: `false`. Id 공급자에 의해 시작 되는 흐름에서 사용자는 외부에서 인증 되 고 원치 않는 응답이 Azure AD B2C 전송 된 다음 토큰을 사용 하 고 오케스트레이션 단계를 실행 한 다음 신뢰 당사자 응용 프로그램에 응답을 보냅니다. |
| NameIdPolicyFormat | Nem | 요청 된 주체를 나타내는 데 사용할 이름 식별자에 대 한 제약 조건을 지정 합니다. 생략 하는 경우 요청 된 주체에 대해 id 공급자가 지 원하는 모든 유형의 식별자를 사용할 수 있습니다. Például:  `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** 는 **NameIdPolicyAllowCreate**와 함께 사용할 수 있습니다. 지원 되는 이름 ID 정책에 대 한 지침은 id 공급자 설명서를 참조 하세요. |
| NameIdPolicyAllowCreate | Nem | **NameIdPolicyFormat**를 사용 하는 경우 **NameIDPolicy**의 `AllowCreate` 속성을 지정할 수도 있습니다. 이 메타 데이터의 값은 `true` 되거나 id 공급자가 로그인 흐름 중에 새 계정을 만들 수 있는지 여부를 나타내는 `false` 됩니다. 이 작업을 수행 하는 방법에 대 한 지침은 id 공급자 설명서를 참조 하세요. |
| AuthenticationRequestExtensions | Nem | Azure AD BC와 id 공급자 간에 합의 된 선택적 프로토콜 메시지 확장 요소입니다. 확장은 XML 형식으로 표시 됩니다. `<![CDATA[Your IDP metadata]]>`CDATA 요소 내에 XML 데이터를 추가 합니다. Id 공급자의 설명서를 확인 하 여 extensions 요소가 지원 되는지 확인 합니다. |
| IncludeAuthnContextClassReferences | Nem | 인증 컨텍스트 클래스를 식별 하는 URI 참조를 하나 이상 지정 합니다. 예를 들어 사용자가 사용자 이름 및 암호를 사용 하 여 로그인 할 수 있도록 하려면 값을 `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`로 설정 합니다. 보호 된 세션 (SSL/TLS)을 통해 사용자 이름 및 암호를 통한 로그인을 허용 하려면 `PasswordProtectedTransport`를 지정 합니다. 지원 되는 **Authncontextclassref** uri에 대 한 지침은 id 공급자 설명서를 참조 하세요. 여러 Uri를 쉼표로 구분 된 목록으로 지정 합니다. |
| IncludeKeyInfo | Nem | 바인딩이 `HTTP-POST`로 설정 된 경우 SAML 인증 요청에 인증서의 공개 키가 포함 되어 있는지 여부를 나타냅니다. 가능한 값: `true` 또는 `false`. |

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 다음 특성을 포함 합니다.

| Attribútum |Szükséges | Leírás |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Igen | SAML 메시지에 서명 하는 데 사용할 X509 인증서 (RSA 키 집합)입니다. Azure AD B2C는이 키를 사용 하 여 요청에 서명 하 고 id 공급자에 게 보냅니다. |
| SamlAssertionDecryption |Igen | SAML 메시지의 암호를 해독 하는 데 사용할 X509 인증서 (RSA 키 집합)입니다. 이 인증서는 id 공급자가 제공 해야 합니다. Azure AD B2C는이 인증서를 사용 하 여 id 공급자가 보낸 데이터의 암호를 해독 합니다. |
| MetadataSigning |Nem | SAML 메타 데이터에 서명 하는 데 사용할 X509 인증서 (RSA 키 집합)입니다. Azure AD B2C는이 키를 사용 하 여 메타 데이터에 서명 합니다.  |

## <a name="next-steps"></a>Következő lépések

Azure AD B2C에서 SAML id 공급자를 사용 하는 예제는 다음 문서를 참조 하세요.

- [사용자 지정 정책을 사용 하 여 SAML id 공급자로 ADFS 추가](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [SAML을 통해 Salesforce 계정을 사용 하 여 로그인](active-directory-b2c-setup-sf-app-custom.md)
