---
title: 미리 빌드된 의도
titleSuffix: Azure Cognitive Services
description: LUIS는 일반적인 대화형 사용자 시나리오를 빠르게 추가할 수 있도록 미리 빌드된 의도 집합을 포함하고 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 8fddbcf82bdbb052468b97754554da01bac7d82b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103730"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>일반 의도에 대해 미리 빌드된 의도 추가 

LUIS는 일반적인 의도 및 발언을 빠르게 추가할 수 있도록 미리 빌드된 도메인의 미리 빌드된 의도 집합을 포함하고 있습니다. 미리 빌드된 의도 집합을 사용하면 기능 추가를 위한 모델을 설계하지 않고도 쉽고 빠르게 대화형 클라이언트 앱에 기능을 추가할 수 있습니다. 

## <a name="add-a-prebuilt-intent"></a>미리 빌드된 의도 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. **의도** 페이지의 의도 목록 위에 있는 도구 모음에서 **미리 빌드된 의도 추가**를 선택합니다. 

1. 팝업 대화 상자에서 **Utilities.Cancel** 의도를 선택합니다. 

    ![미리 빌드된 의도 추가](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. **완료** 단추를 선택합니다.

## <a name="train-and-test"></a>학습 및 테스트

1. 의도를 추가한 후에는 오른쪽 상단 도구 모음에서 **학습**을 선택하여 앱을 학습합니다. 

1. 오른쪽 도구 모음에서 **테스트**를 선택하여 새 의도를 테스트합니다. 

1. 텍스트 상자에 취소 발언을 입력합니다.

    |발언 테스트|예측 점수|
    |--|:--|
    |항공권을 취소하고 싶습니다.|0.67|
    |구매를 취소합니다.|0.52|
    |회의를 취소합니다.|0.56|

    ![미리 빌드된 의도 테스트](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [미리 빌드된 엔터티](./luis-prebuilt-entities.md)