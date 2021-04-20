---
title: 이미지 썸네일 크기 조정 및 자르기 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Search API의 일부 답변에는 Bing에서 제공하는 썸네일 이미지(크기 조정 및 자르기가 가능)에 대한 URL이 포함되어 있으며 쿼리 매개 변수가 포함될 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350486"
---
# <a name="resize-and-crop-thumbnail-images"></a>썸네일 이미지 크기 조정 및 자르기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Search API의 일부 답변에는 Bing에서 제공하는 썸네일 이미지(크기 조정 및 자르기가 가능)에 대한 URL이 포함되어 있으며 쿼리 매개 변수가 포함될 수 있습니다. 예를 들면 다음과 같습니다.

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

이러한 썸네일의 하위 집합을 표시하는 경우 나머지 이미지를 볼 수 있는 옵션을 제공합니다.

> [!NOTE]
> 썸네일 이미지 자르기와 크기 조정은 Bing Search API [사용 및 표시 요구 사항](use-display-requirements.md)에서 요구하는 대로 타사 권리를 존중하는 검색 시나리오를 제공해야 합니다.

## <a name="resize-a-thumbnail"></a>썸네일 크기 조정 

썸네일의 크기를 조정하려면 썸네일의 URL에 `w`(너비) 또는 `h`(높이) 쿼리 매개 변수만 지정하는 것이 좋습니다. 높이 또는 너비만 지정하면 Bing에서 이미지의 원래 가로 세로 비율을 유지할 수 있습니다. 너비와 높이를 픽셀 단위로 지정합니다. 

예를 들어 원래 썸네일이 480x620인 경우

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

크기를 줄이려면 `w` 매개 변수를 새 값(예: `336`)으로 설정하고 `h` 매개 변수를 제거합니다.

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

썸네일의 높이 또는 너비만 지정하면 이미지의 원래 가로 세로 비율이 유지됩니다. 두 매개 변수를 모두 지정했는데 가로 세로 비율이 유지되지 않는 경우 Bing은 이미지의 테두리에 흰색 안쪽 여백을 추가합니다.

예를 들어 480x359 이미지를 자르기 없이 200x200으로 조정하면 전체 너비에 이미지가 포함되지만 높이에는 이미지 맨 위와 맨 아래에 25픽셀의 흰색 안쪽 여백이 포함됩니다. 마찬가지로 이미지가 359x480인 경우에도 왼쪽 및 오른쪽 테두리에 흰색 안쪽 여백이 포함됩니다. 이미지를 자르면 흰색 패딩이 추가되지 않습니다.  

다음 그림은 썸네일 이미지의 원래 크기(480x300)를 보여 줍니다.  
  
![원래 가로 이미지](./media/resize-crop/bing-resize-crop-landscape.png)  
  
다음 그림은 200x200으로 조정된 이미지를 보여 줍니다. 가로 세로 비율이 유지되고 위쪽 및 아래쪽 테두리에 흰색 안쪽 여백이 추가됩니다(안쪽 여백을 보여 주기 위해 검은색 테두리가 포함됨).  
  
