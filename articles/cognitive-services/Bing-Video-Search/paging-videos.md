---
title: 사용 가능한 비디오를 통해 페이징하는 방법 - Bing Video Search
titlesuffix: Azure Cognitive Services
description: Bing이 반환할 수 있는 모든 비디오를 페이징하는 방법을 보여줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181795"
---
# <a name="paging-videos"></a>비디오 페이징

Video Search API를 호출하면 Bing은 결과 목록을 반환합니다. 목록은 쿼리와 관련된 결과의 총 수의 하위 집합입니다. 예상되는 사용 가능한 총 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) 필드에 액세스해야 합니다.  
  
다음 예제는 비디오 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
사용 가능한 비디오를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
  
`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 105개입니다. 기본값은 35입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  
  
페이지당 20개의 비디오를 표시하려면 `count`를 20으로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 20씩 증가합니다(예: 20, 40).  

다음은 오프셋 40에서 시작하여 비디오 20개를 요청하는 예제입니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

기본 `count` 값이 구현에서 작동하는 경우 `offset` 쿼리 매개 변수를 지정하기만 하면 됩니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

일반적으로 비디오 35개를 한 번에 페이징하는 경우 첫 번째 요청에서 `offset` 쿼리 매개 변수를 0으로 설정한 다음, 후속 요청마다 `offset`을 35씩 증가시킵니다. 그러나 후속 응답의 결과 중 일부는 이전 응답과 중복될 수 있습니다. 예를 들어 응답의 처음 두 비디오는 이전 응답의 마지막 두 비디오와 동일할 수 있습니다.

중복되는 결과를 제거하려면 `Videos` 개체의 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) 필드를 사용하면 됩니다.

예를 들어 한 번에 30개 비디오를 페이징하려면 첫 번째 응답에서 `count`를 30으로, `offset`을 0으로 설정합니다. 그 다음 요청에서는 `offset` 쿼리 매개 변수를 `nextOffset` 값으로 설정합니다.


> [!NOTE]
> 페이징은 비디오 검색(/videos/search)에만 적용되고 비디오 인사이트(/videos/details) 또는 추세 비디오(/videos/trending)에는 적용되지 않습니다.

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 
