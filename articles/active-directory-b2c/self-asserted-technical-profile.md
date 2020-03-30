---
title: 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 자체 어설션된 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332502"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자가 입력을 제공해야 하는 Azure Active Directory B2C(Azure AD B2C)의 모든 상호 작용은 자체 어설션된 기술 프로필입니다. 등록 페이지, 로그인 페이지, 암호 재설정 페이지 등을 예로 들 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. 프로필이 자체 어설션되도록 하려면 **handler** 특성이 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 전자 메일 등록에 사용되는 자체 어설션된 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>입력 클레임

자체 어설션된 기술 프로필에서 **InputClaims** 및 **InputClaims변환** 요소를 사용하여 자체 어설션된 페이지(클레임 표시)에 나타나는 클레임 값을 미리 채울 수 있습니다. 예를 들어 프로필 편집 정책의 사용자 경험에서는 먼저 Azure AD B2C 디렉터리 서비스에서 사용자 프로필을 읽습니다. 그러면 자체 어설션된 기술 프로필이 사용자 프로필에 저장된 사용자 데이터로 입력 클레임을 설정합니다. 이러한 클레임은 사용자 프로필에서 수집된 후 사용자에게 표시되며, 그러면 사용자가 기존 데이터를 편집할 수 있습니다.

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

## <a name="display-claims"></a>클레임 표시

디스플레이 클레임 기능은 현재 **미리 보기**상태입니다.

**DisplayClaims** 요소에는 사용자로부터 데이터를 수집하기 위해 화면에 표시할 클레임 목록이 포함되어 있습니다. 표시 클레임값을 미리 채우려면 앞에서 설명한 입력 클레임을 사용합니다. 이 요소는 기본값을 포함할 수도 있습니다.

**DisplayClaims의** 클레임 순서는 Azure AD B2C가 화면에서 클레임을 렌더링하는 순서를 지정합니다. 사용자가 특정 클레임에 대한 값을 제공하도록 설정하려면 **DisplayClaim** 요소의 `true` **필수** 특성을 로 설정합니다.

**DisplayClaims** 컬렉션의 **클레임 유형** 요소는 Azure AD B2C에서 지원하는 모든 사용자 입력 유형으로 **UserInputType** 요소를 설정해야 합니다. 예를 들어 `TextBox` 또는 `DropdownSingleSelect`입니다.

### <a name="add-a-reference-to-a-displaycontrol"></a>디스플레이 컨트롤에 참조 추가

디스플레이 클레임 컬렉션에서 만든 [DisplayControl에](display-controls.md) 대한 참조를 포함할 수 있습니다. 디스플레이 컨트롤은 특별한 기능을 가지고 있으며 Azure AD B2C 백 엔드 서비스와 상호 작용하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 유효성 검사 기술 프로필을 호출하는 페이지에서 작업을 수행할 수 있습니다. 예를 들어 이메일 주소, 전화 번호 또는 고객 충성도 번호를 확인합니다.

다음 예제에서는 `TechnicalProfile` 디스플레이 컨트롤이 있는 표시 클레임의 사용을 보여 줍니다.

* 첫 번째 표시 클레임은 `emailVerificationControl` 전자 메일 주소를 수집하고 확인하는 디스플레이 컨트롤을 참조합니다.
* 다섯 번째 표시 클레임은 `phoneVerificationControl` 전화 번호를 수집하고 확인하는 디스플레이 컨트롤을 참조합니다.
* 다른 표시 클레임은 사용자로부터 수집할 클레임 유형입니다.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

앞서 언급했듯이 디스플레이 컨트롤을 참조하는 디스플레이 클레임은 전자 메일 주소 확인과 같은 자체 유효성 검사를 실행할 수 있습니다. 또한 자체 어설션된 페이지는 유효성 검사 기술 프로필을 사용하여 다음 오케스트레이션 단계로 이동하기 전에 사용자 입력(클레임 유형 또는 디스플레이 컨트롤)을 포함하여 전체 페이지의 유효성을 검사합니다.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>디스플레이 클레임 및 출력 클레임의 사용을 신중하게 결합

