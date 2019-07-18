---
title: 이미지 설명 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 설명 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368387"
---
# <a name="describe-images-with-human-readable-language"></a>사람이 읽을 수 있는 언어를 사용하여 이미지를 설명합니다.

Computer Vision은 이미지를 분석하고 콘텐츠를 설명하는, 사람이 읽을 수 있는 문장을 생성할 수 있습니다. 알고리즘은 여러 시각적 기능을 기준으로 여러 설명을 실제로 반환하며, 각 설명에 신뢰도 점수가 제공됩니다. 최종 출력은 가장 높은 신뢰도부터 가장 낮은 신뢰도 순으로 정렬된 설명 목록입니다.

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
