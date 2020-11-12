---
title: 컨트롤 참조 표시
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 컨트롤에 대 한 참조입니다. 표시 컨트롤을 사용 하 여 사용자 지정 정책에 정의 된 사용자 경험 사용자 지정 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 950c159ed4d2c57796f33b9505e6931dbec983ba
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532378"
---
# <a name="display-controls"></a>표시 컨트롤

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**표시 컨트롤** 은 특수 기능이 있고 Azure Active Directory B2C (Azure AD B2C) 백 엔드 서비스와 상호 작용 하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 [유효성 검사 기술 프로필](validation-technical-profile.md) 을 호출 하는 페이지에서 작업을 수행할 수 있습니다. 표시 컨트롤은 페이지에 표시 되며 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에서 참조 됩니다.

다음 이미지는 기본 및 보조 전자 메일 주소의 유효성을 검사 하는 두 개의 표시 컨트롤이 있는 자체 어설션된 등록 페이지를 보여 줍니다.

![렌더링 된 디스플레이 컨트롤 예제](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>필수 구성 요소

 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [메타 데이터](self-asserted-technical-profile.md#metadata) 섹션에서 참조 된 [contentdefinition](contentdefinitions.md) 은 `DataUri` 페이지 계약 버전 2.0.0 이상으로 설정 해야 합니다. 다음은 그 예입니다. 

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>표시 컨트롤 정의

**DisplayControl** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| Id | 예 | 표시 컨트롤에 사용 되는 식별자입니다. [참조할](#referencing-display-controls)수 있습니다. |
| UserInterfaceControlType | 예 | 표시 컨트롤의 형식입니다. 현재 지원 되는 [VerificationControl](display-control-verification.md) |

**DisplayControl** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **Inputclaims** 은 사용자 로부터 수집할 클레임 값을 미리 채우는 데 사용 됩니다. 자세한 내용은 [Inputclaims](technicalprofiles.md#inputclaims) 요소를 참조 하세요. |
| DisplayClaims | 0:1 | **DisplayClaims** 는 사용자 로부터 수집할 클레임을 나타내는 데 사용 됩니다. 자세한 내용은 [DisplayClaim](technicalprofiles.md#displayclaim) 요소를 참조 하세요.|
| OutputClaims | 0:1 | **Outputclaims** 은이 **DisplayControl** 에 대해 일시적으로 저장 되는 클레임을 나타내는 데 사용 됩니다. 자세한 내용은 [Outputclaims](technicalprofiles.md#outputclaims) 요소를 참조 하세요.|
| 동작 | 0:1 | **작업** 은 프런트 엔드에서 발생 하는 사용자 작업에 대해 호출할 유효성 검사 기술 프로필을 나열 하는 데 사용 됩니다. |

### <a name="input-claims"></a>입력 클레임

표시 컨트롤에서 **inputclaims** 요소를 사용 하 여 페이지의 사용자 로부터 수집할 클레임 값을 미리 채울 수 있습니다. 이 표시 컨트롤을 참조 하는 자체 어설션된 기술 프로필에 모든 **InputClaimsTransformations** 을 정의할 수 있습니다.

다음 예에서는 주소가 이미 있는 것으로 확인 되는 전자 메일 주소를 인시던트의 합니다.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>클레임 표시

표시 컨트롤의 각 형식에는 다른 표시 클레임, [출력 클레임](#output-claims)및 수행할 [작업](#display-control-actions) 집합이 필요 합니다.

[자체 어설션된 기술 프로필](self-asserted-technical-profile.md#display-claims)에 정의 된 **표시 클레임** 과 마찬가지로 표시 클레임은 표시 컨트롤 내에서 사용자 로부터 수집할 클레임을 나타냅니다. 참조 된 **ClaimType** 요소는 또는와 같은 Azure AD B2C에서 지 원하는 사용자 입력 형식에 대해 **userinputtype** 요소를 지정 해야 합니다 `TextBox` `DropdownSingleSelect` . **작업** 에 표시 클레임 값이 필요한 경우에는 **필수** 특성을로 설정 하 여 `true` 사용자가 특정 표시 클레임에 대 한 값을 제공 하도록 합니다.

특정 유형의 표시 컨트롤에 대 한 특정 표시 클레임이 필요 합니다. 예를 들어 **VerificationControl** 형식의 표시 컨트롤에는 **VerificationCode** 가 필요 합니다. 특성 **Controlclaimtype** 을 사용 하 여 해당 필수 클레임에 지정 된 DisplayClaim를 지정 합니다. 다음은 그 예입니다. 

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>출력 클레임

표시 컨트롤의 **출력 클레임** 은 다음 오케스트레이션 단계로 보내지지 않습니다. 현재 표시 컨트롤 세션에 대해서만 일시적으로 저장 됩니다. 이러한 임시 클레임은 동일한 디스플레이 컨트롤의 서로 다른 작업 간에 공유할 수 있습니다.

출력 클레임을 다음 오케스트레이션 단계로 버블링 하려면이 표시 컨트롤을 참조 하는 실제 자체 어설션된 기술 프로필의 **Outputclaims** 을 사용 합니다.

### <a name="display-control-actions"></a>컨트롤 동작 표시

표시 컨트롤의 **작업** 은 사용자가 클라이언트 쪽 (브라우저)에서 특정 작업을 수행할 때 Azure AD B2C 백 엔드에서 발생 하는 프로시저입니다. 예를 들어 사용자가 페이지에서 단추를 선택할 때 수행할 유효성 검사입니다.

작업은 **유효성 검사 기술 프로필** 의 목록을 정의 합니다. 표시 컨트롤의 일부 또는 모든 표시 클레임의 유효성을 검사 하는 데 사용 됩니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사 하 고 사용자에 게 오류를 반환할 수 있습니다. 자체 어설션된 기술 프로필의 [유효성 검사 기술 프로필](validation-technical-profile.md) 에서 사용 되는 것과 유사한 컨트롤 표시 작업에서 **ContinueOnError** , **ContinueOnSuccess** 및 **사전 조건을** 사용할 수 있습니다.

#### <a name="actions"></a>동작

**Actions** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 작업 | 1:n | 실행할 작업의 목록입니다. |

#### <a name="action"></a>작업

**Action** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| Id | 예 | 작업의 유형입니다. 가능한 값은 `SendCode` 또는 `VerifyCode`입니다. `SendCode`값은 사용자에 게 코드를 보냅니다. 이 작업에는 두 가지 유효성 검사 기술 프로필이 포함 될 수 있습니다. 하나는 코드를 생성 하 고 다른 하나는 메시지를 전송 합니다. `VerifyCode`값은 사용자가 입력 텍스트 상자에 입력 한 코드를 확인 합니다. |

**Action** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| Validationclaim이상 변경 | 1:1 | 참조 기술 프로필의 일부 또는 전체 표시 클레임의 유효성을 검사 하는 데 사용 되는 기술 프로필의 식별자입니다. 참조 된 기술 프로필의 모든 입력 클레임은 참조 기술 프로필의 표시 클레임에 표시 되어야 합니다. |

#### <a name="validationclaimsexchange"></a>Validationclaim이상 변경

**Validationclaim이상 change** 요소에는 다음 요소가 포함 되어 있습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 참조 기술 프로필의 일부 또는 전체 표시 클레임의 유효성을 검사 하는 데 사용할 기술 프로필입니다. |

**ValidationTechnicalProfile** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 또는 부모 정책에 이미 정의된 기술 프로필의 식별자입니다. |
|ContinueOnError|아니요| 이 유효성 검사 기술 프로필에서 오류가 발생 하는 경우 후속 유효성 검사 기술 프로필의 유효성 검사를 계속할지 여부를 나타냅니다. 가능한 값: `true` 또는 `false` (기본값, 추가 유효성 검사 프로필 처리가 중지 되 고 오류가 반환 됨) |
|ContinueOnSuccess | 아니요 | 이 유효성 검사 기술 프로필이 성공할 경우 후속 유효성 검사 프로필의 유효성 검사를 계속할지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. 기본값은 `true`입니다. 즉 추가 유효성 검사 프로필의 처리가 계속됩니다. |

**ValidationTechnicalProfile** 요소에 에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 유효성 검사 기술 프로필을 실행하기 위해 충족해야 하는 사전 조건의 목록입니다. |

**사전 조건** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | 예 | 사전 조건에 대해 수행할 검사 또는 쿼리의 유형입니다. 가능한 값은 `ClaimsExist` 또는 `ClaimEquals`입니다. `ClaimsExist` 지정 된 클레임이 사용자의 현재 클레임 집합에 있는 경우 작업을 수행 하도록 지정 합니다. `ClaimEquals` 지정 된 클레임이 있고 해당 값이 지정 된 값과 동일한 경우 작업을 수행 하도록 지정 합니다. |
| `ExecuteActionsIf` | 예 | 테스트가 true 또는 false인 경우 사전 조건의 작업을 수행해야 하는지 여부를 나타냅니다. |

**Precondition** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 값 | 1:n | 검사에 사용되는 데이터입니다. 이 검사 유형이 `ClaimsExist`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 검사 유형이 `ClaimEquals`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 다른 값 요소에서 검사할 값을 지정 합니다.|
| 작업 | 1:1 | 오케스트레이션 단계 내의 사전 조건 검사가 true인 경우 수행해야 하는 작업입니다. **작업** 의 값은 `SkipThisValidationTechnicalProfile` 연결 된 유효성 검사 기술 프로필을 실행 하지 않도록 지정 하는로 설정 됩니다. |

다음 예제에서는 [AZURE AD SSPR 기술 프로필](aad-sspr-technical-profile.md)을 사용 하 여 전자 메일 주소를 보내고 확인 합니다.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

다음 예제에서는 사전 조건이 있는 **mfaType** 클레임의 사용자 선택에 따라 전자 메일 또는 SMS에서 코드를 보냅니다.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>표시 컨트롤 참조

표시 컨트롤은 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [표시 클레임](self-asserted-technical-profile.md#display-claims) 에서 참조 됩니다.

예를 들면 다음과 같습니다.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>다음 단계

표시 컨트롤 사용에 대 한 샘플은 다음을 참조 하세요. 

- [Mailjet를 사용 하 여 사용자 지정 전자 메일 확인](custom-email-mailjet.md)
- [SendGrid를 사용 하 여 사용자 지정 전자 메일 확인](custom-email-sendgrid.md)
