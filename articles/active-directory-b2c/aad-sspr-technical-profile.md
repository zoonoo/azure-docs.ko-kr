---
title: 사용자 지정 정책의 Azure AD SSPR 기술 프로필
titleSuffix: Azure AD B2C
description: Azure AD B2C의 Azure AD SSPR 기술 프로필에 대 한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383600"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 Azure AD SSPR 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 SSPR (셀프 서비스 암호 재설정)에 대 한 전자 메일 주소를 확인 하는 기능을 제공 합니다. Azure AD SSPR 기술 프로필을 사용 하 여 코드를 생성 하 고 전자 메일 주소로 보낸 다음 코드를 확인 합니다. Azure AD SSPR 기술 프로필은 오류 메시지를 반환할 수도 있습니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용 하면 자체 어설션된 페이지에 오류 메시지가 표시 됩니다.

이 기술 프로필:

- 는 사용자와 상호 작용 하기 위한 인터페이스를 제공 하지 않습니다. 대신 사용자 인터페이스가 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 호출 되거나 [디스플레이 컨트롤이](display-controls.md) [유효성 검사 기술 프로필로](validation-technical-profile.md)호출 됩니다.
- 는 Azure AD SSPR 서비스를 사용 하 여 코드를 생성 하 고 전자 메일 주소로 보낸 다음 코드를 확인 합니다.  
- 확인 코드를 통해 전자 메일 주소의 유효성을 검사 합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제에서는 Azure AD SSPR 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>전자 메일 보내기

이 기술 프로필의 첫 번째 모드는 코드를 생성 하 고 보내는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 AZURE AD SSPR에 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 SSPR 기술 프로필에 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
| emailAddress | 예 | 전자 메일 주소를 소유 하는 사용자의 식별자입니다. `PartnerClaimType`입력 클레임의 속성을로 설정 해야 합니다 `emailAddress` . |


**InputClaimsTransformations** 요소는 AZURE AD SSPR 서비스로 보내기 전에 입력 클레임을 수정 하거나 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure AD SSPR 프로토콜 공급자는 **outputclaims**반환 되지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 특성을 설정 하는 동안에는 Azure AD SSPR 프로토콜 공급자에서 반환 하지 않은 클레임을 포함할 수 있습니다 `DefaultValue` .

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
| 연산 | 예 | **Sendcode**여야 합니다.  |

#### <a name="ui-elements"></a>UI 요소

다음 메타 데이터를 사용 하 여 SMS 오류를 보낼 때 표시 되는 오류 메시지를 구성할 수 있습니다. 메타 데이터는 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 구성 해야 합니다. 오류 메시지는 [지역화](localization-string-ids.md#azure-ad-sspr)될 수 있습니다.

| 특성 | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | 아니요 | 서버에 내부 오류가 발생 한 경우 사용자 오류 메시지입니다. |
| UserMessageIfThrottled| 아니요 | 요청이 제한 된 경우 사용자 오류 메시지입니다.|


### <a name="example-send-an-email"></a>예: 전자 메일 보내기

다음 예제에서는 전자 메일을 통해 코드를 보내는 데 사용 되는 Azure AD SSPR 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인 하는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 AZURE AD SSPR에 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 SSPR 기술 프로필에 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필요한 공간 | 설명 |
| --------- | -------- | ----------- | ----------- |
| emailAddress| 예 | 이전에 코드를 전송 하는 데 사용한 것과 동일한 전자 메일 주소입니다. 또한 전자 메일 확인 세션을 찾는 데 사용 됩니다. `PartnerClaimType`입력 클레임의 속성을로 설정 해야 합니다 `emailAddress` .|
| verificationCode  | 예 | 사용자가 확인 하기 위해 제공한 확인 코드입니다. `PartnerClaimType`입력 클레임의 속성을로 설정 해야 합니다 `verificationCode` . |

**InputClaimsTransformations** 요소는 AZURE AD SSPR 서비스를 호출 하기 전에 입력 클레임을 수정 하거나 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure AD SSPR 프로토콜 공급자는 **outputclaims**반환 되지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 특성을 설정 하는 동안에는 Azure AD SSPR 프로토콜 공급자에서 반환 하지 않은 클레임을 포함할 수 있습니다 `DefaultValue` .

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
| 연산 | 예 | **Verifycode** 여야 합니다. |

#### <a name="ui-elements"></a>UI 요소

다음 메타 데이터를 사용 하 여 코드 확인 실패 시 표시 되는 오류 메시지를 구성할 수 있습니다. 메타 데이터는 [자체 어설션된](self-asserted-technical-profile.md) 기술 프로필에서 구성 해야 합니다. 오류 메시지는 [지역화](localization-string-ids.md#azure-ad-sspr)될 수 있습니다.

| 특성 | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | 코드 확인 세션이 만료 된 경우 사용자에 게 표시할 메시지입니다. 코드가 만료 되었거나 지정 된 식별자에 대 한 코드가 생성 되지 않았습니다.|
|UserMessageIfInternalError | 서버에 내부 오류가 발생 한 경우 사용자 오류 메시지입니다.|
|UserMessageIfThrottled | 요청이 제한 된 경우 사용자 오류 메시지입니다.|
|UserMessageIfVerificationFailedNoRetry | 사용자가 잘못 된 코드를 제공 하 고 사용자가 올바른 코드를 제공할 수 없는 경우 사용자에 게 표시할 메시지입니다.|
|UserMessageIfVerificationFailedRetryAllowed | 사용자가 잘못 된 코드를 제공 하 고 사용자가 올바른 코드를 제공할 수 있는 경우 사용자에 게 표시할 메시지입니다.|

### <a name="example-verify-a-code"></a>예: 코드 확인

다음 예제에서는 코드를 확인 하는 데 사용 되는 Azure AD SSPR 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```