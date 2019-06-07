---
title: Azure Active Directory B2C의 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의 | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 자체 어설션된 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512653"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD(Active Directory) B2C에서 사용자가 입력을 제공해야 하는 모든 상호 작용은 자체 어설션된 기술 프로필입니다. 등록 페이지, 로그인 페이지, 암호 재설정 페이지 등을 예로 들 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. 프로필이 자체 어설션되도록 하려면 **handler** 특성이 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 전자 메일 등록에 사용되는 자체 어설션된 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>입력 클레임

자체 어설션된 기술 프로필에서는 **InputClaims** 및 **InputClaimsTransformations** 요소를 사용하여 자체 어설션된 페이지(출력 클레임)에 표시되는 클레임의 값을 미리 채울 수 있습니다. 예를 들어 프로필 편집 정책의 사용자 경험에서는 먼저 Azure AD B2C 디렉터리 서비스에서 사용자 프로필을 읽습니다. 그러면 자체 어설션된 기술 프로필이 사용자 프로필에 저장된 사용자 데이터로 입력 클레임을 설정합니다. 이러한 클레임은 사용자 프로필에서 수집된 후 사용자에게 표시되며, 그러면 사용자가 기존 데이터를 편집할 수 있습니다.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소에는 사용자의 데이터를 수집하기 위해 표시할 클레임 목록이 포함됩니다. 몇 가지 값으로 출력 클레임을 미리 채우려면 앞에서 설명한 입력 클레임을 사용합니다. 이 요소는 기본값을 포함할 수도 있습니다. **OutputClaims**의 클레임 순서는 Azure AD B2C가 화면에서 클레임을 렌더링하는 순서를 제어합니다. **DefaultValue** 특성은 클레임을 이전에 설정한 적이 없는 경우에만 적용됩니다. 그러나 이전 오케스트레이션 단계에서 클레임을 설정한 경우에는 사용자가 값을 비워 두더라도 기본값이 적용되지 않습니다. 기본값을 강제로 사용하려면 **AlwaysUseDefaultValue** 특성을 `true`로 설정합니다. 사용자가 특정 출력 클레임에 대해 값을 입력하도록 강제 지정하려면 **OutputClaims** 요소의 **Required** 특성을 `true`로 설정합니다.

`TextBox`, `DropdownSingleSelect` 등 Azure AD B2C에서 지원하는 모든 사용자 입력 유형에 대해 **OutputClaims** 컬렉션의 **ClaimType** 요소는 **UserInputType** 요소로 설정해야 합니다. 또는 **OutputClaim** 요소를 **DefaultValue**로 설정해야 합니다.  

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

다음 출력 클레임은 항상 `live.com`으로 설정됩니다.

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>사용 사례

출력 클레임은 다음의 네 가지 시나리오에서 사용됩니다.

