---
title: Language Understanding용 미리 빌드된 도메인
titleSuffix: Azure Cognitive Services
description: LUIS는 일반적인 대화형 사용자 시나리오를 빠르게 추가할 수 있도록 미리 빌드된 도메인 집합을 포함하고 있습니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867106"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>일반적인 사용 시나리오를 위해 미리 빌드된 도메인 추가 

LUIS는 일반적인 의도 및 발언을 빠르게 추가할 수 있도록 미리 빌드된 도메인의 미리 빌드된 의도 집합을 포함하고 있습니다. 미리 빌드된 의도 집합을 사용하면 기능 추가를 위한 모델을 설계하지 않고도 쉽고 빠르게 대화형 클라이언트 앱에 기능을 추가할 수 있습니다. 

## <a name="add-a-prebuilt-domain"></a>미리 빌드된 도메인 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. **의도** 페이지의 왼쪽 아래에 있는 도구 모음에서 **미리 빌드된 도메인 추가**를 선택합니다. 

1. **달력** 의도를 선택하고 **도메인 추가** 단추를 선택합니다.

    ![달력 미리 빌드된 도메인 추가](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 왼쪽 탐색 영역에서 **의도**를 선택하여 달력 의도를 봅니다. 이 도메인의 각 의도에는 `Calendar.` 접두사가 붙습니다. 발언을 함께 이 도메인의 두 엔터티 `Calendar.Location` 및 `Calendar.Subject`가 앱에 추가됩니다. 

## <a name="train-and-publish"></a>학습 및 게시

1. 도메인을 추가한 후에는 오른쪽 상단 도구 모음에서 **학습**을 선택하여 앱을 학습합니다. 

1. 위쪽 도구 모음에서 **게시**를 선택합니다. **프로덕션**에 게시합니다. 

1. 녹색 성공 알림이 표시되면 **엔드포인트 목록 참조** 링크를 선택하여 엔드포인트를 봅니다.

1. 엔드포인트를 선택합니다. 해당 엔드포인트에 대한 새 브라우저 탭이 열립니다. 브라우저 탭을 연 상태로 **테스트** 섹션을 계속 진행합니다.

## <a name="test"></a>테스트

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

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [미리 빌드된 도메인 참조](./luis-reference-prebuilt-domains.md)