자체 어설션된 기술 프로필에 하나 이상의 **DisplayClaim** 요소를 지정하는 경우 화면에 표시하고 사용자로부터 수집하려는 *모든* 클레임에 대해 DisplayClaim을 사용해야 합니다. 하나 이상의 디스플레이 클레임을 포함하는 자체 어설션된 기술 프로필에는 출력 클레임이 표시되지 않습니다.

`age` 클레임이 기본 정책의 **출력** 클레임으로 정의되는 다음 예제를 고려합니다. 자체 주장된 기술 프로필에 디스플레이 클레임을 `age` 추가하기 전에 사용자의 데이터 수집을 위해 클레임이 화면에 표시됩니다.

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

해당 기준을 상속하는 리프 정책이 이후에 `officeNumber` **표시** 클레임으로 지정되는 경우:

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

기본 `age` 정책의 클레임은 더 이상 사용자에게 화면에 표시되지 않으며 효과적으로 "숨김"입니다. 클레임을 `age` 표시하고 사용자로부터 연령 값을 수집하려면 `age` **DisplayClaim**을 추가해야 합니다.

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소에는 다음 오케스트레이션 단계로 반환할 클레임 목록이 포함되어 있습니다. **DefaultValue** 특성은 클레임이 설정된 적이 없는 경우에만 적용됩니다. 이전 오케스트레이션 단계에서 설정된 경우 사용자가 값을 비워 두더라도 기본값은 적용되지 않습니다. 기본값을 강제로 사용하려면 **AlwaysUseDefaultValue** 특성을 `true`로 설정합니다.

보안상의 이유로 암호 클레임`UserInputType` `Password`값(설정됨)은 자체 주장된 기술 프로필의 유효성 검사 기술 프로필에서만 사용할 수 있습니다. 다음 오케스트레이션 단계에서는 암호 클레임을 사용할 수 없습니다. 

> [!NOTE]
> 이전 버전의 IEF(ID 환경 프레임워크)에서는 출력 클레임이 사용자로부터 데이터를 수집하는 데 사용되었습니다. 사용자로부터 데이터를 수집하려면 **대신 DisplayClaims** 컬렉션을 사용합니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="when-you-should-use-output-claims"></a>출력 클레임을 사용해야 하는 경우

자체 어설션된 기술 프로필에서 출력 클레임 컬렉션은 클레임을 다음 오케스트레이션 단계로 반환합니다.

다음과 같은 경우 출력 클레임을 사용합니다.

