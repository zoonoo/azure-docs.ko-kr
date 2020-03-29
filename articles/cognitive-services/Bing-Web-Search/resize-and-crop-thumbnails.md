---
title: 이미지 썸네일 크기 조정 및 자르기 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing 검색 API의 일부 답변에는 Bing에서 제공하는 축소판 이미지에 대한 URL이 포함되며, 이 이미지의 크기를 조정하고 자르면 쿼리 매개 변수가 포함될 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110629"
---
# <a name="resize-and-crop-thumbnail-images"></a>썸네일 이미지 크기 조정 및 자르기

Bing 검색 API의 일부 답변에는 Bing에서 제공하는 축소판 이미지에 대한 URL이 포함되며, 이 이미지의 크기를 조정하고 자르면 쿼리 매개 변수가 포함될 수 있습니다. 예를 들어:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

이러한 축소판 그림의 하위 집합을 표시하는 경우 나머지 이미지를 볼 수 있는 옵션을 제공합니다.

> [!NOTE]
> 축소판 이미지 자르기 및 크기 조정은 Bing Search API [사용 및 표시 요구 사항에](use-display-requirements.md)따라 타사 권한을 준수하는 검색 시나리오를 제공해야 합니다.

## <a name="resize-a-thumbnail"></a>축소판 크기 조정 

축소판 그림의 크기를 조정하려면 썸네일 URL에서 (너비) 또는 `w` `h` (높이) 쿼리 매개변수를 하나만 지정하는 것이 좋습니다. 높이 나 너비만 지정하면 Bing이 이미지의 원래 측면을 유지할 수 있습니다. 너비와 높이를 픽셀 단위로 지정합니다. 

예를 들어 원래 썸네일이 480x620인 경우:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

크기를 줄이고 매개 변수를 `w` 새 값으로 설정하고 매개 `336`변수를 `h` 제거하려고 합니다.

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

썸네일의 높이 나 너비만 지정하면 이미지의 원래 가로 세로 비율이 유지됩니다. 두 매개 변수를 모두 지정하고 가로 세로 비율이 유지되지 않으면 Bing은 이미지 테두리에 흰색 패딩을 추가합니다.

예를 들어 자르지 않고 480x359 이미지의 크기를 200x200으로 조정하면 전체 너비에 이미지가 포함되지만 높이에는 이미지의 위쪽과 아래쪽에 25픽셀의 흰색 패딩이 포함됩니다. 이미지가 359x480이면 왼쪽 및 오른쪽 테두리에 흰색 패딩이 포함됩니다. 이미지를 자르면 흰색 패딩이 추가되지 않습니다.  

다음 그림은 썸네일 이미지의 원래 크기(480x300)를 보여 줍니다.  
  
![원래 가로 이미지](./media/resize-crop/bing-resize-crop-landscape.png)  
  
다음 그림은 200x200으로 조정된 이미지를 보여 줍니다. 가로 세로 비율이 유지되고 위쪽 및 아래쪽 테두리가 흰색으로 패딩처리됩니다(여기에 검은색 테두리가 포함되어 패딩을 표시합니다).  
  
