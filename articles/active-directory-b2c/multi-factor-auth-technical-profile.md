---
title: 사용자 지정 정책의 Azure MFA 기술 프로필
titleSuffix: Azure AD B2C
description: Azure AD B2C의 MFA(Azure 다단계 인증) 기술 프로필에 대한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332814"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 Azure MFA 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 MFA(Azure 다단계 인증)를 사용하여 전화 번호 확인을 지원합니다. 이 기술 프로필을 사용하여 전화 번호로 코드를 생성하고 전송한 다음 코드를 확인합니다. Azure MFA 기술 프로필은 오류 메시지를 반환할 수도 있습니다.  유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 자체 어설션된 페이지에 오류 메시지가 표시됩니다.

이 기술 프로필:

- 사용자와 상호 작용하는 인터페이스를 제공하지 않습니다. 대신 사용자 인터페이스는 자체 [어설션된](self-asserted-technical-profile.md) 기술 프로필 또는 [유효성 검사 기술 프로파일로](validation-technical-profile.md)디스플레이 [컨트롤에서](display-controls.md) 호출됩니다.
- Azure MFA 서비스를 사용하여 전화 번호로 코드를 생성하고 전송한 다음 코드를 확인합니다.  
- 문자 메시지를 통해 전화 번호의 유효성을 검사합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **처리기** 특성에는 Azure AD B2C에서 사용하는 프로토콜 처리기 어셈블리의 정규화된 이름이 포함되어야 합니다.

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제에서는 Azure MFA 기술 프로필을 보여 주며 있습니다.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS 보내기

이 기술 프로필의 첫 번째 모드는 코드를 생성하고 보내는 것입니다. 이 모드에 대해 다음 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소에는 Azure MFA에 보낼 클레임 목록이 포함되어 있습니다. 클레임 이름을 MFA 기술 프로필에 정의된 이름에 매핑할 수도 있습니다.

| 클레임참조 Id | 필수 | 설명 |
| --------- | -------- | ----------- |
| userPrincipalName | yes | 전화 번호를 소유한 사용자의 식별자입니다. |
| phoneNumber | yes | SMS 코드를 보낼 전화 번호입니다. |
| companyName | 예 |SMS의 회사 이름입니다. 제공되지 않으면 응용 프로그램의 이름이 사용됩니다. |
| locale | 예 | SMS의 로캘입니다. 제공되지 않으면 사용자의 브라우저 로캘이 사용됩니다. |

**InputClaims변환** 요소에는 입력 클레임을 수정하거나 Azure MFA 서비스로 보내기 전에 새 클레임을 생성하는 데 사용되는 **InputClaims변환** 요소의 컬렉션이 포함될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure MFA 프로토콜 공급자는 **OutputClaims를**반환하지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 특성을 설정하는 한 Azure MFA ID 공급자가 반환하지 않는 클레임을 포함할 `DefaultValue` 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업(Operation) | yes | **일조방향 SMS여야**합니다.  |

#### <a name="ui-elements"></a>UI 요소

다음 메타데이터를 사용하여 SMS 오류를 보낼 때 표시되는 오류 메시지를 구성할 수 있습니다. 메타데이터는 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 구성되어야 합니다. 오류 메시지를 [지역화할](localization-string-ids.md#azure-mfa-error-messages)수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 사용자 메시지인 | 예 | 제공된 전화 번호가 SMS를 수락하지 않는 경우 사용자 오류 메시지입니다. |
| 사용자 메시지유효하지 않은 형식 | 예 | 제공된 전화 번호가 유효한 전화 번호가 아닌 경우 사용자 오류 메시지입니다. |
| 사용자 메시지IfServer오류 | 예 | 서버에 내부 오류가 발생한 경우 사용자 오류 메시지입니다. |
| 사용자 메시지| 예 | 요청이 제한된 경우 사용자 오류 메시지입니다.|

### <a name="example-send-an-sms"></a>예: SMS 보내기

다음 예제에서는 SMS를 통해 코드를 보내는 데 사용되는 Azure MFA 기술 프로필을 보여 주습니다.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인하는 것입니다. 이 모드에 대해 다음 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소에는 Azure MFA에 보낼 클레임 목록이 포함되어 있습니다. 클레임 이름을 MFA 기술 프로필에 정의된 이름에 매핑할 수도 있습니다.

| 클레임참조 Id | 필수 | 설명 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| yes | 이전에 코드를 보내는 데 사용된 전화 번호와 동일합니다. 또한 전화 확인 세션을 찾는 데 사용됩니다. |
| 확인 코드  | yes | 사용자가 제공한 확인 코드 |

**InputClaims변환** 요소에는 입력 클레임을 수정하거나 Azure MFA 서비스를 호출하기 전에 새 클레임을 생성하는 데 사용되는 **InputClaims변환** 요소의 컬렉션이 포함될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure MFA 프로토콜 공급자는 **OutputClaims를**반환하지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 특성을 설정하는 한 Azure MFA ID 공급자가 반환하지 않는 클레임을 포함할 `DefaultValue` 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업(Operation) | yes | 반드시 **확인해야 합니다.** |

#### <a name="ui-elements"></a>UI 요소

다음 메타데이터를 사용하여 코드 확인 실패 시 표시되는 오류 메시지를 구성할 수 있습니다. 메타데이터는 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 구성되어야 합니다. 오류 메시지를 [지역화할](localization-string-ids.md#azure-mfa-error-messages)수 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| 사용자 메시지IfMax허용코드재트리에 도달| 예 | 사용자가 확인 코드를 너무 많이 시도한 경우 사용자 오류 메시지입니다. |
| 사용자 메시지IfServer오류 | 예 | 서버에 내부 오류가 발생한 경우 사용자 오류 메시지입니다. |
| 사용자 메시지| 예 | 요청이 제한되는 경우 사용자 오류 메시지입니다.|
| 사용자 메시지잘못된 코드입력| 예| 확인을 위해 입력한 코드가 잘못된 경우 사용자 오류 메시지입니다.|

### <a name="example-verify-a-code"></a>예: 코드 확인

다음 예제에서는 코드를 확인하는 데 사용되는 Azure MFA 기술 프로필을 보여 주습니다.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
