---
title: Geography V2 미리 빌드된 엔터티
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding(LUIS)의 geographyV2 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: a81c97cf8883d2a3629d1eb1b9f6137609c6edda
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135103"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 GeographyV2 미리 빌드된 엔터티
미리 빌드된 geographyV2 엔터티는 위치를 검색합니다. 이 엔터티를 이미 학습했기 때문에 GeographyV2를 응용 프로그램 의도에 포함하는 예제 발화를 추가할 필요가 없습니다. GeographyV2 엔터티는 영어 [문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="subtypes"></a>하위 유형
지리적 위치에는 하위 유형이 있습니다.

|Subtype|목적|
|--|--|
|`poi`|관심 지점|
|`city`|도시 이름|
|`countryRegion`|국가 또는 지역 이름|
|`continent`|대륙 이름|
|`state`|시/도 이름|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 엔터티의 해결
다음 예제에서는 **builtin.geographyV2** 엔터티의 해결을 보여 줍니다.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>다음 단계

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다. 