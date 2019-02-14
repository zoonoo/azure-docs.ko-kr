---
title: Age 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 age 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: a6aeffb40092252a4e48a200da640f22a149f089
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867310"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Age 미리 빌드된 엔터티
미리 빌드된 age 엔터티는 age 값을 숫자로, 일, 주, 월 및 년에 따라 캡처합니다. 이 엔터티를 이미 학습했기 때문에 age를 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Age 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-age"></a>Age 유형
Age는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-age-entity"></a>미리 빌드된 age 엔터티의 해결
다음 예제에서는 **builtin.age** 엔터티의 해결을 보여 줍니다.

```json
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
