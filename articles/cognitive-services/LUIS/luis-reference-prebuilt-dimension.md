---
title: 미리 작성 한 차원 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 dimension 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 0b5f081dc8c0bc203ccafde4a513371d11e36879
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538350"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Dimension 미리 빌드된 엔터티
미리 빌드된 dimension 엔터티는 LUIS 앱 문화권에 관계없이 다양한 유형의 차원을 검색합니다. 이 엔터티를 이미 학습했기 때문에 dimension을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Dimension 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="types-of-dimension"></a>Dimension 유형

차원은 [인식기 텍스트](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub 리포지토리에서 관리 됩니다.

## <a name="resolution-for-dimension-entity"></a>dimension 엔터티의 해결

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `false` .

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `true` .

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

다음 예제에서는 **builtin.dimension** 엔터티의 해결을 보여 줍니다.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다.
