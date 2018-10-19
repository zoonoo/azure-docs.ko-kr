---
title: 이미지 형식 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지 형식을 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 1a76106caed514f56e897203fba5215b7e93d4ff
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984829"
---
# <a name="detecting-image-types"></a>이미지 형식 검색

Computer Vision은 이미지가 클립 아트인지 또는 선 그리기인지 가능성을 평가하여 이미지의 콘텐츠 형식을 분석할 수 있습니다.

## <a name="detecting-clip-art"></a>클립 아트 검색

Computer Vision은 다음 표에 설명된 대로 이미지를 분석하고, 이미지가 클립 아트인 가능성을 0부터 3까지로 평가합니다.

| 값 | 의미 |
|-------|---------|
| 0 | 클립 아트 아님 |
| 1 | 모호함 |
| 2 | 보통 클립 아트 |
| 3 | 좋은 클립 아트 |

### <a name="clip-art-detection-examples"></a>클립 아트 검색 예제

다음 JSON 응답에서는 예제 이미지가 클립 아트일 가능성을 평가할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![비전 분석 치즈 클립 아트](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![비전 분석 주택 야드](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>선 그리기 검색

Computer Vision는 이미지를 분석하고 이미지가 선 그리기인지 여부를 나타내는 부울 값을 반환합니다.

### <a name="line-drawing-detection-examples"></a>선 그리기 검색 예제

다음 JSON 응답에서는 예제 이미지가 선 그리기인지 나타낼 때 Computer Vision이 반환하는 내용을 보여줍니다.

![비전 분석 사자 그리기](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![비전 분석 꽃](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 분류](concept-categorizing-images.md)에 대한 개념을 알아봅니다.