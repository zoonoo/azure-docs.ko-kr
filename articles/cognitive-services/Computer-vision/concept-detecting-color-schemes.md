---
title: 색 구성표 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 이미지에서 색 구성표를 감지하는 데 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998620"
---
# <a name="detect-color-schemes-in-images"></a>이미지에서 색 구성표 감지

Computer Vision은 이미지의 색을 분석하여 세 가지 특성(주요 전경색, 주요 배경색 및 전체 이미지의 주요 색 세트)을 제공합니다. 반환되는 색은 검정, 파랑, 갈색, 회색, 초록, 주황, 분홍, 자주, 청록, 흰색 및 노랑 세트에 속합니다. 

Computer Vision은 주요 색과 채도의 조합을 기반으로 하여 이미지에서 가장 선명한 색을 나타내는 강조 색도 추출합니다. 강조 색은 16진수 HTML 색 코드로 반환됩니다. 

또한 Computer Vision은 이미지가 흑백인지 여부를 나타내는 부울 값을 반환합니다.

## <a name="color-scheme-detection-examples"></a>색 구성표 감지 예제

다음 예제에서는 예제 이미지의 색 구성표를 검색할 때 Computer Vision에서 반환된 JSON 응답을 보여줍니다. 이 경우 예제 이미지는 흑백 이미지가 아니라 주요 전경색 및 배경색이 검은색이고 전체 이미지의 주요 색이 검은색과 흰색입니다.

![야외 Mountain 일몰, 개인의 실루엣을 사용 하 여](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>주요 색 예제

다음 표에서는 각 샘플 이미지에 대해 반환되는 전경색, 배경색 및 이미지 색을 보여 줍니다.

| 이미지 | 주요 색 |
|-------|-----------------|
|![녹색 배경의 흰색 꽃](./Images/flower.png)| 전경: 검정<br/>배경: 흰색<br/>색: 검은색, 흰색, 녹색|
![역을 달리는 열차](./Images/train_station.png) | 전경: 검정<br/>배경: 검정<br/>색: 검정 |

### <a name="accent-color-examples"></a>강조색 예제

 다음 표에서는 각 예제 이미지에 대해 반환되는 강조 색을 16진수 HTML 색 값으로 보여 줍니다.

| 이미지 | 강조 색 |
|-------|--------------|
|![석양이 보이는 산 바위에 서 있는 사람](./Images/mountain_vista.png) | #BB6D10 |
|![녹색 배경의 흰색 꽃](./Images/flower.png) | #C6A205 |
|![역을 달리는 열차](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>흑백 감지 예제

다음 표에서는 샘플 이미지의 검은색과 흰색에 대한 Computer Vision의 평가를 보여 줍니다.

| 이미지 | 흑백? |
|-------|----------------|
|![맨해튼 건물의 흑백 사진](./Images/bw_buildings.png) | true |
|![파란색 집 및 앞 마당](./Images/house_yard.png) | false |

## <a name="next-steps"></a>다음 단계

[이미지 유형 감지](concept-detecting-image-types.md)에 대한 개념을 알아봅니다.
