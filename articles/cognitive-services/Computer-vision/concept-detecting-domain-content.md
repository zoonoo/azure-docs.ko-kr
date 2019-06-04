---
title: 도메인 특정 콘텐츠 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이미지에 대한 보다 자세한 정보를 반환하기 위해 이미지 범주화 도메인을 지정하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e4b64e00f71768a8821c83a73b019f77089e1b3a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994489"
---
# <a name="detect-domain-specific-content"></a>도메인 특정 콘텐츠 감지

Computer Vision은 태깅 및 고급 분류뿐 아니라 특수 데이터에 대해 학습된 모델을 사용한 도메인 특정 분석을 추가로 지원합니다.

도메인 특정 모델을 사용하는 두 가지 방법은 단독으로 사용하거나(범위 지정 분석), 분류 기능의 향상으로 사용하는 것입니다.

### <a name="scoped-analysis"></a>범위가 지정된 분석

[Models/\<model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API를 호출하면 선택한 도메인 특정 모델만 사용하여 이미지를 분석할 수 있습니다.

다음은 지정된 이미지에 대해 **models/celebrities/analyze** API에서 반환된 샘플 JSON 응답입니다.

![Satya Nadella 준비, 웃는](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>향상된 분류 분석

도메인 특정 모델을 사용하여 일반 이미지 분석을 보완할 수도 있습니다. [분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 호출의 *details* 매개 변수에 도메인 특정 모델을 지정하여 [고급 분류](concept-categorizing-images.md)의 일부로 이 작업을 수행합니다.

이 경우 86개 범주 분류의 분류자가 먼저 호출됩니다. 감지된 범주에 일치하는 도메인 특정 모델이 있는 경우 해당 모델을 통해 이미지가 전달되고 결과가 추가됩니다.

다음 JSON 응답은 보다 광범위한 분류 분석의 `detail` 노드로 도메인 특정 분석을 포함할 수 있는 방법을 보여 줍니다.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>도메인 특정 모델 나열

현재 Computer Vision은 다음과 같은 도메인 특정 모델을 지원합니다.

| Name | 설명 |
|------|-------------|
| celebrities | `people_` 범주에서 분류된 이미지에 대해 지원되는 유명인 인식 |
| landmarks | `outdoor_` 또는 `building_` 범주에서 분류된 이미지에 대해 지원되는 랜드마크 인식 |

[Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API를 호출하면 각 모델이 적용될 수 있는 범주와 함께 이 정보가 반환됩니다.

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[이미지 분류](concept-categorizing-images.md)에 대한 개념을 알아봅니다.
