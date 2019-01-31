---
title: 이미지 설명 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 설명 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 933c4e4a6731a492a5aa461de7c782af422fb6fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162704"
---
# <a name="describe-images-with-human-readable-language"></a>사람이 읽을 수 있는 언어를 사용하여 이미지를 설명합니다.

Computer Vision의 알고리즘은 이미지의 콘텐츠를 분석합니다. 이 분석은 완전한 문장의, 사람이 읽을 수 있는 언어로 표시되는 ‘설명’의 토대가 됩니다. 설명은 이미지에서 찾은 내용을 요약합니다. Computer Vision의 알고리즘은 이미지에서 식별된 시각적 기능에 따라 다양한 설명을 생성합니다. 설명이 각각 평가되고 신뢰도 점수가 생성됩니다. 그런 다음, 가장 높은 신뢰도 점수부터 가장 낮은 점수 순으로 정렬된 목록이 반환됩니다.

## <a name="image-description-example"></a>이미지 설명 예제

다음 JSON 응답에서는 해당 시각적 기능에 따라 예제 이미지를 설명할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![맨해튼 건물의 흑백 사진](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 분류](concept-categorizing-images.md)에 대한 개념을 알아봅니다.
