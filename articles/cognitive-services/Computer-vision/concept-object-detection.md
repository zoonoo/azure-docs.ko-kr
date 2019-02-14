---
title: 개체 검색-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 개체를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 93ce86a438fca47100a34da2524515b46bcad574
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567165"
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

## <a name="limitations"></a>제한 사항

거짓 부정(누락된 개체) 및 제한된 세부 정보의 영향을 방지하거나 완화할 수 있도록 개체 검색 기능의 제한 사항에 유의해야 합니다.
* 일반적으로 개체는 매우 작으면 검색되지 않습니다(이미지의 5% 미만).
* 일반적으로 개체는 매우 긴밀하게 배열되어 있으면 검색되지 않습니다(예: 접시 더미).
* 개체는 브랜드 또는 제품 이름으로 구분되지 않습니다(예: 상점 선반에 있는 다양한 유형의 탄산 음료). 그러나 [브랜드 검색](concept-brand-detection.md) 기능을 사용하여 이미지에서 브랜드 정보를 가져올 수 있습니다.

## <a name="use-the-api"></a>API 사용
개체 검색 기능은 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. 전체 JSON 응답을 받으면 `"objects"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: 이미지 분석(.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [빠른 시작: 이미지 분석(REST API)](./quickstarts/csharp-analyze.md)