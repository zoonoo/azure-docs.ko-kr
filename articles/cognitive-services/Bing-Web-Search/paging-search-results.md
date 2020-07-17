---
title: 검색 결과를 페이징 하는 방법-Bing Search API
titleSuffix: Azure Cognitive Services
description: Bing Search API에서 검색 결과를 페이징 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "73481732"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Bing Search API 결과를 페이징 하는 방법

Bing Web, Custom, Image, News 또는 Video Search Api에 대 한 호출을 보내면 Bing에서 쿼리와 관련이 있을 수 있는 총 결과 수의 하위 집합을 반환 합니다. 사용 가능한 총 예상 결과 수를 가져오려면 응답 개체의 `totalEstimatedMatches` 필드에 액세스 합니다. 

다음은 그 예입니다. 

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

## <a name="paging-through-search-results"></a>검색 결과를 통해 페이징

사용 가능한 결과를 페이지 하려면 요청을 보낼 `count` 때 `offset` 및 쿼리 매개 변수를 사용 합니다.  

> [!NOTE]
>
> * Bing Video, Image 및 News Api로 페이징은 일반 비디오 (`/video/search`), 뉴스 (`/news/search`) 및 이미지 (`/image/search`) 검색에만 적용 됩니다. 추세 항목 및 범주를 통한 페이징은 지원 되지 않습니다.  
> * 필드 `TotalEstimatedMatches` 는 현재 쿼리에 대 한 총 검색 결과 수의 예상 값입니다. `count` 및 `offset` 매개 변수를 설정 하면이 예상 값이 변경 될 수 있습니다.

| 매개 변수 | Description                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 응답에서 반환할 결과의 수를 지정합니다. 의 `count`기본값 및 요청할 수 있는 최대 결과 수는 API에 따라 달라 집니다. [다음 단계](#next-steps)에서 참조 설명서에서 이러한 값을 찾을 수 있습니다. |
| `offset`  | 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.                                           |

예를 들어 페이지당 15 개의 결과를 표시 하려면를 15로 설정 하 고 `count` `offset` 를 0으로 설정 하 여 결과의 첫 번째 페이지를 가져옵니다. 각 후속 API 호출에 대해 15 씩 증가 `offset` 합니다. 다음은 오프셋 45에서 시작하여 웹 페이지 15개를 요청하는 예제입니다.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

기본값 `count` 을 사용 하는 경우 API 호출 에서만 `offset` 쿼리 매개 변수를 지정 해야 합니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Bing 이미지 및 비디오 Api를 사용 하는 경우 `nextOffset` 값을 사용 하 여 중복 검색 결과를 방지할 수 있습니다. 또는 응답 개체에서 값을 가져온 다음 `offset` 매개 변수를 사용 하 여 요청에 사용 합니다. `Videos` `Images`  

> [!NOTE]
> Bing Web Search API은 웹 페이지, 이미지, 비디오 및 뉴스를 포함할 수 있는 검색 결과를 반환 합니다. Bing Web Search API에서 검색 결과를 페이징 하는 경우에는 이미지 또는 뉴스와 같은 다른 응답 형식이 아닌 [웹 페이지](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)만 페이징할 수 있습니다. 개체의 `WebPage` 검색 결과에는 다른 응답 형식에도 표시 되는 결과가 포함 될 수 있습니다.
>
> 필터 값을 지정 `responseFilter` 하지 않고 쿼리 매개 변수를 사용 하는 경우 `count` 및 `offset` 매개 변수를 사용 하지 마세요. 

## <a name="next-steps"></a>다음 단계

* [Bing Web Search Api는 무엇 인가요?](bing-api-comparison.md)
* [Bing Web Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Custom Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
