---
title: 보상 점수 - Personalizer
description: 보상 점수는 사용자에 대한 맞춤 설정 선택 항목인 RewardActionID가 효율적으로 수행된 정도를 나타냅니다. 보상 점수의 값은 사용자 동작 관찰 기반의 비즈니스 논리에 따라 결정됩니다. Personalizer는 보상을 평가하여 기계 학습 모델을 학습시킵니다.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221216"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>보상 점수는 맞춤 설정의 성공을 나타냅니다.

보상 점수는 사용자에 대한 맞춤 설정 선택 항목인 [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)가 효율적으로 수행된 정도를 나타냅니다. 보상 점수의 값은 사용자 동작 관찰 기반의 비즈니스 논리에 따라 결정됩니다.

Personalizer는 보상을 평가하여 기계 학습 모델을 학습시킵니다.

Personalizer 리소스에 대 한 Azure Portal에서 기본 보상 점수를 구성 하 [는 방법](how-to-settings.md#configure-rewards-for-the-feedback-loop) 에 대해 알아봅니다.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>보상 API를 사용하여 Personalizer에 보상 점수 보내기

보상은 [보상 API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward)를 통해 Personalizer에 보내집니다. 일반적으로 보상은 0에서 1 사이의 숫자입니다. 값이-1 인 부정 보상은 특정 시나리오에서 가능 하며, RL (보충 learning)에 익숙한 경우에만 사용 해야 합니다. Personalizer는 시간이 지남에 따라 가능한 가장 높은 총 보상을 달성할 수 있도록 모델을 학습시킵니다.

나중에 며칠이 될 수 있는 사용자 동작이 수행되면 보상을 보냅니다. 이벤트에 보상이 없거나 기본 보상이 있는 것으로 간주될 때까지 Personalizer에서 기다리는 최대 시간은 Azure Portal에서 [보상 대기 시간](#reward-wait-time)으로 구성됩니다.

이벤트에 대한 보상 점수가 **보상 대기 시간** 내에 수신되지 않으면 **기본 보상**이 적용됩니다. 일반적으로 **[기본 보상](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** 은 0으로 구성됩니다.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>보상을 위해 고려할 동작 및 데이터

보상 점수 컨텍스트에 대해 다음 신호 및 동작을 고려합니다.

* 옵션이 관련된 경우 제안에 대한 직접 사용자 입력("X를 의미하나요?")
* 세션 길이
* 세션 간 시간
* 사용자 조작에 대한 감정 분석
* 봇이 사용자에게 유용성, 정확도에 대한 피드백을 요청하는 미니 설문 및 직접 질문
* 경고에 대한 응답 또는 경고에 대한 응답 지연

## <a name="composing-reward-scores"></a>보상 점수 작성

보상 점수는 비즈니스 논리에서 계산해야 합니다. 점수는 다음과 같이 나타낼 수 있습니다.

* 한 번 보낸 단일 숫자
* 즉시 보낸 점수(예: 0.8) 및 나중에 보낸 추가 점수(일반적으로 0.2)

## <a name="default-rewards"></a>기본 보상

Rank(순위) 호출 이후의 기간인 [보상 대기 시간](#reward-wait-time) 내에 보상이 수신되지 않으면 Personalizer에서 **기본 보상**을 해당 Rank 이벤트에 암시적으로 적용합니다.

## <a name="building-up-rewards-with-multiple-factors"></a>여러 요소를 사용하여 보상 작성

효과적인 개인 설정의 경우 여러 요소에 따라 보상 점수를 구축할 수 있습니다.

예를 들어 다음 규칙을 적용하여 비디오 콘텐츠 목록을 개인에 맞게 설정할 수 있습니다.

|사용자 동작|부분 점수 값|
|--|--|
|사용자가 상위 항목을 클릭했습니다.|+0.5 보상|
|사용자가 해당 항목의 실제 내용을 열었습니다.|+0.3 보상|
|사용자가 5분 또는 30% 중 더 오래 걸리는 콘텐츠를 감시했습니다.|+0.2 보상|
|||

그런 다음, 총 보상을 API에 보낼 수 있습니다.

## <a name="calling-the-reward-api-multiple-times"></a>보상 API를 여러 번 호출

동일한 이벤트 ID를 사용하여 보상 API를 호출하여 다른 보상 점수를 보낼 수도 있습니다. Personalizer가 이러한 보상을 가져오는 경우 Personalizer 구성에 지정 된 대로 집계 하 여 해당 이벤트에 대 한 최종 보상을 결정 합니다.

집계 값:

*  **첫 번째**: 이벤트에 대해 받은 첫 번째 보상 점수를 받고 나머지를 삭제 합니다.
* **합계**: eventId에 대해 수집 된 모든 보상 점수를 사용 하 여 함께 추가 합니다.

**보상 대기 시간** 이후에 받은 이벤트에 대한 모든 보상을 버리고 모델 학습에 영향을 주지 않습니다.

보상 점수를 추가 하 여 최종 보상은 예상 점수 범위를 벗어날 수 있습니다. 이 경우 서비스는 실패하지 않습니다.

## <a name="best-practices-for-calculating-reward-score"></a>보상 점수 계산에 대한 모범 사례

* **성공적인 개인 설정에 대 한 실제 표시기를 고려해 보세요**. 클릭 측면에서 생각 하기 쉽지만 사용자가 *수행*하려는 작업 대신 사용자가 *달성할* 수 있도록 하려는 작업을 기반으로 하는 것이 좋습니다.  예를 들어 클릭 수에 보상하는 경우 클릭베이트(clickbait) 경향이 있는 콘텐츠를 선택할 수 있습니다.

* **개인 설정의 작동 방식에 대 한 보상 점수를 사용**합니다. 영화 제안 개인 설정은 사용자가 동영상을 시청 하 고 높은 등급을 제공 하는 것이 좋습니다. 영화 등급은 아마도 많은 요소(연기의 품질, 사용자의 분위기)에 달려 있으므로 *맞춤 설정* 작동의 효율성에 대한 좋은 보상 신호는 아닙니다. 그러나 영화의 처음 몇 분을 보는 사용자는 맞춤 설정 효율성에 대한 더 좋은 신호일 수 있고, 5분 후에 1의 보상을 보내면 더 좋은 신호가 될 것입니다.

* **보상은 RewardActionID에만 적용**됩니다. Personalizer은 RewardActionID에 지정 된 작업의 효율성를 이해 하기 위해 보상을 적용 합니다. 다른 작업을 표시하도록 선택하고 사용자가 해당 작업을 클릭하면 보상이 0이어야 합니다.

* **의도**하지 않은 결과를 고려 합니다. 윤리를 사용 하 여 책임이 있는 결과를 생성 하 [고 담당](ethics-responsible-use.md)하는 보상 함수를 만듭니다.

* **증분 보상 사용**: 더 작은 사용자 동작에 대 한 부분 보상을 추가 하면 더 나은 보상을 Personalizer 수 있습니다. 이 증분 보상을 사용하면 알고리즘에서 최종적으로 원하는 동작에 사용자를 참여시키는 데 더 가까워지고 있음을 확인할 수 있습니다.
    * 영화 목록을 표시하는 경우 사용자가 잠시 동안 자세한 정보를 보기 위해 마우스로 첫 번째 항목 위를 가리키면 일부 사용자가 참여했는지 확인할 수 있습니다. 이 동작은 0.1의 보상 점수로 계산될 수 있습니다.
    * 사용자가 페이지를 연 다음, 종료하는 경우 보상 점수는 0.2가 될 수 있습니다.

## <a name="reward-wait-time"></a>보상 대기 시간

Personalizer는 Rank 호출의 정보와 모델 학습을 위한 Reward 호출에서 보낸 보상을 상호 연결시킵니다. 이러한 작업은 다른 시간에 수행될 수 있습니다. Rank 호출이 비활성 이벤트로 수행되고 나중에 활성화된 경우에도 Personalizer는 Rank 호출이 수행되면 시작하는 제한 시간 동안 기다립니다.

**보상 대기 시간**이 만료되고 보상 정보가 없는 경우 학습을 위해 기본 보상이 해당 이벤트에 적용됩니다. 최대 대기 기간은 6일입니다.

## <a name="best-practices-for-reward-wait-time"></a>보상 대기 시간에 대 한 모범 사례

더 나은 결과를 얻으려면 다음 추천 사항을 따릅니다.

* 보상 대기 시간을 최대한 짧게 설정하는 한편, 사용자 피드백을 받을 수 있는 충분한 시간을 남겨 둡니다.

* 피드백을 받는 데 필요한 시간보다 짧은 기간을 선택하지 않습니다. 예를 들어 사용자가 비디오를 1분 동안 시청한 후에 보상 중 일부가 들어오는 경우 실험 길이는 두 배 이상이어야 합니다.

## <a name="next-steps"></a>다음 단계

* [보충 학습](concepts-reinforcement-learning.md)
* [순위 API 사용해 보기](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [보상 API 사용해 보기](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
