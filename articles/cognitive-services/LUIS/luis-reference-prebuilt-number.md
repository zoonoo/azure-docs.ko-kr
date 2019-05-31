---
title: Number 미리 빌드된 엔터티
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 number 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d4f707d4bf9bac5e2208eadb94983af368b9f521
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072256"
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

### <a name="preview-api-version-3x"></a>미리 보기 API 버전 3.x

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `false`:

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

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `true`:

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
