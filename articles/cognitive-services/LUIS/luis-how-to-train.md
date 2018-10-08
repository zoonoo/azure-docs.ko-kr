---
title: LUIS 버전 학습
titleSuffix: Azure Cognitive Services
description: 학습은 해당 자연어 이해를 향상시키기 위해 LUIS(Language Understanding) 앱 버전을 학습하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182042"
---
# <a name="train-your-luis-app-version"></a>LUIS 앱 버전 학습

학습은 해당 자연어 이해를 개선하기 위해 LUIS(Language Understanding) 앱을 교육하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

앱 학습 및 [테스트](luis-concept-test.md)는 반복적인 프로세스입니다. LUIS 앱을 학습한 후에 샘플 음성으로 앱을 테스트하여 의도 및 엔티티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다. 

## <a name="how-to-train"></a>학습 방법
반복적인 프로세스를 시작하려면 먼저 적어도 한 번 LUIS 앱을 학습해야 합니다. 학습하기 전에 모든 의도에 하나 이상의 발언이 있는지 확인합니다.

1. **내 앱** 페이지에서 해당 이름을 선택하여 앱에 액세스합니다. 

2. 앱의 위쪽 패널에서 **학습**을 선택합니다. 

3. 학습이 완료되면 녹색 알림 표시줄이 브라우저의 위쪽에 나타납니다.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>앱에 예제 발언이 포함되지 않는 하나 이상의 의도가 있는 경우 앱을 학습할 수 없습니다. 모든 의도에 발언을 추가합니다. 자세한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

## <a name="train-with-all-data"></a>모든 데이터를 사용하여 학습
학습은 음수 샘플링(negative sampling)의 작은 비율을 사용합니다. 작은 음수 샘플링 대신 모든 데이터를 사용하려면 `UseAllTrainingData`가 true로 설정된 [버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)를 사용하여 하여 이 기능을 해제합니다. 

## <a name="next-steps"></a>다음 단계

* [LUIS로 제안된 발화에 레이블 지정](luis-how-to-review-endoint-utt.md) 
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md) 