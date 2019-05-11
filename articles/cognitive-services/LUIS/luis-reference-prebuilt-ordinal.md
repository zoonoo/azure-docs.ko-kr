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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 752a26b21854ec9030fc1945024ae461445815a9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146709"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Ordinal 미리 빌드된 엔터티
서수는 집합 내의 개체를 `first`, `second`, `third`와 같이 숫자로 나타낸 것입니다. 이 엔터티를 이미 학습했기 때문에 ordinal을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Ordinal 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-ordinal"></a>Ordinal 유형
Ordinal은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>미리 빌드된 ordinal 엔터티의 해결

### <a name="api-version-2x"></a>API 버전 2.x

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

### <a name="preview-api-version-3x"></a>미리 보기 API 버전 3.x

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `false`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `true`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 합니다 [백분율](luis-reference-prebuilt-percentage.md)를 [전화 번호](luis-reference-prebuilt-phonenumber.md), 및 [온도](luis-reference-prebuilt-temperature.md) 엔터티. 
