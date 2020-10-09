---
title: 이미지 썸네일 크기 조정 및 자르기 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Search API의 일부 답변에는 Bing에서 제공 하는 미리 보기 이미지에 대 한 Url이 포함 되어 있습니다 .이 Url은 크기 조정 및 자르기와 쿼리 매개 변수를 포함할 수 있습니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74110629"
---
# <a name="resize-and-crop-thumbnail-images"></a>썸네일 이미지 크기 조정 및 자르기

Bing Search API의 일부 답변에는 Bing에서 제공 하는 미리 보기 이미지에 대 한 Url이 포함 되어 있습니다 .이 Url은 크기 조정 및 자르기와 쿼리 매개 변수를 포함할 수 있습니다. 예를 들면 다음과 같습니다.

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

이러한 미리 보기의 하위 집합을 표시 하는 경우 나머지 이미지를 볼 수 있는 옵션을 제공 합니다.

> [!NOTE]
> 축소판 이미지를 자르고 크기를 조정 하는 것은 Bing Search API [사용 및 표시 요구 사항에서 요구](use-display-requirements.md)하는 대로 타사 권리를 주는 검색 시나리오를 제공 해야 합니다.

## <a name="resize-a-thumbnail"></a>축소판 그림 크기 조정 

미리 보기의 크기를 조정 하기 위해 Bing에서는 `w` `h` 미리 보기의 URL에 (width) 또는 (height) 쿼리 매개 변수만 지정 하는 것이 좋습니다. 높이나 너비만 지정 하면 이미지의 원래 측면을 유지 관리할 수 있습니다. 너비와 높이를 픽셀 단위로 지정합니다. 

예를 들어 원래 축소판 그림이 480x620 인 경우:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

크기를 줄이려면 `w` 매개 변수를 새 값 (예:)으로 설정 하 `336` 고 매개 변수를 제거 합니다 `h`  .

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

축소판 그림의 높이나 너비만 지정 하면 이미지의 원래 가로 세로 비율이 유지 됩니다. 두 매개 변수를 모두 지정 하 고 가로 세로 비율이 유지 되지 않는 경우 Bing은 이미지의 테두리에 흰색 안쪽 여백을 추가 합니다.

예를 들어, 480x359 이미지를 자르기 없이 200 x 200로 크기를 조정 하는 경우 전체 너비는 이미지를 포함 하지만 높이는 이미지의 위쪽과 아래쪽에 25 픽셀의 흰색 채움 문자를 포함 합니다. 이미지가 359x480 경우 왼쪽 및 오른쪽 테두리에는 흰색 여백이 포함 됩니다. 이미지를 자르면 흰색 패딩이 추가되지 않습니다.  

다음 그림은 썸네일 이미지의 원래 크기(480x300)를 보여 줍니다.  
  
![원래 가로 이미지](./media/resize-crop/bing-resize-crop-landscape.png)  
  
다음 그림은 200x200으로 조정된 이미지를 보여 줍니다. 가로 세로 비율이 유지 되 고 위쪽 및 아래쪽 테두리가 흰색으로 채워집니다 (안쪽 여백을 표시 하기 위해 검은색 테두리가 포함 됨).  
  
