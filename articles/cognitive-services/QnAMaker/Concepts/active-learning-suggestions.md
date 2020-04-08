---
title: 적극적인 학습 제안 - QnA 메이커
description: 적극적인 학습 제안을 사용하면 사용자 제출에 따라 질문 및 답변 쌍에 다른 질문을 제안하여 기술 자료의 품질을 향상시킬 수 있습니다.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804221"
---
# <a name="active-learning-suggestions"></a>적극적인 학습 제안

_활성 학습 제안_ 기능을 사용하면 사용자 제출에 따라 질문 및 답변 쌍에 다른 질문을 제안하여 기술 자료의 품질을 향상시킬 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다.

기술 자료가 자동으로 변경되지는 않습니다. 변경 사항이 적용되기 위해서는 제안을 수락해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="what-is-active-learning"></a>적극적인 학습이란 무엇입니까?

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.

* [암시적 피드백](#how-qna-makers-implicit-feedback-works) - 순위는 사용자 질문에 매우 가까운 점수가 있는 여러 답변이 있을 때 이를 피드백으로 간주하는 경우를 이해합니다. 이런 일이 일어나기 위해 아무 것도 할 필요가 없습니다.
* [명시적 피드백](#how-you-give-explicit-feedback-with-the-train-api) - 점수의 변동이 거의 없는 여러 답변이 기술 자료에서 반환되는 경우 클라이언트 응용 프로그램은 사용자에게 올바른 질문이 무엇인지 묻습니다. 사용자의 명시적 피드백은 [기차 API를](../How-to/improve-knowledge-base.md#train-api)사용하여 QnA Maker로 전송됩니다.

두 방법 모두 랭커에 클러스터된 유사한 쿼리를 제공합니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습은 QnA 메이커에 의해 반환 된 상위 몇 가지 답변의 점수에 따라 트리거됩니다. 쿼리와 일치하는 QnA 쌍 간의 점수 차이가 작은 범위 내에 있는 경우 쿼리는 가능한 각 QnA 쌍에 대해 가능한 제안(대체 질문)으로 간주됩니다. 특정 QnA 쌍에 대해 제안된 질문을 수락하면 다른 쌍에 대해 거부됩니다. 당신은 제안을 수락 한 후, 저장하고 훈련하는 것을 기억해야합니다.

엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다. 5개 이상의 유사한 쿼리가 클러스터화되면 30분마다 QnA Maker는 사용자 기반 질문을 기술 자료 디자이너에게 수락하거나 거부할 것을 제안합니다. 모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다.

QnA Maker 포털에서 질문이 제안되면 해당 제안을 검토하고 수락하거나 거부해야 합니다. 제안을 관리할 API가 없습니다.

## <a name="turn-on-active-learning"></a>활성 학습 설정

기본적으로 활성 학습은 **꺼져 있습니다.**
활성 학습을 사용하려면 다음을 수행하십시오.
* QnA Maker가 기술 자료에 대한 대체 질문을 수집할 수 있도록 [활성 학습을 켜야](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) 합니다.
* 제안된 대체 질문을 보려면 편집 페이지에서 [보기 옵션을 사용합니다.](../How-To/improve-knowledge-base.md#view-suggested-questions)

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker의 암시적 피드백 작동 방식

QnA Maker의 암시적 피드백은 알고리즘을 사용하여 점수 근접성을 결정한 다음 적극적인 학습 제안을 합니다. 점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 다음 예제의 범위는 고정하기 위한 것이 아니라 알고리즘의 영향을 이해하는 지침으로 사용해야 합니다.

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내).

QnA Maker의 generateAnswer에 대한 쿼리에서 다음 JSON 응답에서 A, B 및 C의 점수가 가깝고 제안으로 간주됩니다.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA 메이커는 어떤 대답이 가장 좋은 대답인지 알 수 없습니다. QnA Maker 포털의 제안 목록을 사용하여 최상의 답변을 선택하고 다시 교육하십시오.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Train API를 통해 명시적인 피드백을 제공하는 방법

QnA Maker는 어떤 답변이 가장 좋은 답변이었는지에 대한 명시적인 피드백이 필요합니다. 가장 좋은 대답은 귀하에게 달려 있으며 다음을 포함할 수 있습니다.

* 사용자 피드백, 답변 중 하나를 선택합니다.
* 허용 가능한 점수 범위를 결정하는 것과 같은 비즈니스 논리입니다.
* 사용자 피드백과 비즈니스 논리의 조합입니다.

사용자가 선택한 후 [Train API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) 사용하여 QnA Maker에 정답을 보냅니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 쿼리](query-knowledge-base.md)