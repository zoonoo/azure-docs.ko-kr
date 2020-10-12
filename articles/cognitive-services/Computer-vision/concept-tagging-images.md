---
title: 콘텐츠 태그-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 태그 지정 기능과 관련된 개념에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244514"
---
# <a name="applying-content-tags-to-images"></a>이미지에 콘텐츠 태그 적용

Computer Vision에서는 수천 개의 인식할 수 있는 개체, 생물, 경치 및 작업에 따라 태그를 반환합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 알려진 설정의 컨텍스트에서 태그의 의미를 명확히 설명하는 ‘힌트’를 제공합니다. 태그는 분류로 구성되지 않으며 상속 계층 구조가 존재하지 않습니다. 콘텐츠 태그 컬렉션은 완전한 문장으로 작성되고, 사람이 읽을 수 있는 언어로 표시되는 이미지 ‘설명’의 토대가 됩니다. 이 시점에서 이미지 설명에 대해 지원되는 언어는 영어뿐입니다.

이미지를 업로드하거나 이미지 URL을 지정하면 Computer Vision 알고리즘이 이미지에서 식별된 사물, 생물 및 동작을 기준으로 태그를 출력합니다. 태그 지정은 전면에 있는 사람과 같은 주 피사체로 제한되지 않고 설정(실내 또는 옥외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등도 포함합니다.

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

## <a name="use-the-api"></a>API 사용

태깅 기능은 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. `Tags` **Visualfeatures** 쿼리 매개 변수에를 포함 합니다. 그런 다음 전체 JSON 응답을 가져오는 경우 섹션의 내용에 대 한 문자열을 구문 분석 하면 됩니다 `"tags"` .

* [빠른 시작: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [빠른 시작: 이미지 분석 (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>다음 단계

이미지 [분류](concept-categorizing-images.md) 및 [이미지 설명](concept-describing-images.md)의 관련 개념에 대해 알아봅니다.
