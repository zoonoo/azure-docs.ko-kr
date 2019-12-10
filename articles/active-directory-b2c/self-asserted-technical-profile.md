---
title: 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 자체 어설션된 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950904"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 자체 어설션된 기술 프로필을 정의 합니다.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자가 입력을 제공 해야 하는 Azure Active Directory B2C (Azure AD B2C)의 모든 상호 작용은 자체 어설션된 기술 프로필입니다. 예를 들어 등록 페이지, 로그인 페이지 또는 암호 재설정 페이지가 있습니다.

## <a name="protocol"></a>Protocol (Protokoll)

**프로토콜** 요소의 **Name** 특성을 `Proprietary`으로 설정 해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다 (자체 어설션된의 경우). `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 전자 메일 등록을 위한 자체 어설션된 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>입력 클레임

자체 어설션된 기술 프로필에서 **Inputclaims** 및 **InputClaimsTransformations** 요소를 사용 하 여 자체 어설션된 페이지 (출력 클레임)에 표시 되는 클레임 값을 미리 채울 수 있습니다. 예를 들어, 프로필 편집 정책에서 사용자 경험은 먼저 Azure AD B2C directory 서비스에서 사용자 프로필을 읽고, 자체 어설션된 기술 프로필은 사용자 프로필에 저장 된 사용자 데이터를 사용 하 여 입력 클레임을 설정 합니다. 이러한 클레임은 사용자 프로필에서 수집 된 다음 기존 데이터를 편집할 수 있는 사용자에 게 제공 됩니다.

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

**Outputclaims** 요소는 사용자 로부터 데이터를 수집 하기 위해 제공할 클레임 목록을 포함 합니다. 일부 값을 사용 하 여 출력 클레임을 미리 채우려면 앞에서 설명한 입력 클레임을 사용 합니다. 요소에는 기본값도 포함 될 수 있습니다. **Outputclaims** 의 클레임 순서는 Azure AD B2C 화면에서 클레임을 렌더링 하는 순서를 제어 합니다. **DefaultValue** 특성은 클레임이 이전에 설정 된 적이 없는 경우에만 적용 됩니다. 그러나 이전 오케스트레이션 단계 이전에 설정 된 경우 사용자가 값을 비워 둔 경우에도 기본값은 적용 되지 않습니다. 기본값을 강제로 사용 하려면 **AlwaysUseDefaultValue** 특성을 `true`로 설정 합니다. 사용자가 특정 출력 클레임에 대 한 값을 제공 하도록 하려면 **outputclaims** 요소의 **필수** 특성을 `true`로 설정 합니다.

**Outputclaims** 컬렉션의 **ClaimType** 요소는 **userinputtype** 요소를 Azure AD B2C에서 지 원하는 사용자 입력 형식 (예: `TextBox` 또는 `DropdownSingleSelect`)으로 설정 해야 합니다. 또는 **Outputclaim** 요소가 **DefaultValue**를 설정 해야 합니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **OutputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

다음 출력 클레임은 항상 `live.com`로 설정 됩니다.

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Használati eset

출력 클레임에 대 한 네 가지 시나리오는 다음과 같습니다.

- **사용자의 출력 클레임 수집** -사용자 로부터 정보를 수집 해야 하는 경우 (예: 생년월일) 클레임을 **outputclaims** 컬렉션에 추가 해야 합니다. 사용자에 게 제공 되는 클레임은 `TextBox` 또는 `DropdownSingleSelect`와 같이 **Userinputtype**을 지정 해야 합니다. 자체 어설션된 기술 프로필에 동일한 클레임을 출력 하는 유효성 검사 기술 프로필이 포함 된 경우 Azure AD B2C는 사용자에 게 클레임을 제공 하지 않습니다. 사용자에 게 제공할 출력 클레임이 없는 경우 Azure AD B2C는 기술 프로필을 건너뜁니다.
- 사용자 로부터 데이터를 수집 하거나 유효성 검사 기술 프로필의 데이터를 반환 하지 않고 **출력 클레임에 기본값을 설정** 합니다. `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필은 **SelfAsserted-입력** 클레임을 `true`으로 설정 합니다.
- **유효성 검사 기술 프로필은 출력 클레임을 반환** 합니다. 기술 프로필은 일부 클레임을 반환 하는 유효성 검사 기술 프로필을 호출할 수 있습니다. 클레임을 버블링 하 고 사용자 경험에서 다음 오케스트레이션 단계로 돌아갈 수 있습니다. 예를 들어 로컬 계정으로 로그인 하는 경우 `SelfAsserted-LocalAccountSignin-Email` 이라는 자체 어설션된 기술 프로필은 `login-NonInteractive`이라는 유효성 검사 기술 프로필을 호출 합니다. 이 기술 프로필은 사용자 자격 증명의 유효성을 검사 하 고 사용자 프로필도 반환 합니다. 예: ' userPrincipalName ', ' displayName ', ' givenName ' 및 ' s t r i p '.
- **출력 클레임 변환을 통해 클레임 출력**

