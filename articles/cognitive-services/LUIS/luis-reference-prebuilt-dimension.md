---
title: Dimension 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 dimension 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: fcd90d42f297cec86b32f9a806df4a148e6a93af
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214537"
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
