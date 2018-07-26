---
title: LUIS 미리 빌드된 온도 엔터티 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 아티클에는 LUIS(Language Understanding)의 미리 빌드된 온도 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 6436a7ee8d7b796595813fa613c442824aeae8f3
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237464"
---
# <a name="temperature-entity"></a>온도 엔터티
온도는 다양한 온도 형식을 추출합니다. 이 엔터티를 이미 학습했기 때문에 온도를 포함하는 예제 발언을 응용 프로그램에 추가할 필요가 없습니다. 온도 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-temperature"></a>온도 형식
온도는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-temperature-entity"></a>미리 빌드된 온도 엔터티의 해결 방법
다음 예제에서는 **builtin.temperature** 엔터티의 해결 방법을 보여줍니다.

```JSON
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [age](luis-reference-prebuilt-age.md) 엔터티에 대해 알아봅니다. 