---
title: OTP(일회성 암호) 확인 사용
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책을 사용하여 OTP(일회성 암호) 시나리오를 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332776"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 일회성 암호 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 일회성 암호의 생성 및 확인을 관리하는 지원을 제공합니다. 기술 프로필을 사용하여 코드를 생성한 다음 나중에 해당 코드를 확인합니다.

일회용 암호 기술 프로필은 코드 확인 중에 오류 메시지를 반환할 수도 있습니다. **유효성 검사 기술 프로필을**사용하여 일회성 암호와의 통합을 설계합니다. 유효성 검사 기술 프로필은 일회용 암호 기술 프로필을 호출하여 코드를 확인합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 자체 어설션된 페이지에 오류 메시지가 표시됩니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **처리기** 특성에는 Azure AD B2C에서 사용하는 프로토콜 처리기 어셈블리의 정규화된 이름이 포함되어야 합니다.

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제에서는 일회성 암호 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>코드 생성

이 기술 프로필의 첫 번째 모드는 코드를 생성하는 것입니다. 다음은 이 모드에 대해 구성할 수 있는 옵션입니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소에는 일회성 암호 프로토콜 공급자에게 보내는 데 필요한 클레임 목록이 포함되어 있습니다. 청구 내 의 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| 클레임참조 Id | 필수 | 설명 |
| --------- | -------- | ----------- |
| identifier | yes | 나중에 코드를 확인해야 하는 사용자를 식별하는 식별자입니다. 일반적으로 이메일 주소 또는 전화 번호와 같이 코드가 전달되는 대상의 식별자로 사용됩니다. |

**InputClaims변환** 요소에는 입력 클레임을 수정하거나 일회성 암호 프로토콜 공급자로 보내기 전에 새 클레임을 생성하는 데 사용되는 **InputClaims변환** 요소의 컬렉션이 포함될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

OutputClaims 요소에는 일회성 암호 프로토콜 공급자가 생성한 클레임 목록이 포함되어 **있습니다.** 청구 내 의 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| 클레임참조 Id | 필수 | 설명 |
| --------- | -------- | ----------- |
| otpgenerated | yes | Azure AD B2C에서 세션을 관리하는 생성된 코드입니다. |

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정을 사용하여 코드 생성 모드를 구성할 수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 코드 익스피어레이션인초 | 예 | 코드 만료까지의 시간(초)입니다. 최소: `60`; 최대: `1200`; 기본값: `600`. |
| 코드길이 | 예 | 코드의 길이입니다. 기본값은 `6`입니다. |
| CharacterSet | 예 | 정규식에서 사용할 수 있는 형식이 지정된 코드에 대한 문자 집합입니다. `a-z0-9A-Z`)을 입력합니다. 기본값은 `0-9`입니다. 문자 집합에는 지정된 집합에 최소 10개의 서로 다른 문자가 포함되어야 합니다. |
| 숫자 리트리 시도 | 예 | 코드가 유효하지 않은 것으로 간주되기 전에 시도한 확인 횟수입니다. 기본값은 `5`입니다. |
| 작업(Operation) | yes | 수행할 작업입니다. 가능한 값: `GenerateCode`. |
| 재사용서코드 | 예 | 주어진 코드가 만료되지 않았고 여전히 유효할 때 새 코드를 생성하는 대신 중복 코드를 제공해야 하는지 여부입니다. 기본값은 `false`입니다. |

### <a name="example"></a>예제

다음 예제는 `TechnicalProfile` 코드를 생성하는 데 사용됩니다.

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인하는 것입니다. 다음은 이 모드에 대해 구성할 수 있는 옵션입니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소에는 일회성 암호 프로토콜 공급자에게 보내는 데 필요한 클레임 목록이 포함되어 있습니다. 청구 내 의 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| 클레임참조 Id | 필수 | 설명 |
| --------- | -------- | ----------- |
| identifier | yes | 이전에 코드를 생성한 사용자를 식별하는 식별자입니다. 일반적으로 이메일 주소 또는 전화 번호와 같이 코드가 전달되는 대상의 식별자로 사용됩니다. |
| otpToVerify | yes | 사용자가 제공한 확인 코드입니다. |

**InputClaims변환** 요소에는 입력 클레임을 수정하거나 일회성 암호 프로토콜 공급자로 보내기 전에 새 클레임을 생성하는 데 사용되는 **InputClaims변환** 요소의 컬렉션이 포함될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

이 프로토콜 공급자의 코드 확인 중에 제공된 출력 클레임이 없습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정을 사용하여 코드 확인 모드를 사용할 수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업(Operation) | yes | 수행할 작업입니다. 가능한 값: `VerifyCode`. |


### <a name="ui-elements"></a>UI 요소

다음 메타데이터를 사용하여 코드 확인 실패 시 표시되는 오류 메시지를 구성할 수 있습니다. 메타데이터는 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 구성되어야 합니다. 오류 메시지를 [지역화할](localization-string-ids.md#one-time-password-error-messages)수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 사용자 메시지IfSession존재하지 않음 | 예 | 코드 확인 세션이 만료된 경우 사용자에게 표시할 메시지입니다. 코드가 만료되었거나 지정된 식별자에 대해 코드가 생성된 적이 없습니다. |
| 사용자 메시지IfMax 다시시도 | 예 | 허용되는 최대 확인 시도를 초과한 경우 사용자에게 표시할 메시지입니다. |
| 사용자 메시지유효 코드 | 예 | 잘못된 코드를 제공한 경우 사용자에게 표시할 메시지입니다. |
|사용자 메시지IfSession충돌|예| 코드를 확인할 수 없는 경우 사용자에게 표시할 메시지입니다.|

### <a name="example"></a>예제

다음 예제는 `TechnicalProfile` 코드를 확인하는 데 사용됩니다.

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>다음 단계

사용자 지정 전자 메일 확인을 통해 일회성 암호 기술 프로필을 사용하는 경우와 같은 문서를 참조하십시오.

- [Azure Active Directory B2C에서 사용자 지정 전자 메일 확인](custom-email.md)

