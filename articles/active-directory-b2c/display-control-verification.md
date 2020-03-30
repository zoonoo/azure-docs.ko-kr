---
title: 디스플레이 컨트롤을 통해 클레임 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 컨트롤을 사용하여 사용자 지정 정책에서 제공하는 사용자 이동에서 클레임을 확인하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188786"
---
# <a name="verification-display-control"></a>검증 디스플레이 제어

확인 [표시 컨트롤을](display-controls.md) 사용하여 사용자에게 전송된 확인 코드를 사용하여 이메일 주소 또는 전화 번호와 같은 클레임을 확인합니다.

## <a name="verificationcontrol-actions"></a>확인제어 작업

확인 표시 컨트롤은 두 단계(작업)로 구성됩니다.

1. 확인 코드를 보내야 하는 전자 메일 주소 또는 전화 번호와 같은 사용자에게 목적지를 요청합니다. 사용자가 **코드 보내기** 단추를 선택하면 확인 표시 컨트롤의 **SendCode 작업이** 실행됩니다. **SendCode 작업은** 코드를 생성하고 보낼 콘텐츠를 생성하고 사용자에게 보냅니다. 주소 값을 미리 채울 수 있으며 2단계 인증역할을 할 수 있습니다.

    ![코드 보내기 작업에 대한 예제 페이지](media/display-control-verification/display-control-verification-email-action-01.png)

1. 코드가 전송된 후 사용자는 메시지를 읽고, 표시 컨트롤에서 제공하는 컨트롤에 확인 코드를 입력하고, **코드 확인을**선택합니다. 코드 **확인을**선택하면 **확인 코드 작업이** 실행되어 주소와 연결된 코드를 확인합니다. 사용자가 **새 코드 보내기를**선택하면 첫 번째 작업이 다시 실행됩니다.

    ![코드 작업 확인을 위한 예제 페이지](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>확인제어 필수 요소 제어

**검증 제어에는** 다음 요소가 포함되어야 합니다.

- 의 `DisplayControl` 유형은 `VerificationControl`.
- `DisplayClaims`
  - **보내기** - 확인 코드를 보낼 위치를 지정하는 하나 이상의 클레임입니다. 예를 들어 *이메일* 또는 *국가 코드* 및 전화 *번호.*
  - **확인 코드** - 코드를 보낸 후 사용자가 제공하는 확인 코드 주장입니다. 이 클레임은 필요에 따라 설정해야 하며 `VerificationCode`을 로 `ControlClaimType` 설정해야 합니다.
- 사용자가 확인 프로세스를 완료한 후 자체 어설션된 페이지로 반환되는 출력 클레임(선택 사항)입니다. 예를 들어 *이메일* 또는 *국가 코드* 및 전화 *번호.* 자체 어설션된 기술 프로파일은 클레임을 사용하여 데이터를 유지하거나 출력 클레임을 다음 오케스트레이션 단계로 버블링합니다.
- 다음 `Action`이름을 가진 두 개의 s:
  - **SendCode** - 사용자에게 코드를 보냅니다. 이 작업에는 일반적으로 코드를 생성하고 전송하기 위한 두 개의 유효성 검사 기술 프로필이 포함됩니다.
  - **VerifyCode** - 코드를 확인합니다. 이 작업에는 일반적으로 단일 유효성 검사 기술 프로필이 포함됩니다.

아래 예제에서는 **페이지에 전자 메일** 텍스트 상자가 표시됩니다. 사용자가 전자 메일 주소를 입력하고 **SendCode를**선택하면 Azure AD B2C 백 엔드에서 **SendCode** 작업이 트리거됩니다.

그런 다음 사용자는 **확인 코드를** 입력하고 백 엔드에서 VerifyCode 작업을 트리거하도록 **VerifyCode를** 선택합니다. **VerifyCode** 모든 유효성 검사가 통과하면 **검증 제어가** 완료된 것으로 간주되고 사용자는 다음 단계로 계속 진행할 수 있습니다.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
