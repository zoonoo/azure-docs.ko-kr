---
title: 사용 가능한 웹 페이지 페이징 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Bing Custom Search가 반환할 수 있는 모든 웹 페이지를 페이징하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 3c1bf9c6f2c1b38b9cf9729b769c9198da56147a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388578"
---
# <a name="paging-webpages"></a>웹 페이지 페이징 

Custom Search API를 호출하면 Bing이 결과 목록을 반환합니다. 이 목록은 쿼리와 관련된 총 결과 수의 하위 집합입니다. 예상되는 총 사용 가능한 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) 필드에 액세스해야 합니다.  
  
다음 예제는 웹 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
사용 가능한 웹 페이지를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
  
`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 50개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  
  
페이지당 15개의 이미지를 표시하려면 `count`를 15로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 15씩 증가합니다(예: 15, 30).  
  
다음은 오프셋 45에서 시작하여 이미지 15개를 요청하는 예제입니다.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

기본 `count` 값을 구현에 사용하는 경우 `offset` 쿼리 매개 변수를 지정하기만 하면 됩니다.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 

