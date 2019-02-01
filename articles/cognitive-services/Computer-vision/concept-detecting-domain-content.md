---
title: 도메인 특정 콘텐츠 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이미지에 대한 보다 자세한 정보를 반환하기 위해 이미지 범주화 도메인을 지정하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 76484a2340e527dc016f321dbafa29adb7c358b5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157537"
---
# <a name="detecting-domain-specific-content"></a>도메인 특정 콘텐츠 검색

Computer Vision은 태그 지정 및 최상위 수준 분류 외에도 특수(또는 도메인 특정) 정보를 지원합니다. 특수 정보는 독립 실행형 메서드로 구현하거나 상위 수준 분류를 사용하여 구현할 수 있습니다. 도메인 특정 모델을 추가하여 86개 범주 분류를 더욱 세분화하는 수단으로 사용됩니다.

도메인 특정 모델을 사용하는 두 가지 옵션이 있습니다.

* 범위가 지정된 분석  
  HTTP POST를 호출하여 선택한 모델만 분석합니다. 사용할 모델을 알고 있는 경우 모델의 이름을 지정합니다. 해당 모델에 관련된 정보만 얻게 됩니다. 예를 들어 이 옵션을 사용하여 유명인 인식만 찾을 수 있습니다. 응답에는 잠재적 일치 유명인 목록이 신뢰도 점수와 함께 포함됩니다.
* 향상된 분석  
  분석을 통해 86개 범주 분류의 범주와 관련된 추가 세부 정보를 제공합니다. 이 옵션은 사용자가 하나 이상 도메인 특정 모델의 세부 정보 외에 일반 이미지 분석을 가져오려는 애플리케이션에서 사용할 수 있습니다. 이 메서드를 호출하는 경우 86개 범주 분류의 분류자가 먼저 호출됩니다. 범주 중 하나가 알려지거나 일치하는 모델의 범주와 일치할 경우 분류자 호출이 두 번째로 전달됩니다. 예를 들어 HTTP POST 호출의 `details` 매개 변수가 "all"로 설정되거나 "celebrities"를 포함하는 경우 메서드는 86개 범주 분류자가 호출된 후에 유명인 분류자를 호출합니다. 이미지가 `people_` 또는 해당 범주의 하위 범주로 분류되는 경우 유명인 분류자를 호출합니다.

## <a name="listing-domain-specific-models"></a>도메인 특정 모델 나열

Computer Vision에서 지원되는 도메인 특정 모델을 나열할 수 있습니다. 현재 Computer Vision은 도메인 관련 콘텐츠를 검색하는 데 다음과 같은 도메인 특정 모델을 지원합니다.

| Name | 설명 |
|------|-------------|
| 유명인 | `people_` 범주에서 분류된 이미지에 대해 지원되는 유명인 인식 |
| 랜드마크 | `outdoor_` 또는 `building_` 범주에서 분류된 이미지에 대해 지원되는 랜드마크 인식 |

### <a name="domain-model-list-example"></a>도메인 모델 목록 예제

다음과 같은 JSON은 Computer Vision에서 지원되는 도메인 특정 모델을 나열합니다.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

[이미지 분류](concept-categorizing-images.md)에 대한 개념을 알아봅니다.
