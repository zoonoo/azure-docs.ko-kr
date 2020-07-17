---
title: 학습 정책-Personalizer
description: 학습 설정은 모델 학습의 하이퍼 *매개 변수* 를 결정 합니다. 서로 다른 학습 설정에서 학습 된 동일한 데이터의 두 모델은 서로 다르게 작동 합니다.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84700735"
---
# <a name="learning-policy-and-settings"></a>학습 정책 및 설정

학습 설정은 모델 학습의 하이퍼 *매개 변수* 를 결정 합니다. 서로 다른 학습 설정에서 학습 된 동일한 데이터의 두 모델은 서로 다르게 작동 합니다.

[학습 정책 및 설정은](how-to-settings.md#configure-rewards-for-the-feedback-loop) Azure Portal의 Personalizer 리소스에 대해 설정 됩니다.

## <a name="import-and-export-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure Portal에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이 메서드를 사용 하 여 기존 정책을 저장 하 고 테스트 하 여 바꾸고 나중에 참조 및 감사 하기 위한 아티팩트로 소스 코드 제어에 보관 합니다.

Personalizer 리소스에 대 한 Azure Portal에서 학습 정책을 가져오고 내보내는 [방법](how-to-manage-model.md#import-a-new-learning-policy) 에 대해 알아봅니다.

## <a name="understand-learning-policy-settings"></a>학습 정책 설정 이해

학습 정책의 설정은 변경할 수 없습니다. 설정이 Personalizer에 미치는 영향을 이해 하는 경우에만 설정을 변경 하십시오. 이 지식이 없으면 Personalizer 모델 무효화를 비롯 한 문제가 발생할 수 있습니다.

Personalizer는 [vowpalwabbit](https://github.com/VowpalWabbit) 를 사용 하 여 이벤트를 학습 하 고 점수를 계산 합니다. Vowpalwabbit를 사용 하 여 학습 설정을 편집 하는 방법에 대 한 [vowpalwabbit 설명서](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) 를 참조 하세요. 올바른 명령줄 인수를 사용 하는 경우 명령을 다음 형식으로 파일에 저장 하 고 (arguments 속성 값을 원하는 명령으로 바꾸기) Personalizer 리소스에 대 한 Azure Portal의 **모델 및 학습 설정** 창에서 학습 설정을 가져올 수 있도록 파일을 업로드 합니다.

다음은 `.json` 학습 정책의 예입니다.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>학습 정책 비교

[오프 라인 평가](concepts-offline-evaluation.md)를 수행 하 여 Personalizer 로그의 이전 데이터에 대해 다양 한 학습 정책을 수행 하는 방법을 비교할 수 있습니다.

[사용자 고유의 학습 정책을 업로드](how-to-manage-model.md) 하 여 현재 학습 정책과 비교 합니다.

## <a name="optimize-learning-policies"></a>학습 정책 최적화

Personalizer는 [오프 라인 평가](how-to-offline-evaluation.md)에서 최적화 된 학습 정책을 만들 수 있습니다. Personalizer에서 온라인으로 사용 하는 경우 오프 라인 평가에 더 나은 보상을 주는 최적화 된 학습 정책을 통해 더 나은 결과를 얻을 수 있습니다.

학습 정책을 최적화 한 후에는이 정책을 Personalizer에 직접 적용 하 여 현재 정책을 즉시 바꿀 수 있습니다. 또는 추가 평가를 위해 최적화 된 정책을 저장 하 고 나중에 삭제, 저장 또는 적용할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [활성 및 비활성 이벤트](concept-active-inactive-events.md)에 대해 알아봅니다.
