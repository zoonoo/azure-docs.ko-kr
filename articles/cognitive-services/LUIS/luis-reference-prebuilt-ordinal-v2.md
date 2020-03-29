---
title: 서수 V2 미리 빌드된 엔터티 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 언어 이해(LUIS)의 서수 V2 미리 빌드된 엔터티 정보가 포함되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270492"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 서수 V2 미리 빌드된 엔터티
서수 V2 번호는 [에](luis-reference-prebuilt-ordinal.md) 대한 상대 참조를 `next`제공하기 `last`위해 `previous`서수를 확장합니다. 이러한 엔터티는 서수 미리 빌드된 엔터티를 사용하여 추출되지 않습니다.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>미리 빌드된 서수 V2 엔터티에 대한 해결 방법

다음 엔터티 개체는 쿼리에 대해 반환됩니다.

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON은 매개 `verbose` 변수를 `false`다음과 같은 것으로 설정합니다.

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 자세한 응답](#tab/V3-verbose)

다음 JSON은 매개 `verbose` 변수를 `true`다음과 같은 것으로 설정합니다.

```json
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
```
#### <a name="v2-response"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.ordinalV2** 엔터티의 해상도를 보여 주었습니다.

```json
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
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[백분율,](luis-reference-prebuilt-percentage.md)전화 [번호](luis-reference-prebuilt-phonenumber.md)및 [온도](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다.
