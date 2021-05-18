---
title: Ordinal V2 미리 빌드된 엔터티 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 ordinal V2 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541937"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Ordinal V2 미리 빌드된 엔터티
Ordinal V2 서수는 [Ordinal](luis-reference-prebuilt-ordinal.md)을 확장하여`next`, `last` 및 `previous`와 같은 상대 참조를 제공합니다. 이러한 항목은 미리 빌드된 서수 엔터티를 사용하여 추출되지 않습니다.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>미리 빌드된 ordinal V2 엔터티의 해결

쿼리에 대해 반환되는 엔터티 개체는 다음과 같습니다.

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON의 `verbose` 매개 변수가 `false`로 설정되어 있습니다.

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

#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)

다음 JSON의 `verbose` 매개 변수가 `true`로 설정되어 있습니다.

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

다음 예에서는 **builtin.ordinalV2** 엔터티의 해결 방법을 보여 줍니다.

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

[percentage](luis-reference-prebuilt-percentage.md), [phone number](luis-reference-prebuilt-phonenumber.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다.
