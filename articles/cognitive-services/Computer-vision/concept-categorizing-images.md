---
title: 이미지 분류 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 범주화 기능과 관련된 개념에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995577"
---
# <a name="categorize-images-by-subject-matter"></a>주제로 이미지 분류

Computer Vision은 태그 지정 및 설명 외에도 이미지에서 감지된 분류 기반 범주를 반환합니다. 태그와 달리, 범주는 부모/자식 계층으로 구성되고 개수가 더 적습니다(태그는 수천 개인 반면 86개). 모든 범주 이름은 영어입니다. 범주는 단독으로 또는 최신 태그 모델과 함께 수행될 수 있습니다.

## <a name="the-86-category-concept"></a>86개 범주 개념

컴퓨터 비전은 다음 다이어그램의 86개 범주 목록을 사용하여 광범위하게 또는 구체적으로 이미지를 분류할 수 있습니다. 전체 분류를 텍스트 형식으로 보려면 [범주 분류](category-taxonomy.md)를 참조하세요.

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

| 이미지 | 범주 |
|-------|----------|
| ![가족으로 함께 포즈를 취한 4명의 사람](./Images/family_photo.png) | people_group |
| ![잔디밭에 앉아 있는 강아지](./Images/cute_dog.png) | animal_dog |
| ![석양이 보이는 산 바위에 서 있는 사람](./Images/mountain_vista.png) | outdoor_mountain |
| ![테이블 위에 쌓여있는 빵](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 설명](concept-describing-images.md)에 대한 개념을 알아봅니다.
