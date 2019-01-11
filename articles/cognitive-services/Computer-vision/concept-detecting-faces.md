---
title: 얼굴 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 얼굴 감지 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0c6485bff4ad11aab37139cd2aa2d3660bddac0e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580541"
---
# <a name="face-detection-with-computer-vision"></a>Computer Vision으로 얼굴 감지

Computer Vision은 사진에서 사람 얼굴을 감지하고 감지된 각 얼굴에 대해 연령, 성별 및 사각형을 생성합니다. Computer Vision은 [Face](/azure/cognitive-services/face/)에서 찾을 수 있는 기능의 하위 집합을 제공하며, 얼굴 식별 및 자세 감지와 같은 더 자세한 분석을 위해 Face 서비스를 사용할 수 있습니다.  

## <a name="face-detection-examples"></a>얼굴 감지 예제

첫 번째 예제는 하나의 사람 얼굴을 포함하는 이미지에 대해 Computer Vision에서 반환된 JSON 응답을 보여줍니다.

![비전 분석 여성 지붕 얼굴](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

두 번째 예제는 여러 사람 얼굴을 포함하는 이미지에 대해 반환된 JSON 응답을 보여줍니다.

![비전 분석 가족 사진 얼굴](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>다음 단계

[도메인별 콘텐츠 검색](concept-detecting-domain-content.md)에 대한 개념을 알아봅니다.