---
title: 서 수 V2 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding (LUIS)의 서 수 V2 미리 작성 된 엔터티 정보가 포함 되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 972f75fd1c977e79a2fa70c44bb3069e2c69a2c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563404"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱에 대 한 서 수 V2 미리 작성 된 엔터티
서 수 V2 number는 [서 수](luis-reference-prebuilt-ordinal.md) 를 확장 하 여 `next`, `last`및 `previous`와 같은 상대 참조를 제공 합니다. 이러한 항목은 미리 작성 된 서 수 엔터티를 사용 하 여 추출 되지 않습니다.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>미리 빌드된 서 수 V2 엔터티에 대 한 해결 방법

### <a name="api-version-2x"></a>API 버전 2.x

다음 예제에서는 ordinalV2 엔터티를 확인 하는 방법을 보여 줍니다 **.**

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>Preview API 버전 3(sp3)

다음 JSON은 `verbose` 매개 변수를로 `false`설정 하는입니다.

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

다음 JSON은 `verbose` 매개 변수를로 `true`설정 하는입니다.

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
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

[백분율](luis-reference-prebuilt-percentage.md), [전화 번호](luis-reference-prebuilt-phonenumber.md)및 [온도](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 