![조정된 가로 이미지](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

이미지의 원래 너비와 높이보다 큰 치수를 지정하면 Bing은 왼쪽 및 위쪽 테두리에 흰색 패딩을 추가합니다.  

## <a name="request-different-thumbnail-sizes"></a>다른 축소판 크기 요청

다른 썸네일 이미지 크기를 요청하려면 및 `id` `pid` 매개변수를 제외한 모든 검색대 매개변수를 썸네일 URL에서 제거합니다. 그런 다음 `&w` 원하는 이미지 `&h` 크기를 픽셀 단위로 (너비) 또는 (높이) 쿼리 매개 변수를 추가하지만 둘 다 추가하지는 않습니다. Bing은 이미지의 원래 가로 세로 비율을 유지합니다. 

위의 URL에서 지정한 이미지의 너비를 165픽셀로 높이려면 다음 URL을 사용합니다.

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

이미지의 원래 크기보다 큰 이미지를 요청하는 경우 Bing은 필요에 따라 이미지 주위에 흰색 패딩을 추가합니다. 예를 들어 이미지의 원래 크기가 474x316이고 `&w` 500으로 설정하면 Bing은 500x333 이미지를 반환합니다. 이 이미지는 위쪽 및 아래쪽 가장자리를 따라 8.5 픽셀의 흰색 패딩과 왼쪽 및 오른쪽 가장자리에 13 픽셀의 패딩을 갖습니다.

Bing이 요청된 크기가 이미지의 원래 크기보다 큰 경우 흰색 패딩을 추가하지 못하도록 하려면 쿼리 매개 변수를 `&p` 0으로 설정합니다. 예를 들어 위의 URL에 `&p=0` 매개 변수를 포함하는 경우 Bing은 500x333 이미지 대신 474x316 이미지를 반환합니다.

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

매개 변수와 `&w` `&h` 쿼리를 모두 지정하면 Bing은 이미지의 가로 세로 비율을 유지하고 필요에 따라 흰색 패딩을 추가합니다. 예를 들어 이미지의 원래 크기가 474x316이고 너비 및 높이 매개 변수를 200x200(200x200)으로`&w=200&h=200`설정한 경우 Bing은 위쪽과 아래쪽에 33픽셀의 흰색 패딩이 포함된 이미지를 반환합니다. 쿼리 매개 `&p` 변수를 포함하는 경우 Bing은 200x134 이미지를 반환합니다.

## <a name="crop-a-thumbnail"></a>미리보기 이미지 자르기 

이미지를 자르려면 (자르기) 쿼리 매개 변수를 `c` 포함합니다. 다음과 같은 값을 사용할 수 있습니다.
  
- `4`&mdash; 블라인드 비율  
- `7`&mdash; 스마트 비율  

### <a name="smart-ratio-cropping"></a>스마트 비율 자르기

스마트 비율 자르기를 요청하는 경우(매개 `c` `7`변수를 설정하여) Bing은 이미지의 가로 세로 비율을 유지하면서 관심 영역의 중심에서 바깥쪽으로 이미지를 자작합니다. 관심 영역은 가장 중요한 부분이 포함된 것으로 Bing이 판단하는 이미지 영역입니다. 다음은 관심 영역 예제입니다.  
  
![관심 영역](./media/resize-crop/bing-resize-crop-regionofinterest.png)

이미지 크기를 조정하고 스마트 비율 자르기를 요청하는 경우 Bing은 가로 세로 비율을 유지하면서 이미지를 요청된 크기로 줄입니다. 그런 다음 Bing은 크기 조정된 치수를 기반으로 이미지를 자작합니다. 예를 들어 크기 조정된 너비가 높이보다 작거나 같으면 Bing은 관심 영역의 중심의 왼쪽과 오른쪽으로 이미지를 자지합니다. 그렇지 않으면 Bing은 관심 영역의 중심의 위쪽과 아래쪽으로 자르게 됩니다.  
  
 
다음은 스마트 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정하므로 이미지의 아래쪽 부분이 자른 다. 
  
![200x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
다음은 스마트 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정하므로 이미지의 아래쪽 부분이 자른 다. 
   
![200x100으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
다음은 스마트 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. Bing은 이미지를 중앙에서 측정하므로 이미지의 왼쪽과 오른쪽 부분이 자른다.
  
![100x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Bing이 이미지의 관심 영역을 확인할 수 없는 경우 서비스는 블라인드 비율 자르기(Blind Ratio) 자르기(Blind Ratio)를 사용합니다.  

### <a name="blind-ratio-cropping"></a>블라인드 비율 자르기

블라인드 비율 자르기를 요청하는 경우(매개 `c` `4`변수를 설정하여) Bing은 다음 규칙을 사용하여 이미지를 자르는 것입니다.  
  
- 이미지가 `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`왼쪽 위 모서리에서 측정되고 아래쪽에서 자른 경우  
- 이미지가 `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`가운데에서 측정되고 왼쪽과 오른쪽으로 자른 경우.  

다음은 225x300 세로 이미지를 보여 줍니다.  
  
![원래 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. 이미지는 왼쪽 위 모서리에서 측정되어 이미지의 아래쪽 부분이 자른다.  
  
![200x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. 이미지는 왼쪽 위 모서리에서 측정되어 이미지의 아래쪽 부분이 자른다.  
  
![200x100으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. 중심에서 이미지를 측정하여 이미지 좌우를 자릅니다.  
  
![100x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>다음 단계

* [Bing Search API란?](bing-api-comparison.md)
* [Bing Search API 사용 및 표시 요구 사항](use-display-requirements.md)