다음 예제에서 `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필은 출력 클레임의 사용을 보여 주고 **SelfAsserted-Input** 을 `true`로 설정 합니다. `objectId`, `authenticationSource``newUser` 클레임은 `AAD-UserWriteUsingLogonEmail` 유효성 검사 기술 프로필의 출력 이며 사용자에 게 표시 되지 않습니다.

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

## <a name="persist-claims"></a>클레임 유지

**Persistedclaims** 요소가 없는 경우 자체 어설션된 기술 프로필은 Azure AD B2C 데이터를 유지 하지 않습니다. 대신 데이터 유지를 담당 하는 유효성 검사 기술 프로필을 호출 합니다. 예를 들어 등록 정책은 `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필을 사용 하 여 새 사용자 프로필을 수집 합니다. `LocalAccountSignUpWithLogonEmail` 기술 프로필은 유효성 검사 기술 프로필을 호출 하 여 Azure AD B2C에서 계정을 만듭니다.

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 참조 기술 프로필에 대 한 일부 또는 모든 출력 클레임의 유효성을 검사 하는 데 사용 됩니다. 유효성 검사 기술 프로필의 입력 클레임은 자체 어설션된 기술 프로필의 출력 클레임에 표시 되어야 합니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사 하 고 사용자에 게 오류를 반환할 수 있습니다.

유효성 검사 기술 프로필은 정책의 기술 프로필 (예: [Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md) 기술 프로필) 일 수 있습니다. 이전 예제에서 `LocalAccountSignUpWithLogonEmail` 기술 프로필은 signinName가 디렉터리에 존재 하지 않는지 확인 합니다. 그렇지 않은 경우 유효성 검사 기술 프로필은 로컬 계정을 만들고 objectId, authenticationSource, newUser를 반환 합니다. `SelfAsserted-LocalAccountSignin-Email` 기술 프로필은 `login-NonInteractive` 유효성 검사 기술 프로필을 호출 하 여 사용자 자격 증명의 유효성을 검사 합니다.

비즈니스 논리를 사용 하 여 REST API 기술 프로필을 호출 하거나, 입력 클레임을 덮어쓰거나, 회사의 lob (기간 업무) 응용 프로그램과 통합 하 여 사용자 데이터를 보강 할 수도 있습니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md) 을 참조 하세요.

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| showContinueButton 설정 | Nem | 계속 단추를 표시 합니다. 가능한 값: `true` (기본값) 또는 `false` |
| 설정. showCancelButton | Nem | 취소 단추를 표시 합니다. 가능한 값: `true` (기본값) 또는 `false` |
| operatingMode 설정 | Nem | 로그인 페이지의 경우이 속성은 입력 유효성 검사 및 오류 메시지와 같은 사용자 이름 필드의 동작을 제어 합니다. 예상 값: `Username` 또는 `Email`. |
| ContentDefinitionReferenceId | Igen | 이 기술 프로필에 연결 된 [콘텐츠 정의](contentdefinitions.md) 의 식별자입니다. |
| EnforceEmailVerification | Nem | 등록 또는 프로필 편집의 경우는 전자 메일 확인을 적용 합니다. 가능한 값: `true` (기본값) 또는 `false`. |
| 설정. showSignupLink | Nem | 등록 단추를 표시 합니다. 가능한 값: `true` (기본값) 또는 `false` |
| retryLimit 설정 | Nem | 사용자가 유효성 검사 기술 프로필에 대해 확인 된 데이터를 제공 하기 위해 시도할 수 있는 횟수를 제어 합니다. 예를 들어 사용자는 이미 존재 하는 계정으로 등록을 시도 하 고 제한에 도달할 때까지 계속 시도 합니다.
| SignUpTarget | Nem | 등록 대상 교환 식별자입니다. 사용자가 등록 단추를 클릭 하면 지정 된 exchange 식별자를 Azure AD B2C 실행 합니다. |

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용 되지 않습니다.













