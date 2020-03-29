---
title: 이미지 설명 - 컴퓨터 비전
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
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244718"
---
# <a name="describe-images-with-human-readable-language"></a>사람이 읽을 수 있는 언어를 사용하여 이미지를 설명합니다.

Computer Vision은 이미지를 분석하고 콘텐츠를 설명하는, 사람이 읽을 수 있는 문장을 생성할 수 있습니다. 알고리즘은 실제로 서로 다른 시각적 기능을 기반으로 여러 설명을 반환하며 각 설명에는 신뢰도 점수가 부여됩니다. 최종 출력은 가장 높은 신뢰도부터 가장 낮은 신뢰도 순으로 정렬된 설명 목록입니다.

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

## <a name="use-the-api"></a>API 사용

이미지 설명 기능은 이미지 [분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. `Description` **visual기능** 쿼리 매개 변수에 포함합니다. 그런 다음 전체 JSON 응답을 받으면 `"description"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: 컴퓨터 비전 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [빠른 시작: 이미지 분석(REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>다음 단계

이미지 에 태그를 [지정하고](concept-tagging-images.md) [이미지를 분류하는](concept-categorizing-images.md)관련 개념에 대해 알아봅니다.
