---
title: 이미지 분류 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 범주화 기능과 관련된 개념에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 40873b13407066920caec5c04751b65c01d7e209
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579501"
---
# <a name="image-categorization-with-computer-vision"></a>Computer Vision을 사용하여 이미지 범주화

Computer Vision은 태그 지정 및 설명 외에도 이전 버전에서 정의된 분류 기반 범주를 반환합니다. 이러한 범주는 부모/자식 계층 구조를 가진 분류로 구성되어 있습니다. 모든 범주는 영어로 표시됩니다. 범주는 단독으로 사용하거나 새 태그 지정 모델에서 사용할 수 있습니다.

## <a name="the-86-category-concept"></a>86개 범주 개념

다음 다이어그램에 표시된 86개 개념 목록에 따라 이미지는 대분류에서 소분류로 분류될 수 있습니다. 전체 분류를 텍스트 형식으로 보려면 [범주 분류](category-taxonomy.md)를 참조하세요.

![범주 분류에 있는 모든 범주의 그룹화된 목록](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>이미지 분류 예제

다음 JSON 응답에서는 해당 시각적 기능에 따라 예제 이미지를 범주화할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![아파트 건물 옥상 위의 여자](./Images/woman_roof.png)

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
| ![가족으로 함께 포즈를 취한 4명의 사람](./Images/family_photo.png) | people_group |
| ![잔디밭에 앉아 있는 강아지](./Images/cute_dog.png) | animal_dog |
| ![석양이 보이는 산 바위에 서 있는 사람](./Images/mountain_vista.png) | outdoor_mountain |
| ![테이블 위에 쌓여있는 빵](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 설명](concept-describing-images.md)에 대한 개념을 알아봅니다.