![조정된 가로 이미지](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

이미지의 원래 너비 및 높이보다 큰 차원을 지정하면 이미지의 왼쪽 및 위쪽 테두리에 흰색 안쪽 여백이 추가됩니다.  

## <a name="request-different-thumbnail-sizes"></a>다른 썸네일 크기 요청

다른 썸네일 크기를 요청하려면 썸네일의 URL에서 `id` 및 `pid` 매개 변수를 제외한 모든 쿼리 매개 변수를 제거합니다. 그런 다음 원하는 이미지 크기(픽셀)의 `&w`(너비) 또는 `&h`(높이) 쿼리 매개 변수 중 하나만 추가합니다. Bing은 이미지의 원래 가로 세로 비율을 유지합니다. 

위의 URL로 지정된 이미지의 너비를 165픽셀로 늘리려면 다음 URL을 사용합니다.

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

이미지의 원래 크기보다 큰 이미지를 요청하는 경우 Bing에서 필요에 따라 이미지 주위에 흰색 안쪽 여백을 추가합니다. 예를 들어 이미지의 원래 크기가 474x316이고 `&w`를 500으로 설정하는 경우 Bing은 500x333 이미지를 반환합니다. 이 이미지는 위쪽 및 아래쪽 가장자리를 따라 8.5픽셀의 흰색 안쪽 여백이 있고 왼쪽 및 오른쪽 가장자리에는 13픽셀의 안쪽 여백이 있습니다.

요청한 크기가 이미지의 원래 크기보다 큰 경우 Bing에서 흰색 안쪽 여백을 추가하지 않도록 하려면 `&p` 쿼리 매개 변수를 0으로 설정합니다. 예를 들어 위의 URL에 `&p=0` 매개 변수를 포함하는 경우 Bing은 500x333 이미지 대신 474x316 이미지를 반환합니다.

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

`&w` 및 `&h` 쿼리 매개 변수를 모두 지정하는 경우 Bing은 이미지의 가로 세로 비율을 유지하고 필요에 따라 흰색 안쪽 여백을 추가합니다. 예를 들어 이미지의 원래 크기가 474x316이고 너비 및 높이 매개 변수를 200x200(`&w=200&h=200`)으로 설정하는 경우 Bing은 위쪽 및 아래쪽에 33픽셀의 흰색 안쪽 여백이 포함된 이미지를 반환합니다. `&p` 쿼리 매개 변수를 포함하는 경우 Bing은 200x134 이미지를 반환합니다.

## <a name="crop-a-thumbnail"></a>썸네일 자르기 

이미지를 자르려면 `c`(자르기) 쿼리 매개 변수를 포함합니다. 다음과 같은 값을 사용할 수 있습니다.
  
- `4` &mdash; 블라인드 비율  
- `7` &mdash; 스마트 비율  

### <a name="smart-ratio-cropping"></a>스마트 비율 자르기

`c` 매개 변수를 `7`로 설정하여 스마트 비율 자르기를 요청하는 경우 Bing에서는 이미지의 가로 세로 비율을 유지하면서 관심 영역 중심에서 바깥쪽으로 이미지를 자릅니다. 관심 영역은 가장 중요한 부분이 포함된 것으로 Bing이 판단하는 이미지 영역입니다. 다음은 관심 영역 예제입니다.  
  
![관심 영역](./media/resize-crop/bing-resize-crop-regionofinterest.png)

이미지 크기를 조정하고 스마트 비율 자르기를 요청하는 경우 Bing은 가로 세로 비율을 유지하면서 이미지를 요청된 크기로 축소합니다. 그런 다음 크기가 조정된 차원에 따라 이미지를 자릅니다. 예를 들어 조정된 너비가 높이보다 작거나 같으면 Bing은 관심 영역 중심의 좌우에서 이미지를 자릅니다. 그렇지 않으면 Bing은 관심 영역 중심의 위아래에서 이미지를 자릅니다.  
  
 
다음은 스마트 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정하기 때문에 이미지의 아래쪽 부분이 잘립니다. 
  
![200x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
다음은 스마트 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정하기 때문에 이미지의 아래쪽 부분이 잘립니다. 
   
![200x100으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
다음은 스마트 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. Bing은 중앙에서 이미지를 측정하기 때문에 이미지의 왼쪽 및 오른쪽 부분이 잘립니다.
  
![100x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Bing이 이미지의 관심 영역을 확인할 수 없는 경우 서비스는 블라인드 비율 자르기를 사용합니다.  

### <a name="blind-ratio-cropping"></a>블라인드 비율 자르기

`c` 매개 변수를 `4`로 설정하여 블라인드 비율 자르기를 요청하는 경우 Bing은 다음 규칙을 사용하여 이미지를 자릅니다.  
  
- `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`인 경우 이미지를 왼쪽 위 모서리에서 측정하고 아래쪽에서 자릅니다.  
- `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`인 경우 이미지를 중심에서 측정하여 좌우를 자릅니다.  

다음은 225x300 세로 이미지를 보여 줍니다.  
  
![원래 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. 왼쪽 맨 위 모서리에서 이미지를 측정하여 이미지의 아래쪽 부분을 자릅니다.  
  
![200x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. 왼쪽 맨 위 모서리에서 이미지를 측정하여 이미지의 아래쪽 부분을 자릅니다.  
  
![200x100으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. 중심에서 이미지를 측정하여 이미지 좌우를 자릅니다.  
  
![100x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>다음 단계

* [Bing Search API란?](bing-api-comparison.md)
* [Bing Search API 사용 및 표시 요구 사항](use-display-requirements.md)