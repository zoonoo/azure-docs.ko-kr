---
title: 브랜드 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 브랜드/로고를 검색하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a5cf4a2b0f5e2e2c83e9fe6aa9237c943fa23ed9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864811"
---
# <a name="brand-detection"></a>브랜드 검색

브랜드 검색은 수천 개의 글로벌 로고에 대한 데이터베이스를 사용하여 이미지 또는 비디오에서 상용 브랜드를 식별하는 [개체 검색](concept-object-detection.md)의 특수 모드입니다. 예를 들어 이 기능을 사용하여 소셜 미디어에서 가장 인기 있거나 미디어 제품 배치에서 가장 일반적인 브랜드를 검색할 수 있습니다.

Computer Vision 서비스는 지정된 이미지에 브랜드 로고가 있는지 여부를 검색합니다. 이 경우 브랜드 이름, 신뢰도 점수 및 로고 주변의 경계 상자 좌표를 반환합니다.

기본 제공 로고 데이터베이스는 가전제품, 의류 등의 인기 있는 브랜드를 포함하고 있습니다. 찾고 있는 브랜드가 Computer Vision 서비스에서 검색되지 않는 경우 [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 서비스를 사용하여 사용자 고유의 로고 검색기를 만들고 학습하는 것이 더 좋을 수 있습니다.

## <a name="brand-detection-example"></a>브랜드 검색 예제

다음 JSON 응답에서는 예제 이미지의 브랜드를 검색할 때 Computer Vision에서 반환하는 내용을 보여 줍니다.

![Microsoft 레이블과 로고가 있는 회색 스웨터](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
브랜드 검색기는 경우에 따라 로고 이미지와 스타일이 적용된 브랜드 이름 모두를 별도의 두 로고로 선택합니다.

![Microsoft 레이블과 로고가 있는 빨간색 셔츠](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>API 사용
브랜드 검색 기능은 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API의 일부 입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. 전체 JSON 응답을 받으면 `"brands"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: 이미지 분석(.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [빠른 시작: 이미지 분석(REST API)](./quickstarts/csharp-analyze.md)