---
title: 스마트하게 자른 썸네일 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지의 썸네일을 생성하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bcfaf250aa5ebf29f51fae22f87c14c29416107e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110465334"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Computer Vision을 사용하여 스마트하게 자른 썸네일 생성

썸네일은 이미지의 크기가 축소된 표현입니다. 썸네일은 더 경제적이고 레이아웃에 친숙한 방식으로 이미지 및 기타 데이터를 나타내는 데 사용됩니다. Computer Vision API는 이미지 크기 조정과 함께 스마트 자르기를 사용하여 지정된 이미지에 대한 직관적인 썸네일을 만듭니다.

Computer Vision 썸네일 생성 알고리즘은 다음과 같이 작동합니다.

1. 이미지에서 방해되는 요소를 제거하고 주 개체가 표시되는 _관심 영역_&mdash;이미지 영역을 식별합니다.
1. 식별된 _관심 영역_ 에 따라 이미지를 자릅니다.
1. 대상 썸네일 크기에 맞게 가로 세로 비율을 변경합니다.

## <a name="area-of-interest"></a>관심 영역

이미지를 업로드하는 경우 Computer Vision API는 이를 분석하여 *관심 영역* 을 확인합니다. 그런 다음, 이 영역을 사용하여 이미지를 자르는 방법을 확인할 수 있습니다. 그러나 자르기 작업은 가로 세로 비율이 지정된 경우 항상 원하는 비율과 일치해야 합니다.

대신 **areaOfInterest** API를 호출하여 이 동일한 *관심 영역* 의 원시 경계 상자 좌표를 구할 수 있습니다. 그런 다음, 이 정보를 사용하여 원하는 대로 원본 이미지를 수정할 수 있습니다.

## <a name="examples"></a>예제

생성된 썸네일은 다음 이미지에 나와 있는 대로 높이, 너비 및 스마트 자르기에 지정한 내용에 따라 크게 달라질 수 있습니다.

![산 이미지와 다양하게 자른 구성](./Images/thumbnail-demo.png)

다음 표에서는 예제 이미지로 Computer Vision에서 생성된 일반적인 썸네일을 보여줍니다. 썸네일은 스마트 자르기를 사용하여 50픽셀이라는 지정된 대상 높이 및 너비로 생성되었습니다.

| 이미지 | 미리 보기 |
|-------|-----------|
|![사람의 실루엣이 있는 해질녘의 야외 산](./Images/mountain_vista.png) | ![사람의 실루엣이 있는 해질녘의 야외 산 썸네일](./Images/mountain_vista_thumbnail.png) |
|![녹색 배경의 흰색 꽃](./Images/flower.png) | ![비전 분석 꽃 썸네일](./Images/flower_thumbnail.png) |
|![아파트 건물 옥상 위의 여자](./Images/woman_roof.png) | ![아파트 건물 옥상 위 여자의 썸네일](./Images/woman_roof_thumbnail.png) |

## <a name="use-the-api"></a>API 사용

썸네일 생성 기능은 [썸네일 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20c) 및 [관심 영역 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/b156d0f5e11e492d9f64418d) API를 통해 사용할 수 있습니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. 

* [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)
