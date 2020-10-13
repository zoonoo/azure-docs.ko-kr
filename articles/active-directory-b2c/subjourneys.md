---
title: Azure Active Directory B2C의 SubJourneys | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 SubJourneys 요소를 지정 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952449"
---
# <a name="subjourneys"></a>SubJourneys

Subjourneys를 사용 하 여 사용자 경험 내에서 오케스트레이션 단계를 구성 하 고 간소화할 수 있습니다. [사용자 경험](userjourneys.md)은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 이러한 경로를 통해 사용자를 가져와서 신뢰 당사자에게 표시할 클레임을 검색합니다. 즉, 사용자 경험은 Azure AD B2C ID 경험 프레임워크가 요청을 처리할 때 최종 사용자에게 적용되는 비즈니스 논리를 정의합니다. 사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 오케스트레이션 단계의 [Claim이상 변화](userjourneys.md#claimsexchanges) 요소는를 실행 하는 단일 [기술 프로필](technical-profiles-overview.md) 에 연결 됩니다.

Subjourney은 사용자 경험 내에서 언제 든 지 호출할 수 있는 오케스트레이션 단계를 그룹화 한 것입니다. Subjourneys를 사용 하 여 재사용 가능한 단계 시퀀스를 만들거나 분기를 구현 하 여 비즈니스 논리를 더 잘 나타낼 수 있습니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>사용자 경험 분기

Subjourneys는 [사용자 경험](userjourneys.md)처럼 동작 합니다 .이는 모두 성공적인 트랜잭션을 위해 따라야 하는 오케스트레이션 시퀀스로 표시 됩니다. 사용자 경험를 자체적으로 호출할 수 있으며, 실행 하려면 SendClaims 단계를 수행 해야 합니다. Subjourneys는 사용자 경험의 구성 요소 이며 독립적으로 호출할 수 없으며 항상 사용자 경험에서 호출 됩니다.

분기의 주요 구성 요소는 사용자 경험에서 더 나은 비즈니스 논리 처리를 허용 하는 것입니다. 일반적인 오케스트레이션 단계는 개별적으로 호출할 개별 요소로 그룹화 됩니다. 하위 작업을 통해 여러 오케스트레이션 단계가 결합 되는 (동일한 사전 조건이 있음) 경험을 단순화할 수 있습니다. Subjourney은 사용자 경험 에서만 호출 되며 다른 하위 경험을 호출 해서는 안 됩니다.

Subjourneys에는 다음과 같은 두 가지 유형이 있습니다.

- **Call** -호출자에 게 제어를 반환 합니다. SubJourney이 실행 된 다음 사용자 경험 내에서 현재 실행 중인 오케스트레이션 단계로 제어가 반환 됩니다.
- **전송** -제어를 subjourney (되돌릴 수 없는 분기)으로 전송 합니다. Subjourney에는 클레임을 신뢰 당사자 응용 프로그램으로 다시 반환 하는 SendClaims 단계가 있어야 합니다.

## <a name="example-scenarios"></a>예제 시나리오

### <a name="call-subjourney"></a>SubJourney 호출

호출 SubJourney은 다음과 같은 시나리오에서 유용 합니다.

- 나이 제어: 연령 게이팅의 경우 사용자 경험 간에 여러 공유 구성 요소가 있습니다. 분기를 사용 하면 공통 요소를 공유 가능한 구성 요소로 컴파일할 수 있습니다.  
- 보호자 승인: 분기를 사용 하면 사용자가 동의 해야 하는 사용자를 찾은 후 보호자 승인 사용자 경험으로 분기할 수 있는 것과 함께, 사소한 실행을 경험 하는 사용자의 클레임에 액세스 하도록 허용 하 여 자녀 보호를 편리 하 게 이용할 수 있습니다. 
- 로그인 하려면 등록: 디렉터리에 사용자가 이미 있지만 실제로 계정을 만든 것을 잊은 경우를 고려해 야 합니다. 사용자가 입력 한 자격 증명이 이미 있음을 사용자에 게 알리는 대신 사용자가 해당 사용자에 대 한 등록 흐름에서 등록 흐름으로 스위치를 수행할 수 있도록 사용자에 게 지시 하는 것이 좋을 수 있습니다.  

### <a name="transfer-subjourney"></a>서브 여행 전송

전송 하위 과정은 다음과 같은 시나리오에서 유용 합니다.

- 블록 페이지를 표시 합니다.
- 요청을 하위 과정으로 라우팅하여 토큰을 실행 및 발급 하는 a/B 테스트

## <a name="adding-a-subjourney-element"></a>SubJourney 요소 추가

다음은 `SubJourney` `Call` 컨트롤을 사용자 경험으로 다시 반환 하는 형식의 요소에 대 한 예입니다.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

다음은 `SubJourney` `Transfer` 토큰을 신뢰 당사자 응용 프로그램으로 다시 반환 하는 형식의 요소에 대 한 예입니다.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>Subjourney 단계 호출

유형의 새 오케스트레이션 단계를 `InvokeSubJourney` 사용 하 여 subjourney을 실행 합니다. 다음은이 오케스트레이션 단계의 모든 실행 요소를 보여 주는 예입니다.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Subjourney에서 다른 하위 경험을 호출 해서는 안 됩니다.

## <a name="components"></a>구성 요소

정책에서 지원 되는 subjourneys을 정의 하려면 정책 파일의 최상위 요소 아래에 **subjourneys** 요소를 추가 합니다.

**SubJourneys** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 전체 사용자 흐름에 필요한 모든 구문을 정의 하는 subjourney입니다. |

**SubJourneys** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 사용자 경험에서 정책의 SubJourney을 참조 하는 데 사용할 수 있는 SubJourney 식별자입니다. [후보](userjourneys.md#journeylist) 요소의 **SubJourneyReferenceId** 요소는이 특성을 가리킵니다. |
| 유형 | 예 | 가능한 값은 `Call` , 또는 `Transfer` 입니다. 자세한 내용은 [사용자 경험 분기](#user-journey-branching) 를 참조 하세요.|

**Subjourney** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스입니다. 모든 사용자 경험은 순서대로 실행되는 정렬된 오케스트레이션 단계 목록으로 구성됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

오케스트레이션 단계 요소의 전체 목록은 [UserJourneys](userjourneys.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[UserJourneys](userjourneys.md)