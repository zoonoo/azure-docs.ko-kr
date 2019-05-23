---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124715"
---
일부 Bing 응답에는 Bing에서 제공하는 썸네일 이미지가 포함됩니다. 썸네일 이미지 크기를 조정하고 자를 수 있습니다. 

> [!NOTE]
> Bing Search API 사용 및 표시 요구 사항에 명시된 대로 썸네일의 크기 및 자르기가 검색 시나리오를 제공하고 제3자 권리를 존중해야 합니다.


이미지의 크기를 조정 하려면 w (너비) 쿼리를 포함 합니다. 매개 변수, h (높이) 쿼리 매개 변수 또는 썸네일 URL에 모두 있습니다. 너비와 높이를 픽셀 단위로 지정합니다. 예를 들면 다음과 같습니다.  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
만 너비 또는 높이 쿼리 매개 변수만 지정 하면 Bing 이미지의 가로 세로 비율을 유지 합니다. 너비와 높이 모두 지정 하 고 이미지의 원래 가로 세로 비율을 유지 하지, Bing 이미지의 테두리에 흰색 패딩이 추가 합니다. 예를 들어 480x359 이미지를 자르기 없이 200x200으로 조정하면 전체 너비에 이미지가 포함되지만 높이에는 이미지 맨 위와 맨 아래에 25픽셀의 흰색 패딩이 포함됩니다. 마찬가지로, 왼쪽 및 오른쪽 테두리를 제외한 이미지가 359x480인 경우에도 흰색 패딩이 포함됩니다. 이미지를 자르면 흰색 패딩이 추가되지 않습니다.  

 
다음 그림은 썸네일 이미지의 원래 크기(480x300)를 보여 줍니다.  
  
![원래 가로 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
다음 그림은 200x200으로 조정된 이미지를 보여 줍니다. 가로 세로 비율이 유지되고 위쪽 및 아래쪽 테두리에 흰색 패딩이 추가됩니다(검정 테두리는 패딩을 보여 주기 위해 포함됨).  
  
![조정된 가로 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



이미지의 원래 너비 및 높이보다 큰 치수를 지정하면 이미지의 왼쪽 및 위쪽 테두리에 흰색 패딩이 추가됩니다.  
  
이미지를 자르려면 c(자르기) 쿼리 매개 변수를 포함합니다. 다음은 지정할 수 있는 값입니다.  
  
- 4&mdash;블라인드 비율  
- 7&mdash;스마트 비율  
  
스마트 비율 자르기(c=7)를 요청하면 이미지의 가로 세로 비율을 유지하면서 이미지의 관심 영역 중심에서 바깥쪽으로 이미지가 잘립니다. 관심 영역은 가장 중요한 부분이 포함된 것으로 Bing이 판단하는 이미지 영역입니다. 다음은 관심 영역 예제입니다.  
  
![관심 영역](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

이미지 크기를 조정하고 스마트 비율 자르기를 요청하면 가로 세로 비율을 유지하면서 이미지가 요청된 크기로 축소됩니다. 그런 다음 조정된 크기에 따라 이미지가 잘립니다. 예를 들어 조정된 너비가 높이보다 작거나 같으면 이미지가 관심 영역 중심의 좌우로 잘립니다. 그렇지 않으면 이미지가 관심 영역 중심의 위아래로 잘립니다.  
  
 
다음은 스마트 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다.  
  
![200x200으로 잘린 가로 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
다음은 스마트 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다.  
   
![200x100으로 잘린 가로 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
다음은 스마트 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다.  
  
![100x200으로 잘린 가로 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Bing이 이미지의 관심 영역을 확인할 수 없는 경우 Bing은 블라인드 비율 자르기를 사용합니다.  
  
블라인드 비율 자르기(c=4)를 요청하면 Bing은 다음 규칙을 사용하여 이미지를 자릅니다.  
  
- (원래 이미지 너비/원래 이미지 높이) < (요청된 이미지 너비/요청된 이미지 높이)이면 왼쪽 맨 위 모서리에서 이미지를 측정하여 맨 아래에서 이미지를 자릅니다.  
- (원래 이미지 너비/원래 이미지 높이) > (요청된 이미지 너비/요청된 이미지 높이)이면 중심에서 이미지를 측정하여 좌우로 이미지를 자릅니다.  



다음은 225x300 세로 이미지를 보여 줍니다.  
  
![원래 해바라기 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
다음은 블라인드 비율 자르기를 사용하여 200x200으로 축소된 이미지를 보여 줍니다. 왼쪽 맨 위 모서리에서 이미지를 측정하여 이미지 맨 아래를 자릅니다.  
  
![200x200으로 잘린 해바라기 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
다음은 블라인드 비율 자르기를 사용하여 200x100으로 축소된 이미지를 보여 줍니다. 왼쪽 맨 위 모서리에서 이미지를 측정하여 이미지 맨 아래를 자릅니다.  
  
![200x100으로 잘린 해바라기 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
다음은 블라인드 비율 자르기를 사용하여 100x200으로 축소된 이미지를 보여 줍니다. 중심에서 이미지를 측정하여 이미지 좌우를 자릅니다.  
  
![100x200으로 잘린 해바라기 이미지](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

