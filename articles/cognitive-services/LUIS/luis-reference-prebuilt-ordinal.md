---
title: 서 수 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 ordinal 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535460"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 Ordinal 미리 빌드된 엔터티
서수는 집합 내의 개체를 `first`, `second`, `third`와 같이 숫자로 나타낸 것입니다. 이 엔터티를 이미 학습했기 때문에 ordinal을 포함하는 예제 발언을 애플리케이션 의도에 추가할 필요가 없습니다. Ordinal 엔터티는 [여러 문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="types-of-ordinal"></a>Ordinal 유형
서 수는 [인식자-텍스트](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 리포지토리에서 관리 됩니다.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>미리 빌드된 ordinal 엔터티의 해결

쿼리에 대해 반환 되는 엔터티 개체는 다음과 같습니다.

`Order the second option`

#### <a name="v3-response"></a>[V3 응답](#tab/V3)

다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `false` .

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 자세한 정보 표시 응답](#tab/V3-verbose)
다음 JSON은 `verbose` 매개 변수를로 설정 하는입니다 `true` .

```json
"entities": {
    "ordinal": [
        2
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
```

#### <a name="v2-response"></a>[V2 응답](#tab/V2)

다음 예제에서는 **builtin.ordinal** 엔터티의 해결을 보여 줍니다.

```json
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
```
* * *

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [전화 번호](luis-reference-prebuilt-phonenumber.md)및 [온도](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아보세요.
