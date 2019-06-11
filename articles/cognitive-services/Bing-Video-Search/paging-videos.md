---
title: 사용 가능한 비디오를 통해 페이징하는 방법 - Bing Video Search
titlesuffix: Azure Cognitive Services
description: Bing Video Search API에서 반환되는 모든 비디오를 페이징하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 12549bb53a21dd657f51a4a02460ddc82c47bef8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386398"
---
# <a name="paging-through-video-search-results"></a>비디오 검색 결과를 통한 페이징

Bing Video Search API는 쿼리에서 찾은 모든 검색 결과의 하위 집합을 반환합니다. API에 대한 후속 호출을 통해 이러한 결과를 페이징하면 애플리케이션에서 해당 결과를 가져와 표시할 수 있습니다.

> [!NOTE]
> 페이징은 비디오 검색(/videos/search)에만 적용되고 비디오 인사이트(/videos/details) 또는 추세 비디오(/videos/trending)에는 적용되지 않습니다.

## <a name="total-estimated-matches"></a>총 예상 일치 항목 수

검색되는 예상 결과 수를 가져오려면 JSON 응답에서 [ totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) 필드를 사용합니다.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>비디오를 통한 페이징

사용 가능한 비디오를 페이징하려면 요청을 보낼 때 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
  

|매개 변수  |설명  |
|---------|---------|
|`count`     | 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 100개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.        |
|`offset`     | 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.          |

예를 들어 페이지당 20개의 문서를 표시하려면 `count`를 20으로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 20씩 증가합니다(예: 20, 40).  
  
다음 예제에서는 40 오프셋에서 시작하여 20개의 비디오를 요청합니다.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

[count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count)에 기본값을 사용하는 경우 다음 예제와 같이 `offset` 쿼리 매개 변수만 지정하면 됩니다.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

한 번에 35개의 비디오를 페이징하는 경우 첫 번째 요청에서 `offset` 쿼리 매개 변수를 0으로 설정한 다음, 후속 요청마다 `offset`을 35씩 증가시킵니다. 그러나 다음 응답의 일부 결과에는 이전 응답과 중복되는 비디오 결과가 포함될 수 있습니다. 예를 들어 응답의 처음 두 비디오는 이전 응답의 마지막 두 비디오와 같을 수 있습니다.

중복되는 결과를 제거하려면 `Videos` 개체의 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) 필드를 사용하면 됩니다.

예를 들어 한 번에 30개의 비디오를 페이징하려면 첫 번째 요청에서 `count`를 30으로, `offset`을 0으로 설정할 수 있습니다. 다음 요청에서는 `offset` 쿼리 매개 변수를 `nextOffset` 값으로 설정합니다.

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 
  
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비디오 인사이트 가져오기](video-insights.md)
