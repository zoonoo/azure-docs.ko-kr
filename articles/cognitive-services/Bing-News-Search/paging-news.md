---
title: 사용 가능한 문서를 페이징하는 방법 - Bing News Search
titlesuffix: Azure Cognitive Services
description: Bing New Search가 반환할 수 있는 모든 새 문서를 페이징하는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: fff1da15df2e690cd0b37bb82654a4d30159325a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803395"
---
# <a name="paging-news"></a>뉴스 페이징

News Search API를 호출하면 Bing이 결과 목록을 반환합니다. 이 목록은 쿼리와 관련된 총 결과 수의 하위 집합입니다. 예상되는 사용 가능한 총 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) 필드에 액세스해야 합니다.  
  
다음 예제는 뉴스 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
사용 가능한 문서를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
  
`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 100개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  

페이지당 20개의 문서를 표시하려면 `count`를 20으로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 20씩 증가합니다(예: 20, 40).  
  
다음은 오프셋 40에서 시작하여 새 문서 20개를 요청하는 예제입니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
기본 `count` 값이 구현에서 작동하는 경우 다음 예제와 같이 `offset` 쿼리 매개 변수만 지정하면 됩니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> 페이징은 뉴스 검색(/news/search)에만 적용되고 추세 항목(/news/trendingtopics) 또는 뉴스 카테고리(/news)에는 적용되지 않습니다.