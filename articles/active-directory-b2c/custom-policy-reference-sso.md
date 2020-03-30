---
title: 사용자 지정 정책을 사용하는 단일 사인온 세션 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 정책을 사용하여 SSO 세션을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246033"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 Single Sign-On 세션 관리

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)의 단일 사인온(SSO) 세션 관리를 통해 관리자는 사용자가 이미 인증된 후 사용자와의 상호 작용을 제어할 수 있습니다. 예를 들어 관리자는 ID 공급자의 선택이 표시되는지 여부 또는 로컬 계정 세부 정보를 다시 입력해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

SSO 세션 관리에는 두 가지 부분이 있습니다. 첫 번째 부분은 Azure AD B2C와 사용자의 직접 상호 작용을 다루고, 다른 부분은 Facebook과 같은 외부 당사자와 사용자의 상호 작용을 다룹니다. Azure AD B2C는 외부 당사자에 의해 보관될 수 있는 SSO 세션을 재정의하거나 무시하지 않습니다. 대신 Azure AD B2C를 통해 외부 파티에 도착하는 경로는 "기억"되므로 사용자가 소셜 또는 엔터프라이즈 ID 공급자를 선택하도록 다시 프롬프트할 필요가 없습니다. 최종 SSO 결정은 외부 당사자에서 유지됩니다.

SSO 세션 관리는 사용자 지정 정책에서 다른 기술 프로필과 동일한 의미 체계를 사용합니다. 오케스트레이션 단계를 실행하면 이 단계와 관련된 기술 프로필을 `UseTechnicalProfileForSessionManagement` 참조에 대해 쿼리합니다. 참조된 SSO 세션 공급자가 있는 경우 사용자가 세션 참가자인지를 확인합니다. 그렇다면 SSO 세션 공급자를 사용하여 세션을 다시 채웁니다. 마찬가지로 오케스트레이션 단계의 실행이 완료되면 SSO 세션 공급자가 지정된 경우 이 공급자를 사용하여 세션에서 정보를 저장합니다.

Azure AD B2C는 사용할 수 있는 SSO 세션 공급자 수를 정의합니다.

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

기술 프로필의 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 요소를 사용하여 SSO 관리 클래스를 지정합니다.

## <a name="input-claims"></a>입력 클레임

`InputClaims` 요소가 비어 있거나 없습니다.

## <a name="persisted-claims"></a>지속된 클레임

응용 프로그램에 반환하거나 후속 단계에서 사전 조건에서 사용해야 하는 클레임은 세션에 저장하거나 디렉터리의 사용자 프로필에서 읽은 내용을 통해 보강되어야 합니다. 지속클레임을 사용하면 인증 여정이 누락된 클레임에 실패하지 않습니다. 세션에서 클레임을 추가하려면 기술 프로필의 `<PersistedClaims>` 요소를 사용합니다. 세션을 다시 채우는 데 공급자를 사용하는 경우 유지되는 클레임은 클레임 모음에 추가됩니다.

## <a name="output-claims"></a>출력 클레임

는 `<OutputClaims>` 세션에서 클레임을 검색하는 데 사용됩니다.

## <a name="session-providers"></a>세션 공급자

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

이름이 지정하는 대로 이 공급자는 아무 작업도 수행하지 않습니다. 특정 기술 프로필에 대한 SSO 동작을 무시하기 위해 이 공급자를 사용할 수 있습니다. 다음 `SM-Noop` 기술 프로필은 [사용자 지정 정책 시작 팩에](custom-policy-get-started.md#custom-policy-starter-pack)포함되어 있습니다.

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

세션에서 클레임을 저장하는 데 이 공급자를 사용할 수 있습니다. 이 공급자는 로컬 계정을 관리하기 위해 사용되는 기술 프로필에서 일반적으로 참조됩니다. 다음 `SM-AAD` 기술 프로필은 [사용자 지정 정책 시작 팩에](custom-policy-get-started.md#custom-policy-starter-pack)포함되어 있습니다.

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

다음 `SM-MFA` 기술 프로필은 [사용자 지정 정책 시작 팩에](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`포함되어 있습니다. 이 기술 프로필은 다단계 인증 세션을 관리합니다.

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

이 공급자는 "ID 공급자 선택" 화면을 표시 하지 않는 데 사용 됩니다. 일반적으로 Facebook 등의 외부 ID 공급자에 구성된 기술 프로필에서 참조됩니다. 다음 `SM-SocialLogin` 기술 프로필은 [사용자 지정 정책 시작 팩에](custom-policy-get-started.md#custom-policy-starter-pack)포함되어 있습니다.

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

| 특성 | 필수 | 설명|
| --- | --- | --- |
| 항상인가클레임로부터제공자 | 예 | 현재 사용되지 않는 무시할 수 있습니다. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

이 공급자는 공급자 응용 프로그램 또는 페더레이션된 SAML ID 공급자 간의 Azure AD B2C SAML 세션을 관리하는 데 사용됩니다. SAML ID 공급자 세션을 저장하기 위해 SSO `RegisterServiceProviders` 공급자를 `false`사용하는 경우 를 로 설정해야 합니다. 다음 `SM-Saml-idp` 기술 프로필은 [SAML 기술 프로필에서](saml-technical-profile.md)사용됩니다.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

B2C SAML 세션을 저장하기 위해 공급자를 사용하는 경우 를 로 `RegisterServiceProviders` 설정해야 합니다. `true` SAML 세션 로그아웃을 완료하는 데 `SessionIndex` 및 `NameID`가 필요합니다.

다음 `SM-Saml-idp` 기술 프로필은 [SAML 발급자 기술 프로필에서](saml-issuer-technical-profile.md) 사용됩니다.

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명|
| --- | --- | --- |
| IncludeSessionIndex | 예 | 현재 사용되지 않는 무시할 수 있습니다.|
| RegisterServiceProviders | 예 | 공급자가 어설션을 발급한 모든 SAML 서비스 공급자를 등록해야 함을 의미합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다.|



