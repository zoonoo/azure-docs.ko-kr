---
title: 미리 작성 한 차원 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 dimension 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 15ba8ad4d3bcf00024a0c7b14b004de08d37621a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677656"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Dimension 미리 빌드된 엔터티
미리 빌드된 dimension 엔터티는 LUIS 앱 문화권에 관계없이 다양한 유형의 차원을 검색합니다. 이 엔터티를 이미 학습했기 때문에 dimension을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Dimension 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다. 

## <a name="types-of-dimension"></a>Dimension 유형

Dimension은 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub 리포지토리에서 관리됩니다.


## <a name="resolution-for-dimension-entity"></a>dimension 엔터티의 해결

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 끝점 응답](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 끝점 응답](#tab/V3)

다음 JSON은 `verbose` 매개 변수를 `false`로 설정 합니다.

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

다음 JSON은 `verbose` 매개 변수를 `true`로 설정 합니다.

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 끝점](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다. 
