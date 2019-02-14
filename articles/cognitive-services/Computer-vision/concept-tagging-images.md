---
title: 이미지에 콘텐츠 태그 적용 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 태그 지정 기능과 관련된 개념에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f5013c83f3575e7e1fbaa6dff614e4679abf09b6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882236"
---
# <a name="applying-content-tags-to-images"></a>이미지에 콘텐츠 태그 적용

Computer Vision에서는 수천 개의 인식할 수 있는 개체, 생물, 경치 및 작업에 따라 태그를 반환합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 알려진 설정의 컨텍스트에서 태그의 의미를 명확히 설명하는 ‘힌트’를 제공합니다. 태그는 분류로 구성되지 않으며 상속 계층이 없습니다. 콘텐츠 태그 컬렉션은 완전한 문장으로 작성되고, 사람이 읽을 수 있는 언어로 표시되는 이미지 ‘설명’의 토대가 됩니다. 이 시점에서 이미지 설명에 대해 지원되는 언어는 영어뿐입니다.

이미지를 업로드하거나 이미지 URL을 지정한 후 Computer Vision의 알고리즘은 이미지에서 식별된 사물, 생물 및 동작을 기준으로 태그를 출력합니다. 태그 지정은 전면에 있는 사람과 같은 주 피사체로 제한되지 않고 설정(실내 또는 옥외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등도 포함합니다.

## <a name="image-tagging-example"></a>이미지 태그 지정 예제

다음 JSON 응답에서는 예제 이미지에서 검색된 시각적 기능의 태그를 지정할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![파란색 집 및 앞 마당](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>다음 단계

[이미지 분류](concept-categorizing-images.md) 및 [이미지 설명](concept-describing-images.md)에 대한 개념을 알아봅니다.
