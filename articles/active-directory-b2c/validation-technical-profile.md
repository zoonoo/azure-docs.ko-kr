---
title: Azure Active Directory B2C의 사용자 지정 정책에 유효성 검사 기술 프로필 정의 | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 Azure Active Directory 기술 프로필을 정의합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511917"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C의 사용자 지정 정책에 유효성 검사 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

유효성 검사 기술 프로필은 [Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md)와 같은 모든 프로토콜의 일반적인 기술 프로필입니다. 유효성 검사 기술 프로필은 출력 클레임을 반환하거나 다음 데이터가 있는 HTTP 409 오류 메시지(응답 충돌 상태 코드 충돌)를 반환합니다.

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

유효성 검사 기술 프로필에서 반환되는 클레임은 클레임 모음에 다시 추가됩니다. 이러한 클레임은 다음 유효성 검사 기술 프로필에서 사용할 수 있습니다.

유효성 검사 기술 프로필은 **ValidationTechnicalProfiles** 요소에 표시되는 순서대로 실행됩니다. 유효성 검사 기술 프로필에서 오류가 발생하거나 성공하는 경우 후속 유효성 검사 기술 프로필을 계속 실행할지 여부를 유효성 검사 기술 프로필에 구성할 수 있습니다.

유효성 검사 기술 프로필은 **ValidationTechnicalProfile** 요소에 정의된 사전 조건에 따라 조건부로 실행할 수 있습니다. 예를 들어 특정 클레임이 있는지 또는 클레임이 지정된 값과 같은지 여부를 확인할 수 있습니다.

자체 어설션된 기술 프로필은 출력 클레임의 일부 또는 전부를 검증하는 데 사용되는 유효성 검사 기술 프로필을 정의할 수 있습니다. 참조된 기술 프로필의 모든 입력 클레임은 참조하는 유효성 검사 기술 프로필의 출력 클레임에 나타나야 합니다.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 참조하는 기술 프로필의 출력 클레임 일부 또는 전부의 유효성을 검사하는 데 사용되는 기술 프로필입니다. |

**ValidationTechnicalProfile** 요소에 포함되는 특성은 다음과 같습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 또는 부모 정책에 이미 정의된 기술 프로필의 식별자입니다. |
|ContinueOnError|아닙니다.| 이 유효성 검사 기술 프로필에서 오류가 발생 하는 경우 모든 후속 유효성 검사 기술 프로필의 유효성을 검사 계속 해야 하는지 여부를 나타내는입니다. 가능한 값: `true` 또는 `false`(기본값, 추가 유효성 프로필의 처리가 중지되고 오류가 반환됨) |
|ContinueOnSuccess | 아닙니다. | 이 유효성 검사 기술 프로필이 성공하는 경우 후속 유효성 검사 프로필의 유효성을 계속 검사할지 여부를 나타냅니다. 가능한 값은 `true` 또는 `false`입니다. 기본값은 `true`입니다. 즉 추가 유효성 검사 프로필의 처리가 계속됩니다. |

**ValidationTechnicalProfile** 요소에 에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 유효성 검사 기술 프로필을 실행하기 위해 충족해야 하는 사전 조건의 목록입니다. |

**Precondition** 요소에 포함되는 특성은 다음과 같습니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | 예 | 사전 조건에 대해 수행할 검사 또는 쿼리의 유형입니다. 지정된 클레임이 사용자의 현재 클레임 집합에 있는 경우 작업을 수행하도록 보장하기 위해 `ClaimsExist`가 지정되거나, 지정된 클레임이 있고 해당 값이 지정된 값과 같은 경우 작업을 수행하도록 보장하기 위해 `ClaimEquals`가 지정됩니다. |
| `ExecuteActionsIf` | 예 | 테스트가 true 또는 false인 경우 사전 조건의 작업을 수행해야 하는지 여부를 나타냅니다. |

**Precondition** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 값 | 1:n | 검사에 사용되는 데이터입니다. 이 검사 유형이 `ClaimsExist`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 검사 유형이 `ClaimEquals`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 한편 다른 값 요소에는 검사할 값이 포함됩니다.|
| 액션(Action) | 1:1 | 오케스트레이션 단계 내의 사전 조건 검사가 true인 경우 수행해야 하는 작업입니다. **Action**의 값은 `SkipThisValidationTechnicalProfile`로 설정됩니다. 연결된 유효성 검사 기술 프로필을 실행하지 않도록 지정합니다. |

### <a name="example"></a>예

다음과 같은 유효성 검사 기술 프로필을 사용하는 예제입니다.

1. 사용자 자격 증명을 확인하고, 잘못된 사용자 이름 또는 올바르지 않은 암호와 같은 오류가 발생하면 첫 번째 유효성 검사 기술 프로필이 계속되지 않습니다.
2. userType 클레임이 없거나 userType 값이 `Partner`이면 다음 유효성 검사 기술 프로필이 실행되지 않습니다. 유효성 검사 기술 프로필은 내부 고객 데이터베이스에서 사용자 프로필을 읽으려고 시도하고, 사용할 수 없는 REST API 서비스 또는 내부 오류와 같은 오류가 발생하더라도 계속됩니다.
3. userType 클레임이 없거나 userType 값이 `Customer`이면 마지막 유효성 검사 기술 프로필이 실행되지 않습니다. 유효성 검사 기술 프로필은 내부 파트너 데이터베이스에서 사용자 프로필을 읽으려고 시도하고, 사용할 수 없는 REST API 서비스 또는 내부 오류와 같은 오류가 발생하더라도 계속됩니다.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
