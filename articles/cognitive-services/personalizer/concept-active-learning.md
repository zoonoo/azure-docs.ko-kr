---
title: 학습 정책 - 개인화
description: 학습 설정은 모델 학습의 *하이퍼매개 변수를 결정합니다.* 서로 다른 학습 설정에서 학습된 동일한 데이터의 두 모델은 서로 다릅니다.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221212"
---
# <a name="learning-policy-and-settings"></a>학습 정책 및 설정

학습 설정은 모델 학습의 *하이퍼매개 변수를 결정합니다.* 서로 다른 학습 설정에서 학습된 동일한 데이터의 두 모델은 서로 다릅니다.

[학습 정책 및 설정은](how-to-settings.md#configure-rewards-for-the-feedback-loop) Azure 포털의 개인 설정리소스에 설정됩니다.

## <a name="import-and-export-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure 포털에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이 메서드를 사용하여 기존 정책을 저장하고, 테스트하고, 바꾸고, 소스 코드 컨트롤에 보관하여 나중에 참조 및 감사를 위한 아티팩트로 보관할 수 있습니다.

개인 설정 [리소스에](how-to-manage-model.md#import-a-new-learning-policy) 대한 Azure 포털에서 학습 정책을 가져오고 내보내는 방법을 알아봅니다.

## <a name="understand-learning-policy-settings"></a>학습 정책 설정 이해

학습 정책의 설정은 변경할 수 없습니다. 설정이 개인 설정에 미치는 영향을 이해하는 경우에만 설정을 변경합니다. 이 지식이 없으면 개인 설정 모델 무효화를 포함하여 문제가 발생할 수 있습니다.

개인화는 이벤트를 훈련하고 점수를 [vowpalwabbit을](https://github.com/VowpalWabbit) 사용합니다. [vowpalwabbit를](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) 사용하여 학습 설정을 편집하는 방법에 대한 vowpalwabbit 문서를 참조하십시오. 올바른 명령줄 인수가 있으면 다음 형식의 파일에 명령을 저장하고(인수 속성 값을 원하는 명령으로 대체) 개인 설정에 대한 Azure 포털의 **모델 및 학습 설정** 창에서 학습 설정을 가져오도록 파일을 업로드합니다.

다음은 `.json` 학습 정책의 예입니다.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>학습 정책 비교

오프라인 평가를 수행하여 Personalizer 로그에서 다른 학습 정책이 이전 데이터와 수행하는 방식을 비교할 수 [있습니다.](concepts-offline-evaluation.md)

[자신의 학습 정책을 업로드하여 현재 학습 정책과 비교합니다.](how-to-manage-model.md)

## <a name="optimize-learning-policies"></a>학습 정책 최적화

개인 설정은 [오프라인 평가에서](how-to-offline-evaluation.md)최적화된 학습 정책을 만들 수 있습니다. 오프라인 평가에서 더 나은 보상을 제공하는 최적화된 학습 정책은 Personalizer에서 온라인으로 사용될 때 더 나은 결과를 얻을 수 있습니다.

학습 정책을 최적화한 후 현재 정책을 즉시 대체할 수 있도록 맞춤 설정에 직접 적용할 수 있습니다. 또는 추가 평가를 위해 최적화된 정책을 저장하고 나중에 삭제, 저장 또는 적용할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [활성 및 비활성 이벤트에](concept-active-inactive-events.md)대해 알아봅니다.
