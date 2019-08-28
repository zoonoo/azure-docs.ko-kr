---
title: 미리 작성 한 엔터티 수-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 number 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f2868610bf9215b1414ea3ba342e3064b9c54f63
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933497"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Number 미리 빌드된 엔터티
숫자 값은 다양한 방식으로 정보를 정량화하고, 표현하고, 설명하는 데 사용됩니다. 이 문서에서는 가능한 예제 중 일부만 제공합니다. LUIS는 사용자 발언에서 변형을 해석하고 일관된 숫자 값을 반환합니다. 이 엔터티를 이미 학습했기 때문에 number를 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. 

## <a name="types-of-number"></a>Number의 유형
Number는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub 리포지토리에서 관리됩니다.

## <a name="examples-of-number-resolution"></a>Number 해결 예제

| 발화        | 엔터티   | 해결 방법 |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS에서 반환하는 JSON 응답의 `resolution` 필드에는 **`builtin.number`** 엔터티의 인식된 값이 포함되어 있습니다.

## <a name="resolution-for-prebuilt-number"></a>미리 빌드된 number의 해결


### <a name="api-version-2x"></a>API 버전 2.x

다음 예제에서는 발언 "two dozen"에 대해 값 24라는 해결을 포함하는 LUIS의 JSON 응답을 보여줍니다.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Preview API 버전 3(sp3)

다음 JSON은 `verbose` 매개 변수를로 `false`설정 하는입니다.

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

다음 JSON은 `verbose` 매개 변수를로 `true`설정 하는입니다.

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

[currency](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md) 및 [percentage](luis-reference-prebuilt-percentage.md)에 대해 알아봅니다. 