- **사용자로부터 출력 클레임 수집** - 생년월일 등의 정보를 사용자로부터 수집해야 할 때는 클레임을 **OutputClaims** 컬렉션에 추가해야 합니다. 사용자에게 표시되는 클레임은 `TextBox`, `DropdownSingleSelect` 등의 **UserInputType**을 지정해야 합니다. 자체 어설션된 기술 프로필이 같은 클레임을 출력하는 유효성 검사 기술 프로필을 포함하는 경우 Azure AD B2C는 클레임을 사용자에게 표시하지 않습니다. 사용자에게 표시할 출력 클레임이 없으면 Azure AD B2C는 기술 프로필을 건너뜁니다.
- **출력 클레임에서 기본값 설정** - 사용자 로부터 데이터를 수집하거나 유효성 검사 기술 프로필에서 데이터를 반환하지 않는 경우입니다. `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필은 **executed-SelfAsserted-Input** 클레임을 `true`로 설정합니다.
- **유효성 검사 기술 프로필이 출력 클레임 반환** - 기술 프로필이 일부 클레임을 반환하는 유효성 검사 기술 프로필을 호출할 수 있습니다. 해당 클레임을 확장하여 사용자 경험의 다음 오케스트레이션 단계로 반환할 수 있습니다. 로컬 계정으로 로그인할 때 자체 어설션된 기술 프로필 `SelfAsserted-LocalAccountSignin-Email`이 유효성 검사 기술 프로필 `login-NonInteractive`를 호출하는 경우를 예로 들어 보겠습니다. 이 기술 프로필은 사용자 자격 증명의 유효성을 검사하며 사용자 프로필도 반환합니다. 즉, 'userPrincipalName', 'displayName', 'givenName', 'surName' 등의 프로필을 반환할 수 있습니다.
- **출력 클레임 변환을 통해 클레임 출력**

다음 예제에서 자체 어설션된 기술 프로필 `LocalAccountSignUpWithLogonEmail`은 출력 클레임 사용 방식을 보여 주며 **executed-SelfAsserted-Input**을 `true`로 설정합니다. `objectId`, `authenticationSource`, `newUser` 클레임은 유효성 검사 기술 프로필 `AAD-UserWriteUsingLogonEmail`의 출력이며 사용자에게는 표시되지 않습니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>영구 클레임

**PersistedClaims** 요소가 없으면 자체 어설션된 기술 프로필은 Azure AD B2C에 데이터를 유지하지 않습니다. 대신 데이터 유지를 수행하는 유효성 검사 기술 프로필을 호출합니다. 등록 정책이 자체 어설션된 기술 프로필 `LocalAccountSignUpWithLogonEmail`을 사용하여 새 사용자 프로필을 수집하는 경우를 예로 들어 보겠습니다. `LocalAccountSignUpWithLogonEmail` 기술 프로필은 Azure AD B2C에 계정을 만들기 위해 유효성 검사 기술 프로필을 호출합니다.

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용됩니다. 유효성 검사 기술 프로필의 입력 클레임은 자체 어설션된 기술 프로필의 출력 클레임에 표시되어야 합니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사하며, 사용자에게 오류를 반환할 수 있습니다. 

[Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md) 기술 프로필과 같이 정책에 포함된 어떤 기술 프로필이든 유효성 검사 기술 프로필로 사용될 수 있습니다. 위의 예제에서 `LocalAccountSignUpWithLogonEmail` 기술 프로필은 signinName이 디렉터리에 없는지 유효성을 검사합니다. signinName이 디렉터리에 없으면 유효성 검사 기술 프로필은 로컬 계정을 만들고 objectId, authenticationSource, newUser를 반환합니다. `SelfAsserted-LocalAccountSignin-Email` 기술 프로필은 사용자 자격 증명의 유효성을 검사하기 위해 `login-NonInteractive` 유효성 검사 기술 프로필을 호출합니다.

비즈니스 논리를 사용하여 REST API 기술 프로필을 호출하거나, 입력 클레임을 덮어쓰거나, 회사 LOB(기간 업무) 애플리케이션과 추가로 통합하여 사용자 데이터를 보강할 수도 있습니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 참조하세요.

## <a name="metadata"></a>Metadata

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| setting.showContinueButton | 아닙니다. | 계속 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.showCancelButton | 아닙니다. | 취소 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.operatingMode | 아닙니다. | 로그인 페이지의 경우 이 속성은 입력 유효성 검사, 오류 메시지 등 username 필드의 동작을 제어합니다. 필요한 값은 `Username` 또는 `Email`입니다. |
| ContentDefinitionReferenceId | 예 | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| EnforceEmailVerification | 아닙니다. | 등록 또는 프로필 편집의 경우 전자 메일 확인을 적용합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. | 
| setting.showSignupLink | 아닙니다. | 등록 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.retryLimit | 아닙니다. | 사용자가 유효성 검사 기술 프로필과의 일치 여부를 확인하는 데이터의 입력을 시도할 수 있는 횟수를 제어합니다. 사용자가 이미 있는 계정으로 등록을 계속 시도할 수 있는 제한 횟수를 예로 들 수 있습니다.
| SignUpTarget | 아닙니다. | 등록 대상 교환 식별자입니다. 사용자가 등록 단추를 클릭하면 Azure AD B2C는 지정된 교환 식별자를 실행합니다. |

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.