- **클레임은 출력 클레임 변환에 의해 출력됩니다.**
- 사용자로부터 데이터를 수집하거나 유효성 검사 기술 프로필에서 데이터를 반환하지 않고 **출력 클레임에서 기본값을 설정합니다.** `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필은 **executed-SelfAsserted-Input** 클레임을 `true`로 설정합니다.
- **유효성 검사 기술 프로필이 출력 클레임 반환** - 기술 프로필이 일부 클레임을 반환하는 유효성 검사 기술 프로필을 호출할 수 있습니다. 해당 클레임을 확장하여 사용자 경험의 다음 오케스트레이션 단계로 반환할 수 있습니다. 로컬 계정으로 로그인할 때 자체 어설션된 기술 프로필 `SelfAsserted-LocalAccountSignin-Email`이 유효성 검사 기술 프로필 `login-NonInteractive`를 호출하는 경우를 예로 들어 보겠습니다. 이 기술 프로필은 사용자 자격 증명의 유효성을 검사하며 사용자 프로필도 반환합니다. 즉, 'userPrincipalName', 'displayName', 'givenName', 'surName' 등의 프로필을 반환할 수 있습니다.
- **디스플레이 컨트롤은 출력 클레임을 반환합니다** - 기술 프로필에 [디스플레이 컨트롤에](display-controls.md)대한 참조가 있을 수 있습니다. 디스플레이 컨트롤은 확인된 이메일 주소와 같은 일부 클레임을 반환합니다. 해당 클레임을 확장하여 사용자 경험의 다음 오케스트레이션 단계로 반환할 수 있습니다. 디스플레이 제어 기능은 현재 **미리 보기**상태입니다.

다음 예제에서는 표시 클레임 및 출력 클레임을 모두 사용하는 자체 어설션된 기술 프로필의 사용을 보여 줍니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>영구 클레임

Persisted클레임 요소는 사용되지 않습니다. 자체 어설션된 기술 프로필은 데이터를 Azure AD B2C에 유지하지 않습니다. 대신 데이터 유지를 수행하는 유효성 검사 기술 프로필을 호출합니다. 등록 정책이 자체 어설션된 기술 프로필 `LocalAccountSignUpWithLogonEmail`을 사용하여 새 사용자 프로필을 수집하는 경우를 예로 들어 보겠습니다. `LocalAccountSignUpWithLogonEmail` 기술 프로필은 Azure AD B2C에 계정을 만들기 위해 유효성 검사 기술 프로필을 호출합니다.

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용됩니다. 유효성 검사 기술 프로필의 입력 클레임은 자체 어설션된 기술 프로필의 출력 클레임에 표시되어야 합니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사하며, 사용자에게 오류를 반환할 수 있습니다.

[Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md) 기술 프로필과 같이 정책에 포함된 어떤 기술 프로필이든 유효성 검사 기술 프로필로 사용될 수 있습니다. 위의 예제에서 `LocalAccountSignUpWithLogonEmail` 기술 프로필은 signinName이 디렉터리에 없는지 유효성을 검사합니다. signinName이 디렉터리에 없으면 유효성 검사 기술 프로필은 로컬 계정을 만들고 objectId, authenticationSource, newUser를 반환합니다. `SelfAsserted-LocalAccountSignin-Email` 기술 프로필은 사용자 자격 증명의 유효성을 검사하기 위해 `login-NonInteractive` 유효성 검사 기술 프로필을 호출합니다.

비즈니스 논리를 사용하여 REST API 기술 프로필을 호출하거나, 입력 클레임을 덮어쓰거나, 회사 LOB(기간 업무) 애플리케이션과 추가로 통합하여 사용자 데이터를 보강할 수도 있습니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 참조하세요.

## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 설정.운영모드 <sup>1</sup>| 예 | 로그인 페이지의 경우 이 속성은 입력 유효성 검사, 오류 메시지 등 username 필드의 동작을 제어합니다. 필요한 값은 `Username` 또는 `Email`입니다.  |
| 허용 생성클레임클레임허용값| 예| null 값으로 클레임을 생성할 수 있도록 허용합니다. 예를 들어 사용자가 확인란을 선택하지 않는 경우를 예로 들 수 있습니다.|
| ContentDefinitionReferenceId | yes | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| EnforceEmailVerification | 예 | 등록 또는 프로필 편집의 경우 전자 메일 확인을 적용합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.retryLimit | 예 | 사용자가 유효성 검사 기술 프로필에 대해 확인된 데이터를 제공하려고 시도할 수 있는 횟수를 제어합니다. 사용자가 이미 있는 계정으로 등록을 계속 시도할 수 있는 제한 횟수를 예로 들 수 있습니다.
| 가입 대상 <sup>1</sup>| 예 | 등록 대상 교환 식별자입니다. 사용자가 등록 단추를 클릭하면 Azure AD B2C는 지정된 교환 식별자를 실행합니다. |
| setting.showCancelButton | 예 | 취소 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.showContinueButton | 예 | 계속 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| 세팅.쇼로그인링크 <sup>2</sup>| 예 | 등록 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| 세팅.잊어버린암호링크위치 <sup>2</sup>| 예| 잊어버린 암호 링크를 표시합니다. 가능한 값: `AfterInput` (기본값) 링크가 페이지 하단에 표시되거나 `None` 잊어버린 암호 링크를 제거합니다.|
| 설정.enableRememberMe <sup>2</sup>| 예| 로그인 [유지](custom-policy-keep-me-signed-in.md) 확인란을 표시합니다. 가능한 값: `true` `false` " 또는 (기본값) . |
| 클레임 해결내 처리 포함  | 예 | 입력 및 출력 클레임의 경우 [클레임 해결이](claim-resolver-overview.md) 기술 프로필에 포함되는지 여부를 지정합니다. 가능한 값: `true` `false`  " 또는 (기본값) . 기술 프로필에서 클레임 확인자를 사용하려면 이 것을 `true`로 설정합니다. |

참고:
1. 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 형식 `unifiedssp`또는 `unifiedssd`에 사용할 수 있습니다.
1. 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 형식 `unifiedssp`또는 `unifiedssd`에 사용할 수 있습니다. [페이지 레이아웃 버전](page-layout.md) 1.1.0 이상.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.
