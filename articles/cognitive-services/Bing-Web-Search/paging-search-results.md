---
title: 검색 결과를 통해 페이지 하는 방법 - 빙 검색 API
titleSuffix: Azure Cognitive Services
description: Bing 검색 API에서 검색 결과를 통해 페이징하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481732"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Bing 검색 API의 결과를 통해 페이징하는 방법

Bing 웹, 사용자 지정, 이미지, 뉴스 또는 비디오 검색 API에 대한 호출을 보내면 Bing은 쿼리와 관련이 있을 수 있는 총 결과 수의 하위 집합을 반환합니다. 사용 가능한 총 결과 수를 얻으려면 응답 개체의 `totalEstimatedMatches` 필드에 액세스합니다. 

예를 들어: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>검색 결과를 통한 페이징

사용 가능한 결과를 페이지로 `count` 이동하려면 요청을 보낼 때 및 `offset` 쿼리 매개 변수를 사용합니다.  

> [!NOTE]
>
> * Bing 비디오, 이미지 및 뉴스 API를 사용하여 페이징은`/video/search`일반`/news/search`비디오 (),`/image/search`뉴스 () 및 이미지 () 검색에만 적용됩니다. 인기 토픽 및 카테고리를 페이징하는 것은 지원되지 않습니다.  
> * 필드는 `TotalEstimatedMatches` 현재 쿼리에 대한 총 검색 결과 수의 추정치입니다. 및 `count` `offset` 매개변수를 설정하면 이 추정치가 변경될 수 있습니다.

| 매개 변수 | 설명                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 응답에서 반환할 결과의 수를 지정합니다. `count`의 기본값과 요청할 수 있는 최대 결과 수는 API에 따라 다릅니다. [다음 단계](#next-steps)아래의 참조 설명서에서 이러한 값을 찾을 수 있습니다. |
| `offset`  | 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.                                           |

예를 들어 페이지당 15개의 결과를 표시하려면 15와 `count` `offset` 0으로 설정하여 결과의 첫 페이지를 가져옵니다. 이후의 각 API 호출에 대해 `offset` 15씩 증가합니다. 다음은 오프셋 45에서 시작하여 웹 페이지 15개를 요청하는 예제입니다.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

기본값을 `count` 사용하는 경우 API 호출에서 `offset` 쿼리 매개 변수만 지정하면 됩니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Bing 이미지 및 비디오 API를 사용하는 경우 `nextOffset` 이 값을 사용하여 중복 검색 결과를 방지할 수 있습니다. 또는 응답 개체에서 값을 가져옵니다 및 `offset` 매개 변수와 함께 요청에 사용 합니다. `Videos` `Images`  

> [!NOTE]
> Bing 웹 검색 API는 웹 페이지, 이미지, 비디오 및 뉴스를 포함할 수 있는 검색 결과를 반환합니다. Bing 웹 검색 API에서 검색 결과를 통해 페이징하면 이미지나 뉴스와 같은 다른 답변 유형이 아닌 [웹 페이지만](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)페이징됩니다. 개체의 `WebPage` 검색 결과에는 다른 답변 유형에도 표시되는 결과가 포함될 수 있습니다.
>
> 필터 값을 `responseFilter` 지정하지 않고 쿼리 매개 변수를 사용하는 `count` 경우 `offset` 및 매개 변수를 사용하지 마십시오. 

## <a name="next-steps"></a>다음 단계

* [Bing 웹 검색 API는 무엇입니까?](bing-api-comparison.md)
* [Bing Web Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [빙 사용자 정의 검색 API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [빙 뉴스 검색 API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [빙 비디오 검색 API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [빙 이미지 검색 API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
