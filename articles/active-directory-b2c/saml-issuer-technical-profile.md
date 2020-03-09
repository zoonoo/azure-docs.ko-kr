---
title: 사용자 지정 정책에서 SAML 발급자의 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 SAML (Security Assertion Markup Language 토큰) 발급자에 대 한 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f038868e80b600c805a1f33a54f9d0032e81dab8
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933194"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 SAML 토큰 발급자의 기술 프로필을 정의 합니다.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 각 인증 흐름을 처리할 때 여러 유형의 보안 토큰을 내보냅니다. SAML 토큰 발급자의 기술 프로필은 신뢰 당사자 응용 프로그램 (서비스 공급자)에 다시 반환 되는 SAML 토큰을 내보냅니다. 일반적으로 이 기술 프로필은 사용자 경험의 마지막 오케스트레이션 단계입니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. **OutputTokenFormat** 요소를 `SAML2`로 설정합니다.

다음 예제는 `Saml2AssertionIssuer`의 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>입력, 출력 및 유지 클레임

**InputClaims**, **OutputClaims** 및 **PersistClaims** 요소가 비어 있거나 없습니다. **InutputClaimsTransformations** 및 **OutputClaimsTransformations** 요소도 없습니다.

## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| IssuerUri | 아니요 | SAML 응답에 표시 되는 발급자 이름입니다. 값은 신뢰 당사자 응용 프로그램에 구성 된 이름과 같아야 합니다. |

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| MetadataSigning | 예 | SAML 메타데이터에 서명을 하는 데 사용할 X509 인증서(RSA 키 집합)입니다. Azure AD B2C는 이 키를 사용하여 메타데이터에 서명을 합니다. |
| SamlMessageSigning| 예| SAML 메시지에 서명 하는 데 사용할 X509 인증서 (RSA 키 집합)를 지정 합니다. Azure AD B2C는이 키를 사용 하 여 신뢰 당사자에 게 보낼 응답 `<samlp:Response>`에 서명 합니다.|

## <a name="session-management"></a>세션 관리

신뢰 당사자 응용 프로그램 간에 Azure AD B2C SAML 세션을 구성 하려면 `UseTechnicalProfileForSessionManagement` 요소의 특성과 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 세션을 참조 하세요.












