---
title: 디스플레이 제어 참조
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 컨트롤에 대한 참조입니다. 사용자 지정 정책에 정의된 사용자 여정을 사용자 지정하려면 표시 컨트롤을 사용합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188735"
---
# <a name="display-controls"></a>컨트롤 표시

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**디스플레이 컨트롤은** 특별한 기능을 가지고 있으며 Azure Active Directory B2C(Azure AD B2C) 백 엔드 서비스와 상호 작용하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 유효성 검사 [기술 프로필을](validation-technical-profile.md) 호출하는 페이지에서 작업을 수행할 수 있습니다. 디스플레이 컨트롤은 페이지에 표시되며 [자체 어설션된 기술 프로필에](self-asserted-technical-profile.md)의해 참조됩니다.

다음 이미지는 기본 및 보조 전자 메일 주소의 유효성을 검사하는 두 개의 디스플레이 컨트롤이 있는 자체 어설션된 등록 페이지를 보여 줍니다.

![렌더링된 디스플레이 컨트롤 예제](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

 [자체 어설션된 기술 프로필의](self-asserted-technical-profile.md) [메타데이터](self-asserted-technical-profile.md#metadata) 섹션에서 참조된 `DataUri` [ContentDefinition는](contentdefinitions.md) 페이지 계약 버전 2.0.0 이상으로 설정해야 합니다. 예를 들어:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>디스플레이 컨트롤 정의

**DisplayControl** 요소에는 다음 특성이 포함되어 있습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 디스플레이 컨트롤에 사용되는 식별자입니다. [참조할](#referencing-display-controls)수 있습니다. |
| 사용자 인터페이스 제어 유형 | yes | 디스플레이 컨트롤의 유형입니다. 현재 지원되는 [검증 제어](display-control-verification.md) |

**디스플레이 컨트롤** 요소에는 다음 요소가 포함되어 있습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **Input클레임은** 사용자로부터 수집할 클레임 값을 미리 채우는 데 사용됩니다. |
| 디스플레이 클레임 | 0:1 | **표시 클레임은** 사용자로부터 수집할 클레임을 나타내는 데 사용됩니다. |
| OutputClaims | 0:1 | **OutputClaims는** 이 **DisplayControl**에 대해 일시적으로 저장될 클레임을 나타내는 데 사용됩니다. |
| 동작 | 0:1 | **작업은** 프런트 엔드에서 발생하는 사용자 작업에 대해 호출할 유효성 검사 기술 프로필을 나열하는 데 사용됩니다. |

### <a name="input-claims"></a>입력 클레임

디스플레이 컨트롤에서 **InputClaims** 요소를 사용하여 페이지에서 사용자로부터 수집할 클레임 값을 미리 채울 수 있습니다. 모든 **입력클레임변환은** 이 디스플레이 컨트롤을 참조하는 자체 어설션기술 프로파일에서 정의할 수 있습니다.

다음 예제는 확인할 전자 메일 주소를 이미 있는 주소로 미리 채웁니다.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>클레임 표시

각 유형의 디스플레이 컨트롤에는 다른 디스플레이 클레임 집합, [출력 클레임](#output-claims)및 [수행할 작업이](#display-control-actions) 필요합니다.

[자체 주장기술 프로파일에](self-asserted-technical-profile.md#display-claims)정의된 **디스플레이 클레임과** 유사하게, 디스플레이 클레임은 디스플레이 제어 내에서 사용자로부터 수집되는 클레임을 나타낸다. 참조되는 **ClaimType** 요소는 `TextBox` Azure `DropdownSingleSelect`AD B2C에서 지원하는 사용자 입력 유형에 대한 **UserInputType** 요소를 지정해야 합니다. **작업에서**표시 클레임 값이 필요한 경우 **필수** 특성을 설정하여 `true` 사용자가 특정 표시 클레임에 대한 값을 제공하도록 합니다.

특정 유형의 디스플레이 제어에는 특정 디스플레이 클레임이 필요합니다. 예를 들어 **확인 코드는** 확인 **제어**형식의 표시 제어에 필요합니다. **ControlClaimType** 특성을 사용하여 필요한 클레임에 대해 지정된 표시 클레임을 지정합니다. 예를 들어:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>출력 클레임

디스플레이 컨트롤의 **출력 클레임은** 다음 오케스트레이션 단계로 전송되지 않습니다. 현재 디스플레이 제어 세션에 대해서만 일시적으로 저장됩니다. 이러한 임시 클레임은 동일한 디스플레이 컨트롤의 서로 다른 작업 간에 공유할 수 있습니다.

출력 클레임을 다음 오케스트레이션 단계로 버블링하려면 이 디스플레이 컨트롤을 참조하는 실제 자체 어설션기술 프로파일의 **OutputClaims를** 사용합니다.

### <a name="display-control-actions"></a>디스플레이 제어 작업

디스플레이 컨트롤의 **작업은** 사용자가 클라이언트 측(브라우저)에서 특정 작업을 수행할 때 Azure AD B2C 백 엔드에서 발생하는 프로시저입니다. 예를 들어 사용자가 페이지에서 단추를 선택할 때 수행할 유효성 검사입니다.

작업은 유효성 검사 기술 프로필 목록을 **정의합니다.** 디스플레이 컨트롤의 디스플레이 클레임의 일부 또는 전부를 검증하는 데 사용됩니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사하고 사용자에게 오류를 반환할 수 있습니다. 자체 설명된 기술 [프로필에서 유효성 검사 기술 프로필에](validation-technical-profile.md) 사용되는 방식과 유사한 디스플레이 제어 작업에서 **ContinueOnError,** **ContinueOnSuccess**및 **사전 조건을** 사용할 수 있습니다.

다음 예제에서는 **사용자가 선택한 mfaType** 클레임에 따라 전자 메일 또는 SMS로 코드를 보냅니다.

```XML
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

## <a name="referencing-display-controls"></a>디스플레이 컨트롤 참조

디스플레이 컨트롤은 [자체 어설션된 기술 프로파일의](self-asserted-technical-profile.md) [디스플레이 클레임에서](self-asserted-technical-profile.md#display-claims) 참조됩니다.

예를 들어:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
