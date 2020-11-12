---
title: 개체 검색-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API 사용 및 제한의 개체 검색 기능과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1dfd1942c0c83433dab3c2091be29e62fbc05706
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540742"
---
# <a name="detect-common-objects-in-images"></a>이미지에서 공통 개체 검색

개체 검색은 [태깅](concept-tagging-images.md)과 유사하지만, API는 검색된 각 개체에 대해 경계 상자 좌표(픽셀 단위)를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업을 실행하면 해당 개체를 해당 좌표와 함께 이미지에 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 처리할 수 있습니다. 또한 이미지에 동일한 태그의 인스턴스가 여러 개 있는지 여부도 확인할 수 있습니다.

Detect API는 개체를 기반으로 한 태그 또는 이미지에서 식별되는 생물에 적용합니다. 현재 태깅 분류와 개체 검색 분류 간에는 공식적 관계가 없습니다. 개념 수준에서 검색 API는 개체와 살아있는 사물만 찾지만 태그 API에는 경계 상자를 사용 하 여 지역화할 수 없는 "실내"와 같은 상황별 용어가 포함 될 수도 있습니다.

## <a name="object-detection-example"></a>개체 검색 예제

다음 JSON 응답에서는 예제 이미지의 개체를 검색할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![주방에서 Microsoft Surface 디바이스를 사용하는 여자](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>제한 사항

거짓 부정 (누락 된 개체)의 영향 및 제한 된 세부 사항을 방지 하거나 완화할 수 있도록 개체 검색의 제한 사항을 확인 하는 것이 중요 합니다.

* 개체는 일반적으로 크기가 작은 경우 (이미지의 5% 미만) 검색 되지 않습니다.
* 개체는 서로 긴밀 하 게 정렬 된 경우 (예: 판의 스택) 일반적으로 검색 되지 않습니다.
* 개체는 브랜드 또는 제품 이름으로 구분되지 않습니다(예: 상점 선반에 있는 다양한 유형의 탄산 음료). 그러나 [브랜드 검색](concept-brand-detection.md) 기능을 사용하여 이미지에서 브랜드 정보를 가져올 수 있습니다.

## <a name="use-the-api"></a>API 사용

개체 검색 기능은 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. `Objects` **Visualfeatures** 쿼리 매개 변수에를 포함 합니다. 그런 다음 전체 JSON 응답을 가져오는 경우 섹션의 내용에 대 한 문자열을 구문 분석 하면 됩니다 `"objects"` .

* [빠른 시작: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [빠른 시작: 이미지 분석 (REST API)](./quickstarts/csharp-analyze.md)