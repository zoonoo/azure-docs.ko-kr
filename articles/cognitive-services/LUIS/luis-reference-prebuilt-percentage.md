---
title: Percentage 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 percentage 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 9b9faaae78cd1e3590291aef68db47f57f050f3d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165693"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Percentage 미리 빌드된 엔터티
Percentage 수치는 분수, `3 1/2` 또는 백분율, `2%`로 표시될 수 있습니다. 이 엔터티를 이미 학습했기 때문에 percentage를 포함하는 예제 발언을 응용 프로그램 의도에 추가할 필요가 없습니다. Percentage 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-percentage"></a>Percentage 유형
Percentage는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-percentage-entity"></a>미리 빌드된 Percentage 엔터티의 해결
다음 예제에서는 **builtin.percentage** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 