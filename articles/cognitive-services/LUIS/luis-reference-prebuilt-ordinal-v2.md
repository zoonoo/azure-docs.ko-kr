---
title: 서 수 V2 미리 작성 된 엔터티
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 문서에서는 서 수 V2 미리 작성 된 엔터티 정보에서 LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486198"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>서 수 V2 LUIS 앱에 대 한 미리 작성 된 엔터티
서 수 V2 수 확장 [서](luis-reference-prebuilt-ordinal.md) 상대 참조를 제공 하 `next`를 `last`, 및 `previous`합니다. 다음은 서 수 미리 작성 된 엔터티를 사용 하 여 추출 되지 않습니다.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>미리 작성 된 서 수 V2 엔터티에 대 한 확인

### <a name="api-version-2x"></a>API 버전 2.x

다음 예제에서는 확인 합니다 **builtin.ordinalV2** 엔터티.

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

### <a name="preview-api-version-3x"></a>미리 보기 API 버전 3.x

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `false`:

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

다음 JSON을 사용 하는 것은 `verbose` 매개 변수 설정 `true`:

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

에 대 한 자세한 합니다 [백분율](luis-reference-prebuilt-percentage.md)를 [전화 번호](luis-reference-prebuilt-phonenumber.md), 및 [온도](luis-reference-prebuilt-temperature.md) 엔터티. 
