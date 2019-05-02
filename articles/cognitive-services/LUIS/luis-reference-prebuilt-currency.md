---
title: 통화 미리 작성 된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 currency 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 1dfa81ad7981578d4f296de1b421c7e064819718
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867284"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 미리 빌드된 Currency 엔터티
미리 작성 된 통화 엔터티 denominations 및 국가/지역, LUIS 앱 문화권에 관계 없이 여러 통화를 검색합니다. 이 엔터티를 이미 학습했기 때문에 currency를 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Currency 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-currency"></a>Currency 유형
Currency는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-currency-entity"></a>Currency 엔터티의 해결
다음 예제에서는 **builtin.currency** 엔터티의 해결을 보여 줍니다.

```json
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