![조정된 가로 이미지](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

이미지의 원래 너비와 높이 보다 큰 크기를 지정 하는 경우 Bing은 왼쪽 및 위쪽 테두리에 흰색 안쪽 여백을 추가 합니다.  

## <a name="request-different-thumbnail-sizes"></a>다른 축소판 그림 크기 요청

다른 축소판 이미지 크기를 요청 하려면 및 매개 변수를 제외 하 고 미리 보기의 URL에서 모든 쿼리 매개 변수를 제거 `id` `pid` 합니다. 그런 다음 `&w` 원하는 이미지 크기 (픽셀)를 포함 하는 (너비) 또는 `&h` (height) 쿼리 매개 변수 중 하나만 추가 합니다. Bing은 이미지의 원래의 가로 세로 비율을 유지 합니다. 

위의 URL로 지정 된 이미지의 너비를 165 픽셀로 늘리려면 다음 URL을 사용 합니다.

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

이미지의 원래 크기 보다 큰 이미지를 요청 하는 경우 Bing에서 필요에 따라 이미지 주위에 흰색 여백을 추가 합니다. 예를 들어 이미지의 원래 크기가 474x316이 고를 500로 설정 하는 경우 `&w` Bing은 500x333 이미지를 반환 합니다. 이 이미지는 위쪽 및 아래쪽 가장자리를 따라 8.5 픽셀의 흰색 안쪽 여백이 있고 왼쪽 및 오른쪽 가장자리에는 13 픽셀의 안쪽 여백이 있습니다.

요청한 크기가 이미지의 원래 크기 보다 큰 경우 Bing에서 흰색 안쪽 여백을 추가 하지 않도록 하려면 `&p` 쿼리 매개 변수를 0으로 설정 합니다. 예를 들어 `&p=0` 위의 URL에 매개 변수를 포함 하는 경우 Bing은 500x333 이미지 대신 474x316 이미지를 반환 합니다.

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

`&w`및 `&h` 쿼리 매개 변수를 모두 지정 하는 경우 Bing은 이미지의 가로 세로 비율을 유지 하 고 필요에 따라 흰색 안쪽 여백을 추가 합니다. 예를 들어 이미지의 원래 크기가 474x316이 고 width 및 height 매개 변수를 200 x 200 ()로 설정 하는 경우 `&w=200&h=200` Bing은 위쪽 및 아래쪽에 33 픽셀의 흰색 채움 문자가 포함 된 이미지를 반환 합니다. 쿼리 매개 변수를 포함 하는 경우 `&p` Bing은 200x134 이미지를 반환 합니다.

## <a name="crop-a-thumbnail"></a>축소판 그림 자르기 

이미지를 자르려면 `c` (자르기) 쿼리 매개 변수를 포함 합니다. 다음과 같은 값을 사용할 수 있습니다.
  
- `4`&mdash;블라인드 비율  
- `7`&mdash;스마트 비율  

### <a name="smart-ratio-cropping"></a>스마트 비율 자르기

매개 변수를로 설정 하 여 스마트 비율 자르기를 요청 하는 경우 `c` `7` Bing에서는 이미지의 가로 세로 비율을 유지 하면서 관심 영역 중심의 이미지를 바깥쪽으로 자릅니다. 관심 영역은 가장 중요한 부분이 포함된 것으로 Bing이 판단하는 이미지 영역입니다. 다음은 관심 영역 예제입니다.  
  
![관심 영역](./media/resize-crop/bing-resize-crop-regionofinterest.png)

이미지 크기를 조정 하 고 스마트 비율 자르기를 요청 하는 경우 Bing은 가로 세로 비율을 유지 하면서 이미지를 요청 된 크기로 줄입니다. 그러면 Bing은 크기 조정 된 차원에 따라 이미지를 자릅니다. 예를 들어 크기가 조정 된 너비가 높이 보다 작거나 같으면 Bing은 원하는 영역 중앙의 왼쪽과 오른쪽에 이미지를 자릅니다. 그렇지 않으면 Bing은 관심 영역 중심의 위쪽 및 아래쪽으로 자릅니다.  
  
 
다음은 스마트 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정 하기 때문에 이미지의 아래쪽 부분이 잘립니다. 
  
![200x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
다음은 스마트 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. Bing은 왼쪽 위 모서리에서 이미지를 측정 하기 때문에 이미지의 아래쪽 부분이 잘립니다. 
   
![200x100으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
다음은 스마트 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. Bing은 중앙에서 이미지를 측정 하기 때문에 이미지의 왼쪽과 오른쪽 부분이 잘립니다.
  
![100x200으로 잘린 가로 이미지](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Bing에서 원하는 이미지의 영역을 확인할 수 없는 경우에는이 서비스에서 블라인드 비율 자르기를 사용 합니다.  

### <a name="blind-ratio-cropping"></a>블라인드 비율 자르기

매개 변수를로 설정 하 여 블라인드 비율 자르기를 요청 하는 경우 `c` `4` Bing은 다음 규칙을 사용 하 여 이미지를 자릅니다.  
  
- 인 경우 `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` 이미지를 왼쪽 위 모서리에서 측정 하 고 아래쪽에서 자릅니다.  
- 이면 `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` 이미지는 가운데에서 측정 되 고 왼쪽과 오른쪽으로 잘립니다.  

다음은 225x300 세로 이미지를 보여 줍니다.  
  
![원래 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. 왼쪽 위 모퉁이에서 이미지를 측정 하 여 이미지의 아래쪽 부분을 자릅니다.  
  
![200x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. 왼쪽 위 모퉁이에서 이미지를 측정 하 여 이미지의 아래쪽 부분을 자릅니다.  
  
![200x100으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
다음은 블라인드 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. 중심에서 이미지를 측정하여 이미지 좌우를 자릅니다.  
  
![100x200으로 잘린 해바라기 이미지](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>다음 단계

* [Bing Search API란?](bing-api-comparison.md)
* [Bing Search API 사용 및 표시 요구 사항](use-display-requirements.md)
