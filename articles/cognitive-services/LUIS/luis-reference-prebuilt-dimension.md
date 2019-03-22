---
title: Dimension 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 dimension 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 9099cdbb91e41998065d953b9d48b3b501df7c10
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336940"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Dimension 미리 빌드된 엔터티
미리 빌드된 dimension 엔터티는 LUIS 앱 문화권에 관계없이 다양한 유형의 차원을 검색합니다. 이 엔터티를 이미 학습했기 때문에 dimension을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Dimension 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-dimension"></a>Dimension 유형

Dimension은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub 리포지토리에서 관리됩니다.


## <a name="resolution-for-dimension-entity"></a>dimension 엔터티의 해결
다음 예제에서는 **builtin.dimension** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다. 
