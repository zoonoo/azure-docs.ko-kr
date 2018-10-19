---
title: 이미지 분류 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지를 분류하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 602ea8028cf89b23df692d5c2fb9b781f64bcad4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341687"
---
# <a name="categorizing-images"></a>이미지 분류

Computer Vision은 태그 지정 및 설명 외에도 이전 버전에서 정의된 분류 기반 범주를 반환합니다. 이러한 범주는 부모/자식 계층 구조를 가진 분류로 구성되어 있습니다. 모든 범주는 영어로 표시됩니다. 범주는 단독으로 사용하거나 새 태그 지정 모델에서 사용할 수 있습니다.

## <a name="the-86-category-concept"></a>86개 범주 개념

다음 다이어그램에 표시된 86개 개념 목록에 따라 이미지는 대분류에서 소분류로 분류될 수 있습니다. 전체 분류를 텍스트 형식으로 보려면 [범주 분류](category-taxonomy.md)를 참조하세요.

![범주 분석](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>이미지 분류 예제

다음 JSON 응답에서는 해당 시각적 기능에 따라 예제 이미지를 범주화할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![여성 지붕](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

다음 표에서는 각 이미지에 대해 Computer Vision에서 반환된 일반적인 이미지 집합 및 범주를 보여줍니다.

| 이미지 | Category |
|-------|----------|
| ![가족 사진](./Images/family_photo.png) | people_group |
| ![귀여운 개](./Images/cute_dog.png) | animal_dog |
| ![옥외 산](./Images/mountain_vista.png) | outdoor_mountain |
| ![비전 분석 음식 빵](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 설명](concept-describing-images.md)에 대한 개념을 알아봅니다.