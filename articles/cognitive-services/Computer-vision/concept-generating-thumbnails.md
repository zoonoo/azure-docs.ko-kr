---
title: 썸네일 생성 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지의 썸네일을 생성하는 데 관련된 개념입니다.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: ff16fa4d30fb60e6b1376027a40242af23be04d1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985350"
---
# <a name="generating-thumbnails"></a>썸네일 생성

썸네일은 전체 크기 이미지의 작은 표현입니다. 휴대폰, 태블릿, PC 등 장치가 다양해지면서 각기 다른 UX(사용자 환경) 레이아웃과 썸네일 크기가 필요하게 되었습니다. 이 Computer Vision 기능은 스마트 자르기를 사용하여 문제 해결을 지원합니다.

이미지를 업로드하면 Computer Vision은 고품질 썸네일 이미지를 생성한 다음, 이미지 내의 개체를 분석하여 ROI(*관심 영역*)를 식별합니다. 필요에 따라 ROI의 요구 사항에 맞게 이미지를 자를 수 있습니다. 요구 사항을 수용하기 위해 원래 이미지의 가로 세로 비율과 다른 가로 세로 비율을 사용하여 생성된 썸네일을 표시할 수 있습니다.

썸네일 알고리즘은 다음과 같이 작동합니다.

1. 이미지에서 방해가 되는 요소를 제거하고 주요 개체인 관심 영역을 식별합니다.
2. 식별된 관심 영역에 따라 이미지를 자릅니다.
3. 대상 썸네일 크기에 맞게 가로 세로 비율을 변경합니다.

생성된 썸네일은 다음 이미지에 나와 있는 대로 높이, 너비 및 스마트 자르기에 지정한 내용에 따라 크게 달라질 수 있습니다.

![썸네일](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>썸네일 생성 예제

다음 표에서는 예제 이미지로 Computer Vision에서 생성된 일반적인 썸네일을 보여줍니다. 썸네일은 스마트 자르기를 사용하여 50픽셀이라는 지정된 대상 높이 및 너비로 생성되었습니다.

| 이미지 | 미리 보기 |
|-------|-----------|
|![옥외 산](./Images/mountain_vista.png) | ![옥외 산 썸네일](./Images/mountain_vista_thumbnail.png) |
|![비전 분석 꽃](./Images/flower.png) | ![비전 분석 꽃 썸네일](./Images/flower_thumbnail.png) |
|![여성 지붕](./Images/woman_roof.png) | ![여성 지붕 썸네일](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>다음 단계

[이미지 태그 지정](concept-tagging-images.md) 및 [이미지 분류](concept-categorizing-images.md)에 대한 개념을 알아봅니다.