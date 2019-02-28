---
title: 얼굴 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 얼굴 감지 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1056b8be113d56342aea8f83d5325737f7ecb93b
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308454"
---
# <a name="face-detection-with-computer-vision"></a>Computer Vision으로 얼굴 감지

Computer Vision은 이미지에서 사람 얼굴을 감지하고 감지된 각 얼굴에 대해 연령, 성별 및 사각형을 생성합니다. 

> [!NOTE]
> 이 기능은 Azure [Face](/azure/cognitive-services/face/) 서비스를 통해서도 제공됩니다. 얼굴 인식 및 자세 감지를 비롯한 더 자세한 얼굴 감지에 대해서는 이 대안을 참조하세요. 

## <a name="face-detection-examples"></a>얼굴 감지 예제

다음 예제는 사람 얼굴이 하나인 이미지에 대해 Computer Vision에서 반환된 JSON 응답을 보여줍니다.

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

다음 예제는 사람 얼굴이 여러 개인 이미지에 대해 반환된 JSON 응답을 보여줍니다.

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

[이미지 분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 참조 설명서를 참조하여 얼굴 감지 기능을 사용하는 방법에 대해 자세히 알아봅니다.
