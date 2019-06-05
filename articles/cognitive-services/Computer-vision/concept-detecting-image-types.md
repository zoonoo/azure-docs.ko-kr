---
title: 이미지 유형 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 유형 감지 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 37cdac16a51a30bdaf1ba0266bab7fdd1f2990f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895450"
---
# <a name="detecting-image-types-with-computer-vision"></a>Computer Vision으로 이미지 유형 감지

[분석 이미지](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API를 사용하여, Computer Vision은 이미지가 클립 아트인지 또는 선 그리기인지 여부를 나타내는 이미지의 콘텐츠 형식을 분석할 수 있습니다.

## <a name="detecting-clip-art"></a>클립 아트 감지

Computer Vision은 다음 표에 설명된 대로 이미지를 분석하고, 이미지가 클립 아트인 가능성을 0부터 3까지로 평가합니다.

| 값 | 의미 |
|-------|---------|
| 0 | 클립 아트 아님 |
| 1 | 모호함 |
| 2 | 보통 클립 아트 |
| 3 | 좋은 클립 아트 |

### <a name="clip-art-detection-examples"></a>클립 아트 감지 예제

다음 JSON 응답에서는 예제 이미지가 클립 아트일 가능성을 평가할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![치즈 조각의 클립 아트 이미지](./Images/cheese_clipart.png)

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

![파란색 집 및 앞 마당](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>선 그리기 감지

Computer Vision는 이미지를 분석하고 이미지가 선 그리기인지 여부를 나타내는 부울 값을 반환합니다.

### <a name="line-drawing-detection-examples"></a>선 그리기 감지 예제

다음 JSON 응답에서는 예제 이미지가 선 그리기인지 나타낼 때 Computer Vision이 반환하는 내용을 보여줍니다.

![사자의 선 그리기 이미지](./Images/lion_drawing.png)

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

![녹색 배경의 흰색 꽃](./Images/flower.png)

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

[이미지 분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 참조 설명서를 참조하여 이미지 유형을 감지하는 방법에 대해 자세히 알아봅니다.
