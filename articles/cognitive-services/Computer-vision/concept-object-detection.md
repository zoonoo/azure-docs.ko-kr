---
title: 개체 검색-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 개체를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 89323e584b4020613fe3ff8411df50f2ab96f156
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582732"
---
# <a name="object-detection"></a>개체 감지

개체 검색은 [태깅](concept-tagging-images.md)과 유사하지만, API는 검색된 각 개체에 대해 경계 상자 좌표(픽셀 단위)를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업을 실행하면 해당 개체를 해당 좌표와 함께 이미지에 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 처리할 수 있습니다. 또한 이 기능을 통해 이미지에 동일한 태그의 다중 인스턴스가 있는 지 확인할 수 있습니다.

Detect API는 개체를 기반으로 한 태그 또는 이미지에서 식별되는 생물에 적용합니다. 이 시점에서 태깅에 사용되는 분류와 개체 검색에 사용되는 분류 간에 공식적인 관계는 없습니다. 개념 수준에서 Detect API는 개체 및 생물만 검색하지만 Tag API는 경계 상자로 지역화할 수 없는 “실내”와 같은 개념 용어를 포함할 수도 있습니다.

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

## <a name="next-steps"></a>다음 단계

[이미지 분류](concept-categorizing-images.md) 및 [이미지 설명](concept-describing-images.md)에 대한 개념을 알아봅니다.