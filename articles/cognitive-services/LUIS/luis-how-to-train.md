---
title: 앱 교육
titleSuffix: Language Understanding - Azure Cognitive Services
description: 학습은 해당 자연어 이해를 향상시키기 위해 LUIS(Language Understanding) 앱 버전을 학습하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 1c3c7e913f2f096918efaebb84384df98bd68be8
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895835"
---
# <a name="train-your-active-version-of-the-luis-app"></a>LUIS 앱의 활성 버전 학습 

학습은 해당 자연어 이해를 개선하기 위해 LUIS(Language Understanding) 앱을 교육하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

앱 학습 및 [테스트](luis-concept-test.md)는 반복적인 프로세스입니다. LUIS 앱을 학습한 후에 샘플 음성으로 앱을 테스트하여 의도 및 엔티티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다. 

학습은 LUIS 포털에서 현재 사용 중인 버전에 적용됩니다. 

## <a name="how-to-train-interactively"></a>대화형으로 학습하는 방법

[LUIS 포털](https://www.luis.ai)에서 반복적인 프로세스를 시작하려면 먼저 적어도 한 번 LUIS 앱을 학습해야 합니다. 학습하기 전에 모든 의도에 하나 이상의 발언이 있는지 확인합니다.

1. **내 앱** 페이지에서 해당 이름을 선택하여 앱에 액세스합니다. 

2. 앱의 위쪽 패널에서 **학습**을 선택합니다. 

3. 학습이 완료되면 녹색 알림 표시줄이 브라우저의 위쪽에 나타납니다.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>앱에 예제 발언이 포함되지 않는 하나 이상의 의도가 있는 경우 앱을 학습할 수 없습니다. 모든 의도에 발언을 추가합니다. 자세한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

## <a name="train-with-all-data"></a>모든 데이터를 사용하여 학습

학습은 음수 샘플링(negative sampling)의 작은 비율을 사용합니다. 작은 음수 샘플링 대신 모든 데이터를 사용하려면 `UseAllTrainingData`가 true로 설정된 [버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)를 사용하여 이 기능을 해제합니다. 

## <a name="unnecessary-training"></a>불필요한 학습

변경할 때마다 학습할 필요는 없습니다. 학습은 변경 그룹을 모델에 적용한 후 수행해야 하며, 수행할 다음 단계는 테스트 또는 게시입니다. 테스트하거나 게시할 필요가 없으면 학습이 필요하지 않습니다. 

## <a name="training-with-the-rest-apis"></a>REST API로 학습

LUIS 포털에서 학습은 **학습** 단추를 한 번만 누르면 됩니다. REST API를 사용한 학습은 2단계 프로세스입니다. 첫 번째 방법은 HTTP POST를 사용한 [학습 요청](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)입니다. 그런 다음, HTTP Get을 사용하여 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)를 요청합니다. 

학습이 완료될 때를 알기 위해 모든 모델이 성공적으로 학습될 때까지 상태를 폴링해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [LUIS 사용 하 여 제안 된 길이 발언 레이블](luis-how-to-review-endpoint-utterances.md) 
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md) 
