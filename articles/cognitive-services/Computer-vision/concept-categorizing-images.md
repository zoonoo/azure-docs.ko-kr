---
title: 이미지 분류-Computer Vision
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
ms.openlocfilehash: 43359c47b4d2c1b8895938271c74dab03f7731f7
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541779"
---
# <a name="categorize-images-by-subject-matter"></a>주제별 이미지 분류

Computer Vision은 태그 지정 및 설명 외에도 이미지에서 감지된 분류 기반 범주를 반환합니다. 태그와 달리, 범주는 부모/자식 계층으로 구성되고 개수가 더 적습니다(태그는 수천 개인 반면 86개). 모든 범주 이름은 영어입니다. 범주는 단독으로 또는 최신 태그 모델과 함께 수행될 수 있습니다.

## <a name="the-86-category-concept"></a>86개 범주 개념

컴퓨터 비전은 다음 다이어그램에서 86 범주의 목록을 사용 하 여 이미지를 광범위 하거나 구체적으로 분류할 수 있습니다. 전체 분류를 텍스트 형식으로 보려면 [범주 분류](category-taxonomy.md)를 참조하세요.

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

## <a name="use-the-api"></a>API 사용

분류 기능은 [분석 이미지](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. `Categories` **Visualfeatures** 쿼리 매개 변수에를 포함 합니다. 그런 다음 전체 JSON 응답을 가져오는 경우 섹션의 내용에 대 한 문자열을 구문 분석 하면 됩니다 `"categories"` .

* [빠른 시작: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [빠른 시작: 이미지 분석 (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>다음 단계

[이미지에 태그](concept-tagging-images.md) 를 지정 하 고 [이미지를 설명](concept-describing-images.md)하는 관련 개념을 알아봅니다.
