---
title: Ordinal 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 ordinal 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f6762c74dbe6b16791e597464584b91e696220f1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863453"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Ordinal 미리 빌드된 엔터티
서수는 집합 내의 개체를 `first`, `second`, `third`와 같이 숫자로 나타낸 것입니다. 이 엔터티를 이미 학습했기 때문에 ordinal을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Ordinal 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-ordinal"></a>Ordinal 유형
Ordinal은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>미리 빌드된 ordinal 엔터티의 해결
다음 예제에서는 **builtin.ordinal** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-phonenumber.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 
