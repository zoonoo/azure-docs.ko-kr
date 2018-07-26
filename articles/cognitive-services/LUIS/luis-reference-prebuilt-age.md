---
title: LUIS 미리 빌드된 엔터티 age 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 age 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237403"
---
# <a name="age-entity"></a>Age 엔터티
미리 빌드된 age 엔터티는 age 값을 숫자로, 일, 주, 월 및 년에 따라 캡처합니다. 이 엔터티를 이미 학습했기 때문에 age를 포함하는 예제 발언을 응용 프로그램 의도에 추가할 필요가 없습니다. Age 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-age"></a>Age 유형
Age는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-age-entity"></a>미리 빌드된 age 엔터티의 해결
다음 예제에서는 **builtin.age** 엔터티의 해결을 보여 줍니다.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) 및 [dimension](luis-reference-prebuilt-dimension.md) 엔터티에 대해 알아봅니다. 