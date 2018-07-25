---
title: LUIS의 미리 빌드된 엔터티 | Microsoft Docs
description: 이 문서에는 LUIS(Language Understanding Intelligent Services)에 포함된 미리 빌드된 엔터티 목록이 포함되어 있습니다.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 0dc1dbf32114e989fd2b9c2be61bd531f7b5e839
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347343"
---
# <a name="prebuilt-entities"></a>미리 빌드된 엔터티

LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다. 문화권별 지원을 비롯하여 LUIS에서 지원하는 미리 빌드된 엔터티의 전체 목록에 대해서는 [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)를 참조하세요.

> [!NOTE]
> **builtin.datetime**은 더 이상 사용되지 않습니다. 이 엔터티는 모호한 날짜 및 시간의 인식을 개선하고 날짜 및 시간 범위를 인식할 수 있도록 하는 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md)로 대체됩니다.

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 왼쪽에서 **엔터티**를 클릭합니다. 
2. **엔터티** 페이지에서 **미리 빌드된 엔터티 관리**를 클릭합니다.

    ![엔터티 페이지 - 미리 빌드된 엔터티 관리](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. **미리 빌드된 엔터티 추가** 대화 상자에서 추가하려는 미리 빌드된 엔터티(예: "datetimeV2")를 추가합니다. 그런 다음 **Save**를 클릭합니다.

    ![미리 빌드된 엔터티 추가 대화 상자](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>미리 빌드된 number 엔터티 사용
미리 빌드된 엔터티가 응용 프로그램에 포함되어 있으면, 게시된 응용 프로그램에 해당 예측이 포함됩니다. 미리 빌드된 엔터티의 동작은 미리 학습되며 수정할 수 **없습니다**. 다음 단계에 따라 미리 빌드된 엔터티가 작동하는 방식을 확인하세요.

1. **number** 엔터티를 앱에 추가하고 앱을 [학습](interactive-test.md) 및 [게시](luis-how-to-publish-app.md)합니다.
2. **앱 게시** 페이지에서 끝점 URL을 클릭하여 웹 브라우저에서 LUIS 끝점을 엽니다. 
3. 숫자 식이 포함된 URL에 발언을 추가합니다. 예를 들어, `buy two plane ticktets`를 입력하면 LUIS는 `two`를 `builtin.number` 엔터티로, `2`를 `resolution` 필드의 해당 값으로 식별하는 것을 확인할 수 있습니다. `resolution` 필드는 숫자와 날짜를 클라이언트 응용 프로그램이 보다 쉽게 사용할 수 있는 정규 양식으로 해석하는 데 도움이 됩니다. 

    ![number 엔터티를 포함하는 브라우저의 발언](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS는 표준 양식이 아닌 숫자를 지능적으로 인식할 수 있습니다. 발언에서 여러 다른 숫자 식을 사용하면서 LUIS가 반환하는 결과를 확인해보세요.

다음 예제에서는 발언 "two dozen"에 대해 값 24라는 해결을 포함하는 LUIS의 JSON 응답을 보여줍니다.

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>미리 빌드된 number datetimeV2 사용
**datetimeV2** 미리 빌드된 엔터티는 날짜, 시간, 날짜 범위 및 시간 기간을 인식합니다. 다음 단계에 따라 `datetimeV2` 미리 빌드된 엔터티가 작동하는 방식을 확인하세요.

1. **datetimeV2** 엔터티를 앱에 추가하고 앱을 [학습](interactive-test.md) 및 [게시](luis-how-to-publish-app.md)합니다.
2. **앱 게시** 페이지에서 끝점 URL을 클릭하여 웹 브라우저에서 LUIS 끝점을 엽니다. 
3. 날짜 범위가 포함된 URL에 발언을 추가합니다. 예를 들어, `book a flight tomorrow`를 입력하면 LUIS는 `tomorrow`를 `builtin.datetimeV2.date` 엔터티로, 내일 날짜를 `resolution` 필드의 해당 값으로 식별하는 것을 확인할 수 있습니다. 

다음 예제에서는 오늘 날짜가 2017년 10월 31일인 경우 LUIS의 JSON 응답이 어떻게 표시되는지를 보여 줍니다.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)
