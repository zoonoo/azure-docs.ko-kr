---
title: LUIS 미리 빌드된 엔터티 currency 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 currency 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b383e21f870d15818c540b79a9a56c1dd65fa342
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236825"
---
# <a name="currency-entity"></a>Currency 엔터티
미리 빌드된 currency 엔터티는 LUIS 앱 문화권에 관계없이 많은 액면가 및 국가에서 통화를 검색합니다. 이 엔터티를 이미 학습했기 때문에 currency를 포함하는 예제 발언을 응용 프로그램 의도에 추가할 필요가 없습니다. Currency 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-currency"></a>Currency 유형
Currency는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) Github 리포지토리에서 관리됩니다.

## <a name="resolution-for-currency-entity"></a>Currency 엔터티의 해결
다음 예제에서는 **builtin.currency** 엔터티의 해결을 보여 줍니다.

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md) 및 [email](luis-reference-prebuilt-email.md) 엔터티에 대해 알아봅니다. 