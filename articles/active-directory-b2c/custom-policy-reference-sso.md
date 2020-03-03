---
title: 사용자 지정 정책을 사용 하는 Single sign-on 세션 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 정책을 사용하여 SSO 세션을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bdea51c6cb53222f31a07906785a94073a0293a1
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226796"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 Single Sign-On 세션 관리

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)의 SSO (Single sign-on) 세션 관리를 통해 관리자는 사용자가 이미 인증 된 후 사용자와의 상호 작용을 제어할 수 있습니다. 예를 들어 관리자는 ID 공급자의 선택이 표시되는지 여부 또는 로컬 계정 세부 정보를 다시 입력해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

SSO 세션 관리에는 두 가지 부분이 있습니다. 첫 번째 부분은 Azure AD B2C와 사용자의 직접 상호 작용을 다루고, 다른 부분은 Facebook과 같은 외부 당사자와 사용자의 상호 작용을 다룹니다. Azure AD B2C는 외부 당사자에 의해 보관될 수 있는 SSO 세션을 재정의하거나 무시하지 않습니다. 외부 당사자에게 가져오는 Azure AD B2C를 통한 경로를 "기억"하여 사용자에게 해당 소셜 공급자 또는 엔터프라이즈 ID 공급자를 선택하라는 메시지를 다시 표시하지 않도록 합니다. 최종 SSO 결정은 외부 당사자에서 유지됩니다.

SSO 세션 관리는 사용자 지정 정책에서 다른 기술 프로필과 동일한 의미 체계를 사용합니다. 오케스트레이션 단계를 실행하면 이 단계와 관련된 기술 프로필을 `UseTechnicalProfileForSessionManagement` 참조에 대해 쿼리합니다. 참조된 SSO 세션 공급자가 있는 경우 사용자가 세션 참가자인지를 확인합니다. 그렇다면 SSO 세션 공급자를 사용하여 세션을 다시 채웁니다. 마찬가지로 오케스트레이션 단계의 실행이 완료되면 SSO 세션 공급자가 지정된 경우 이 공급자를 사용하여 세션에서 정보를 저장합니다.

Azure AD B2C는 사용할 수 있는 SSO 세션 공급자 수를 정의합니다.

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

기술 프로필의 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 요소를 사용하여 SSO 관리 클래스를 지정합니다.

## <a name="input-claims"></a>입력 클레임

`InputClaims` 요소가 비어 있거나 없습니다.

## <a name="persisted-claims"></a>지속형 클레임

응용 프로그램에 반환 되거나 후속 단계에서 사전 조건에서 사용 되어야 하는 클레임은 세션에 저장 되거나 디렉터리의 사용자 프로필에서 읽은 후에 야 합니다. 지속형 클레임을 사용 하면 인증 경험 누락 된 클레임에서 실패 하지 않습니다. 세션에서 클레임을 추가하려면 기술 프로필의 `<PersistedClaims>` 요소를 사용합니다. 세션을 다시 채우는 데 공급자를 사용하는 경우 유지되는 클레임은 클레임 모음에 추가됩니다.

## <a name="output-claims"></a>출력 클레임

`<OutputClaims>`는 세션에서 클레임을 검색 하는 데 사용 됩니다.

## <a name="session-providers"></a>세션 공급자

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

이름이 지정하는 대로 이 공급자는 아무 작업도 수행하지 않습니다. 특정 기술 프로필에 대한 SSO 동작을 무시하기 위해 이 공급자를 사용할 수 있습니다. 다음 `SM-Noop` 기술 프로필은 [사용자 지정 정책 시작 팩](custom-policy-get-started.md#custom-policy-starter-pack)에 포함 되어 있습니다.

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

세션에서 클레임을 저장하는 데 이 공급자를 사용할 수 있습니다. 이 공급자는 로컬 계정을 관리하기 위해 사용되는 기술 프로필에서 일반적으로 참조됩니다. 다음 `SM-AAD` 기술 프로필은 [사용자 지정 정책 시작 팩](custom-policy-get-started.md#custom-policy-starter-pack)에 포함 되어 있습니다.

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

다음 `SM-MFA` 기술 프로필은 [사용자 지정 정책 시작 팩](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`에 포함 되어 있습니다. 이 기술 프로필은 multi-factor authentication 세션을 관리 합니다.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

이 공급자는 "id 공급자 선택" 화면을 표시 하지 않는 데 사용 됩니다. 일반적으로 Facebook 등의 외부 ID 공급자에 구성된 기술 프로필에서 참조됩니다. 다음 `SM-SocialLogin` 기술 프로필은 [사용자 지정 정책 시작 팩](custom-policy-get-started.md#custom-policy-starter-pack)에 포함 되어 있습니다.

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>메타데이터

| attribute | 필수 | Description|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | 예 | 현재 사용 되지 않습니다 .를 무시할 수 있습니다. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

이 공급자는 신뢰 당사자 응용 프로그램 또는 페더레이션된 SAML id 공급자 간의 Azure AD B2C SAML 세션을 관리 하는 데 사용 됩니다. SSO 공급자를 사용 하 여 SAML id 공급자 세션을 저장 하는 경우 `RegisterServiceProviders`를 `false`으로 설정 해야 합니다. 다음 `SM-Saml-idp` 기술 프로필은 [SAML 기술 프로필](saml-technical-profile.md)에 사용 됩니다.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

B2C SAML 세션을 저장 하는 데 공급자를 사용 하는 경우 `RegisterServiceProviders`를 `true`으로 설정 해야 합니다. SAML 세션 로그아웃을 완료하는 데 `SessionIndex` 및 `NameID`가 필요합니다.

다음 `SM-Saml-idp` 기술 프로필은 [SAML 발급자 기술 프로필](connect-with-saml-service-providers.md) 에 사용 됩니다.

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>메타데이터

| attribute | 필수 | Description|
| --- | --- | --- |
| IncludeSessionIndex | 예 | 현재 사용 되지 않습니다 .를 무시할 수 있습니다.|
| RegisterServiceProviders | 예 | 공급자가 어설션을 발급한 모든 SAML 서비스 공급자를 등록해야 함을 의미합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다.|



