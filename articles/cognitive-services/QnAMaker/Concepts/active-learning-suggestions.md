---
title: 활성 학습 제안-QnA Maker
description: 활성 학습 제안을 통해 사용자의 질문 및 답변 쌍에 대 한 사용자 제출에 따라 대체 질문을 제안 하 여 기술 자료의 품질을 향상 시킬 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: e8cdb1d471ceef2aef790c426424b9224faac5ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777040"
---
# <a name="active-learning-suggestions"></a>활성 학습 제안

_활성 학습 제안_ 기능을 사용 하면 질문 및 답변 쌍에 대 한 사용자 제출에 따라 대체 질문을 제안 하 여 기술 자료의 품질을 향상 시킬 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다.

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용을 적용 하려면 제안에 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="what-is-active-learning"></a>활성 학습 이란?

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.

* [암시적 피드백](#how-qna-makers-implicit-feedback-works) – ranker은 사용자 질문에 매우 가까운 점수와 함께 여러 답변이 있는 경우이를 이해 하 고이를 사용자 의견으로 간주 합니다. 이렇게 하려면 아무것도 수행할 필요가 없습니다.
* [명시적 피드백](#how-you-give-explicit-feedback-with-the-train-api) – 기술 자료에서 점수가 약간 변형 된 여러 답변이 반환 되는 경우 클라이언트 응용 프로그램은 사용자에 게 정확한 질문을 묻습니다. 사용자의 명시적인 피드백이 [학습 API](../How-to/improve-knowledge-base.md#train-api)를 사용 하 여 QnA Maker 전송 됩니다.

두 방법 모두 클러스터링 된 유사한 쿼리를 사용 하 여 ranker를 제공 합니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습은 QnA Maker에서 반환 하는 가장 많은 답변의 점수를 기준으로 트리거됩니다. 쿼리와 일치 하는 QnA 쌍의 점수 차이가 작은 범위 내에 있는 경우 쿼리는 가능한 각 QnA 쌍에 대 한 가능한 제안 (대체 질문)으로 간주 됩니다. 특정 QnA 쌍에 대해 제안 된 질문을 수락한 후에는 다른 쌍에 대해 거부 됩니다. 제안을 수락한 후 저장 하 고 학습 해야 합니다.

엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다. 5 개 이상의 유사한 쿼리가 클러스터링 되 면 30 분 마다 QnA Maker 기술 자료 디자이너에 게 허용 하거나 거부할 사용자 기반 질문을 제안 합니다. 모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다.

QnA Maker 포털에서 질문이 제안 되 면 해당 제안을 검토 하 고 수락 하거나 거부 해야 합니다. 제안을 관리 하는 API가 없습니다.

## <a name="turn-on-active-learning"></a>활성 학습 설정

기본적으로 활성 학습은 **해제**되어 있습니다.
활성 학습을 사용 하려면:
* QnA Maker에서 기술 자료에 대 한 대체 질문을 수집 하도록 [활성 학습을 설정](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) 해야 합니다.
* 제안 된 대체 질문을 확인 하려면 편집 페이지에서 [보기 옵션을 사용](../How-To/improve-knowledge-base.md#view-suggested-questions) 합니다.

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker의 암시적 피드백 작동 방법

QnA Maker의 암시적 피드백은 알고리즘을 사용 하 여 근접 점수를 확인 한 다음 활성 학습 제안을 만듭니다. 점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 다음 예제의 범위는 수정 되지 않지만 알고리즘의 영향을 이해 하기 위한 지침으로 사용 되어야 합니다.

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내).

쿼리의 다음 JSON 응답에서 QnA Maker의 generateAnswer에 대 한, B, C의 점수는 가까이 있으며 제안 된 것으로 간주 됩니다.

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

QnA Maker 가장 좋은 답이 무엇 인지 알 수 없습니다. QnA Maker 포털의 제안 목록을 사용 하 여 최상의 답변을 선택 하 고 다시 학습 합니다.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>학습 API를 사용 하 여 명시적 피드백을 제공 하는 방법

QnA Maker에는 가장 적합 한 답변에 대 한 명시적인 피드백이 필요 합니다. 가장 좋은 답은 다음과 같이 결정 됩니다.

* 사용자 의견. 답변 중 하나를 선택 합니다.
* 허용 가능한 점수 범위를 결정 하는 등의 비즈니스 논리
* 사용자 피드백과 비즈니스 논리의 조합입니다.

[학습 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) 를 사용 하 여 사용자가 선택한 후 QnA Maker에 올바른 답변을 보냅니다.

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 쿼리](query-knowledge-base.md)