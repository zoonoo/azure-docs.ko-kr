---
title: LUIS 앱 학습 - Azure | Microsoft Docs
description: LUIS(Language Understanding)를 사용하여 모델을 학습합니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8ec731ee2110b21a35d76d53fae4a1a6756fb5b8
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951817"
---
# <a name="train-your-luis-app"></a>LUIS 앱 학습

학습은 해당 자연어 이해를 개선하기 위해 LUIS(Language Understanding) 앱을 교육하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

앱 학습 및 [테스트](luis-concept-test.md)는 반복적인 프로세스입니다. LUIS 앱을 학습한 후에 샘플 음성으로 앱을 테스트하여 의도 및 엔티티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다. 

## <a name="train-your-app"></a>앱 학습
반복적인 프로세스를 시작하려면 먼저 적어도 한 번 LUIS 앱을 학습해야 합니다. 학습하기 전에 모든 의도에 하나 이상의 발언이 있는지 확인합니다.

1. **내 앱** 페이지에서 해당 이름을 선택하여 앱에 액세스합니다. 

2. 앱의 위쪽 패널에서 **학습**을 선택합니다. 

    ![학습 단추](./media/luis-how-to-train/train-button.png)

3. 학습이 완료되면 녹색 알림 표시줄이 브라우저의 위쪽에 나타납니다.

    ![앱 학습 및 테스트 페이지](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>앱에 예제 발언이 포함되지 않는 하나 이상의 의도가 있는 경우 앱을 학습할 수 없습니다. 모든 의도에 발언을 추가합니다. 자세한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [LUIS로 제안된 음성에 레이블 지정](luis-how-to-review-endoint-utt.md) 
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md) 