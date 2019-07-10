---
title: 썸네일 생성 - Computer Vision
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
ms.openlocfilehash: 8bbc86f5c6fe0f30968a1ba5bd5fa28160ef6963
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372897"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Computer Vision을 사용하여 스마트하게 자른 썸네일 생성

썸네일은 이미지의 크기가 축소된 표현입니다. 썸네일은 더 경제적이고 레이아웃에 친숙한 방식으로 이미지 및 기타 데이터를 나타내는 데 사용됩니다. Computer Vision API는 이미지 크기 조정과 함께 스마트 자르기를 사용하여 지정된 이미지에 대한 직관적인 썸네일을 만듭니다.

Computer Vision 썸네일 생성 알고리즘은 다음과 같이 작동합니다.

1. 이미지에서 방해되는 요소를 제거하고 주 개체가 표시되는 _관심 영역_&mdash;이미지 영역을 식별합니다.
1. 식별된 _관심 영역_에 따라 이미지를 자릅니다.
1. 대상 썸네일 크기에 맞게 가로 세로 비율을 변경합니다.

## <a name="area-of-interest"></a>관심 영역

이미지를 업로드하는 경우 Computer Vision API는 이를 분석하여 *관심 영역*을 확인합니다. 그런 다음, 이 영역을 사용하여 이미지를 자르는 방법을 확인할 수 있습니다. 그러나 자르기 작업은 가로 세로 비율이 지정된 경우 항상 원하는 비율과 일치해야 합니다.

대신 **areaOfInterest** API를 호출하여 이 동일한 *관심 영역*의 원시 경계 상자 좌표를 구할 수 있습니다. 그런 다음, 이 정보를 사용하여 원하는 대로 원본 이미지를 수정할 수 있습니다.

## <a name="examples"></a>예

생성된 썸네일은 다음 이미지에 나와 있는 대로 높이, 너비 및 스마트 자르기에 지정한 내용에 따라 크게 달라질 수 있습니다.

![다양 한 자르기 구성 옆에 있는 mountain 이미지를](./Images/thumbnail-demo.png)

다음 표에서는 예제 이미지로 Computer Vision에서 생성된 일반적인 썸네일을 보여줍니다. 썸네일은 스마트 자르기를 사용하여 50픽셀이라는 지정된 대상 높이 및 너비로 생성되었습니다.

| 이미지 | 미리 보기 |
|-------|-----------|
|![야외 Mountain 일몰, 개인의 실루엣을 사용 하 여](./Images/mountain_vista.png) | ![개인의 실루엣을 사용 하 여 일몰에 야외 Mountain의 미리 보기](./Images/mountain_vista_thumbnail.png) |
|![녹색 배경의 흰색 꽃](./Images/flower.png) | ![비전 분석 꽃 썸네일](./Images/flower_thumbnail.png) |
|![아파트 건물 옥상 위의 여자](./Images/woman_roof.png) | ![아파트 건물의 지붕 남자의 미리 보기](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 분류](concept-categorizing-images.md)에 대해 알아봅니다.
