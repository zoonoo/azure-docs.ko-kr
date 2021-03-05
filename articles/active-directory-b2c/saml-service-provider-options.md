---
title: SAML 서비스 공급자 옵션 구성
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML 서비스 공급자 옵션을 구성 하는 방법
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: adfe5318949ffa624ebe3548944b558bd0dda9e1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198475"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에서 SAML 응용 프로그램을 등록 하는 옵션

이 문서에서는 Azure Active Directory (Azure AD B2C)를 SAML 응용 프로그램에 연결할 때 사용할 수 있는 구성 옵션을 설명 합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>암호화 된 SAML 어설션

응용 프로그램에서 SAML 어설션이 암호화 된 형식이 될 것으로 예상 되는 경우 Azure AD B2C 정책에서 암호화가 사용 되도록 설정 되어 있는지 확인 해야 합니다.

Azure AD B2C는 서비스 공급자의 공개 키 인증서를 사용 하 여 SAML 어설션을 암호화 합니다. 다음 예제와 같이 KeyDescriptor ' use '를 ' Encryption '으로 설정 하 여 SAML 응용 프로그램의 메타 데이터 끝점에 공개 키가 있어야 합니다.

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C에서 암호화 된 어설션을 보내도록 설정 하려면 신뢰 당사자  `true` [기술 프로필](relyingparty.md#technicalprofile)에서 WantsEncryptedAssertion 메타 데이터 항목을로 설정 합니다. SAML 어설션을 암호화 하는 데 사용 되는 알고리즘을 구성할 수도 있습니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>암호화 방법

SAML 어설션 데이터를 암호화 하는 데 사용 되는 암호화 방법을 구성 하려면 `DataEncryptionMethod` 신뢰 당사자 내에서 메타 데이터 키를 설정 합니다. 가능한 값은 `Aes256` (기본값), `Aes192` , `Sha512` 또는 `Aes128` 입니다. 메타 데이터는 `<EncryptedData>` SAML 응답의 요소 값을 제어 합니다.

SAML 어설션 데이터를 암호화 하는 데 사용 된 키의 복사본을 암호화 하는 데 사용 되는 암호화 방법을 구성 하려면 `KeyEncryptionMethod` 신뢰 당사자 내에서 메타 데이터 키를 설정 합니다. 가능한 값은 `Rsa15` (기본값)-RSA PKCS (공개 키 암호화 표준) 버전 1.5 알고리즘 및 `RsaOaep` -RSA 최적 OAEP (비대칭 암호화 패딩) 암호화 알고리즘입니다.  메타 데이터는  `<EncryptedKey>` SAML 응답의 요소 값을 제어 합니다.

다음 예제에서는 `EncryptedAssertion` SAML 어설션의 섹션을 보여 줍니다. 암호화 된 데이터 메서드는이 `Aes128` 고 암호화 된 키 메서드는 `Rsa15` 입니다.

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

암호화 된 어설션의 형식을 변경할 수 있습니다. 암호화 형식을 구성 하려면 `UseDetachedKeys` 신뢰 당사자 내에서 메타 데이터 키를 설정 합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 값이로 설정 되 면 `true` 분리 된 키는와는 달리 암호화 된 어설션을의 자식으로 추가 합니다 `EncrytedAssertion` `EncryptedData` .

암호화 방법 및 형식을 구성 하 고, [신뢰 당사자 기술 프로필](relyingparty.md#technicalprofile)내에서 메타 데이터 키를 사용 합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Id 공급자-시작 된 흐름

응용 프로그램에서 먼저 SAML 인증 요청을 id 공급자로 보내지 않고 SAML 어설션을 받아야 하는 경우 id 공급자가 시작한 흐름에 대 한 Azure AD B2C를 구성 해야 합니다.

Id 공급자가 시작한 흐름에서 로그인 프로세스는 서비스 공급자 (신뢰 당사자 응용 프로그램)에 게 원치 않는 SAML 응답을 보내는 id 공급자 (Azure AD B2C)에 의해 시작 됩니다.

현재는 시작 id 공급자가 Azure AD B2C와 페더레이션된 외부 id 공급자 (예: [AD FS](identity-provider-adfs.md)또는 [Salesforce](identity-provider-salesforce-saml.md)) 인 시나리오를 지원 하지 않습니다. Azure AD B2C 로컬 계정 인증에 대해서만 지원 됩니다.

Id 공급자가 시작한 흐름을 사용 하도록 설정 하려면  `true` 신뢰 당사자 [기술 프로필](relyingparty.md#technicalprofile)에서 IdpInitiatedProfileEnabled 메타 데이터 항목을로 설정 합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Id 공급자가 시작한 흐름을 통해 사용자를 로그인 하거나 등록 하려면 다음 URL을 사용 합니다.

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

다음 값을 바꿉니다.

* 테 넌 트 **이름으로 테 넌 트 이름**
* **정책-** SAML 신뢰 당사자 정책 이름으로 이름
* **응용 프로그램 식별자-** 메타 데이터 파일의를 사용 하는 uri입니다 ( `identifierUris` 예:). `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>샘플 정책

SAML 테스트 앱을 사용 하 여 테스트 하는 데 사용할 수 있는 전체 샘플 정책을 제공 합니다.

1. [SAML SP 시작 로그인 예제 정책을](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)다운로드 합니다.
1. `TenantId`테 넌 트 이름 (예: *contoso.b2clogin.com*)과 일치 하도록 업데이트 합니다.
1. *B2C_1A_signup_signin_saml* 정책 이름을 유지 합니다.

## <a name="saml-response-signature-algorithm"></a>SAML 응답 서명 알고리즘

SAML 어설션에 서명 하는 데 사용 되는 서명 알고리즘을 구성할 수 있습니다. 가능한 값은 `Sha256`, `Sha384`, `Sha512` 및 `Sha1`입니다. 기술 프로필 및 응용 프로그램에서 동일한 서명 알고리즘을 사용 하는지 확인 합니다. 인증서가 지원하는 알고리즘만 사용하세요.

`XmlSignatureAlgorithm`신뢰 당사자 메타 데이터 요소 내에서 메타 데이터 키를 사용 하 여 서명 알고리즘을 구성 합니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>SAML 응답 수명

SAML 응답이 유효한 상태로 유지 되는 기간을 구성할 수 있습니다. `TokenLifeTimeInSeconds`SAML 토큰 발급자 기술 프로필 내에서 메타 데이터 항목을 사용 하 여 수명을 설정 합니다. 이 값은 `NotBefore` 토큰 발급 시간에 계산 된 타임 스탬프에서 경과할 수 있는 시간 (초)입니다. 기본 수명은 300 초 (5 분)입니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>왜곡에서 유효한 SAML 응답

SAML 응답 타임 스탬프에 적용 되는 시간 오차를 구성할 수 있습니다 `NotBefore` . 이 구성을 사용 하면 두 플랫폼 간의 시간이 동기화 되지 않을 경우이 시간 오차 내에 SAML 어설션이 여전히 유효한 것으로 간주 됩니다.

`TokenNotBeforeSkewInSeconds`SAML 토큰 발급자 기술 프로필 내 메타 데이터 항목을 사용 하 여 시간차를 설정 합니다. 기울이기 값은 초 단위로 제공 되며 기본값은 0입니다. 최대값은 3600 (1 시간)입니다.

예를 들어 `TokenNotBeforeSkewInSeconds` 가 초로 설정 된 경우 `120` :

- 토큰은 13:05:10 UTC에 발급 됩니다.
- 토큰은 13:03:10 UTC에서 유효 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>날짜 및 시간에서 밀리초를 제거 합니다.

SAML 응답 내의 datetime 값에서 밀리초를 제거할지 여부를 지정할 수 있습니다 (IssueInstant, NotBefore, NotOnOrAfter 및 AuthnInstant 포함). 밀리초를 제거 하려면 `RemoveMillisecondsFromDateTime
` 신뢰 당사자 내에서 메타 데이터 키를 설정 합니다. 가능한 값은 `false` (기본값) 또는 `true` 입니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C 발급자 ID

서로 다른 값에 따라 달라 지는 여러 SAML 응용 프로그램이 있는 경우 `entityID` 신뢰 당사자 `issueruri` 파일의 값을 재정의할 수 있습니다. 발급자 URI를 재정의 하려면 기본 파일에서 "Saml2AssertionIssuer" ID를 사용 하 여 기술 프로필을 복사 하 고 값을 재정의 `issueruri` 합니다.

> [!TIP]
> `<ClaimsProviders>`기본에서 섹션을 복사 하 고 클레임 공급자 (, 및) 내에서 이러한 요소 `<DisplayName>Token Issuer</DisplayName>` 를 유지 `<TechnicalProfile Id="Saml2AssertionIssuer">` `<DisplayName>Token Issuer</DisplayName>` 합니다.
 
예제:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>세션 관리

`UseTechnicalProfileForSessionManagement`요소와 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider)를 사용 하 여 Azure AD B2C와 SAML 신뢰 당사자 응용 프로그램 간의 세션을 관리할 수 있습니다.

## <a name="debug-the-saml-protocol"></a>SAML 프로토콜 디버깅

서비스 공급자와의 통합을 구성 하 고 디버그 하는 데 도움이 되도록 SAML 프로토콜에 대 한 브라우저 확장을 사용할 수 있습니다. 예를 들어 Chrome 용 [Saml DevTools 확장](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) , FireFox 용 [saml 추적 기능](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 또는에 [지 또는 IE 개발자 도구](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)를 사용할 수 있습니다.

이러한 도구를 사용 하 여 응용 프로그램과 Azure AD B2C 간의 통합을 확인할 수 있습니다. 다음은 그 예입니다. 

* SAML 요청에 서명이 포함 되어 있는지 확인 하 고 권한 부여 요청에 서명 하는 데 사용 되는 알고리즘을 결정 합니다.
* Azure AD B2C에서 오류 메시지를 반환 하는지 확인 합니다.
* 어설션 섹션이 암호화 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- SAML 프로토콜에 대 한 자세한 내용은 [OASIS 웹 사이트](https://www.oasis-open.org/)를 확인 하세요.
- [Azure AD B2C GitHub 커뮤니티](https://github.com/azure-ad-b2c/saml-sp-tester)리포지토리에서 SAML 테스트 웹 앱을 가져옵니다.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
