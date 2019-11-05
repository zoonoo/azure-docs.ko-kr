---
title: Language Understanding에 대 한 미리 빌드된 모델
titleSuffix: Azure Cognitive Services
description: LUIS에는 일반적인 대화형 사용자 시나리오를 신속 하 게 추가 하기 위한 미리 작성 된 모델 집합이 포함 되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501025"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>일반적인 사용 시나리오에 대 한 미리 빌드된 모델 추가 

LUIS는 일반적인 의도 및 발언을 빠르게 추가할 수 있도록 미리 빌드된 도메인의 미리 빌드된 의도 집합을 포함하고 있습니다. 미리 빌드된 의도 집합을 사용하면 기능 추가를 위한 모델을 설계하지 않고도 쉽고 빠르게 대화형 클라이언트 앱에 기능을 추가할 수 있습니다. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>미리 빌드된 도메인 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. **의도** 페이지의 왼쪽 아래에 있는 도구 모음에서 **미리 빌드된 도메인 추가**를 선택합니다. 

1. **달력** 의도를 선택하고 **도메인 추가** 단추를 선택합니다.

    ![달력 미리 빌드된 도메인 추가](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 왼쪽 탐색 영역에서 **의도**를 선택하여 달력 의도를 봅니다. 이 도메인의 각 의도에는 `Calendar.` 접두사가 붙습니다. 발언을 함께 이 도메인의 두 엔터티 `Calendar.Location` 및 `Calendar.Subject`가 앱에 추가됩니다. 

### <a name="train-and-publish"></a>학습 및 게시

1. 도메인을 추가한 후에는 오른쪽 상단 도구 모음에서 **학습**을 선택하여 앱을 학습합니다. 

1. 위쪽 도구 모음에서 **게시**를 선택합니다. **프로덕션**에 게시합니다. 

1. 녹색 성공 알림이 표시되면 **엔드포인트 목록 참조** 링크를 선택하여 엔드포인트를 봅니다.

1. 엔드포인트를 선택합니다. 해당 엔드포인트에 대한 새 브라우저 탭이 열립니다. 브라우저 탭을 연 상태로 **테스트** 섹션을 계속 진행합니다.

### <a name="test"></a>테스트

**q** 매개 변수의 값을 추가하여 엔드포인트에서 새 의도를 테스트하세요. `Schedule a meeting with John Smith in Seattle next week`

LUIS가 올바른 의도 및 회의 제목을 반환합니다.

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>미리 빌드된 의도 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. **의도** 페이지의 의도 목록 위에 있는 도구 모음에서 **미리 빌드된 의도 추가**를 선택합니다. 

1. 팝업 대화 상자에서 **Utilities.Cancel** 의도를 선택합니다. 

    ![미리 빌드된 의도 추가](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. **완료** 단추를 선택합니다.

### <a name="train-and-test"></a>학습 및 테스트

1. 의도를 추가한 후에는 오른쪽 상단 도구 모음에서 **학습**을 선택하여 앱을 학습합니다. 

1. 오른쪽 도구 모음에서 **테스트**를 선택하여 새 의도를 테스트합니다. 

1. 텍스트 상자에 취소 발언을 입력합니다.

    |발언 테스트|예측 점수|
    |--|:--|
    |항공권을 취소하고 싶습니다.|0.67|
    |구매를 취소합니다.|0.52|
    |회의를 취소합니다.|0.56|

    ![미리 빌드된 의도 테스트](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 왼쪽에서 **엔터티**를 클릭합니다. 

1. **엔터티** 페이지에서 **미리 빌드된 엔터티 추가**를 클릭합니다.

1. **미리 빌드된 엔터티 추가** 대화 상자에서 미리 빌드된 엔터티 datetimeV2를 선택합니다. 

    ![미리 빌드된 엔터티 추가 대화 상자](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. **완료**를 선택합니다. 엔터티를 추가한 후에는 앱을 학습할 필요가 없습니다. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>게시-예측 끝점에서 미리 작성 한 모델 보기

미리 작성 된 모델의 값을 보는 가장 쉬운 방법은 게시 된 끝점에서 쿼리 하는 것입니다. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>미리 빌드된 엔터티 토큰이 포함된 엔터티 표시
 모델에 `HH-1234`미리 빌드된 번호_가 추가된 사용자 지정 엔터티_  및 [로 표시하려는 텍스트(예: ](luis-reference-prebuilt-number.md))가 있는 경우, LUIS 포털에서 사용자 지정 엔터티를 표시할 수 없습니다. 이것은 API로 표시할 수 있습니다. 

 이 유형의 토큰을 표시하려면 미리 빌드된 엔터티로 이미 표시되어 있는 부분에서 LUIS 앱으로부터 미리 빌드된 엔터티를 제거합니다. 앱을 학습할 필요가 없습니다. 그런 다음, 사용자 고유의 사용자 지정 엔터티를 사용하여 토큰을 표시합니다. 그리고 나서 미리 빌드된 엔터티를 LUIS 앱에 다시 추가합니다.

 다른 예제를 보려면 utterance를 클래스 기본 설정 목록으로 간주 합니다. LUIS app에 Fakes.prebuild 라고 하겠습니다 서 수가 추가 된 경우 `I want first year spanish, second year calculus, and fourth year english lit.`, `first`, `second`, `fourth`는 이미 서 수로 표시 됩니다. 서수 및 클래스를 캡처하려는 경우 복합 엔터티를 작성하고 클래스 이름에 대한 사용자 지정 엔터티 및 미리 빌드된 서수를 래핑하면 됩니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [REST Api를 사용 하 여 .csv에서 모델 빌드](./luis-tutorial-node-import-utterances-csv.md)
