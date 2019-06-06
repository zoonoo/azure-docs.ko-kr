---
title: 검색 결과를 필터링하는 방법 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API의 검색 결과를 필터링하고 표시하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 8d8fd03d9c3d912788e9893377bbab3efac86f8a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383846"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>검색 응답에 포함되는 답변 필터링  

웹을 쿼리할 때 Bing은 검색에 대해 찾은 관련된 모든 콘텐츠를 반환합니다. 예를 들어 검색 쿼리가 “sailing+dinghies”인 경우 응답에는 다음과 같은 답변이 포함될 수 있습니다.

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```
[responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) 쿼리 매개 변수를 사용하여 수신하는 콘텐츠의 유형(예: 이미지, 비디오 및 뉴스)을 필터링할 수 있습니다. Bing에서 지정한 답변의 관련 콘텐츠를 찾으면 반환됩니다. 응답 필터는 쉼표로 구분된 답변 목록입니다. 

응답에서 이미지와 같은 특정 콘텐츠 유형을 제외하려면 `responseFilter` 값의 시작 부분에 `-` 문자를 추가할 수 있습니다. 쉼표(`,`)를 사용하여 제외된 형식을 구분할 수 있습니다. 예를 들면 다음과 같습니다.

```
&responseFilter=-images,-videos
```


아래에서는 `responseFilter`를 사용하여 돛단배의 이미지, 비디오 및 뉴스를 요청하는 방법을 보여 줍니다. 쿼리 문자열을 인코드하면 쉼표가 %2C로 바뀝니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

아래에서는 이전 쿼리에 대한 응답을 보여 줍니다. Bing이 관련 비디오 및 뉴스 결과를 찾지 못했기 때문에 응답에 이러한 결과가 포함되지 않습니다.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Bing이 이전 응답에서 비디오 및 뉴스 결과를 반환하지 않았다고 해서 비디오 및 뉴스 콘텐츠가 없는 것은 아닙니다. 단지 해당 페이지에 포함되지 않은 것입니다. 그러나 더 많은 결과를 [페이징](./paging-webpages.md)하면 후속 페이지에 이러한 콘텐츠가 있을 가능성이 큽니다. 또한 [Video Search API](../bing-video-search/search-the-web.md) 및 [News Search API](../bing-news-search/search-the-web.md) 엔드포인트를 직접 호출하는 경우 응답에 결과가 포함될 가능성이 큽니다.

`responseFilter`를 사용하여 단일 API에서 결과를 가져오는 것은 좋지 않습니다. 단일 Bing API의 콘텐츠를 원하는 경우 해당 API를 직접 호출합니다. 예를 들어 이미지만 받으려면 Image Search API 엔드포인트 `https://api.cognitive.microsoft.com/bing/v7.0/images/search` 또는 다른 [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) 엔드포인트 중 하나에 요청을 전송합니다. 성능상의 이유뿐 아니라 콘텐츠 특정 API가 더 풍부한 결과를 제공한다는 점에서 단일 API 호출이 중요합니다. 예를 들어 Web Search API에 제공되지 않는 필터를 사용하여 결과를 필터링할 수 있습니다.  

특정 도메인에서 검색 결과를 가져오려면 쿼리 문자열에 `site:` 쿼리 연산자를 포함합니다.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> `site:` 쿼리 연산자를 사용하는 경우 쿼리에 따라 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) 설정에 관계없이 응답에 성인 콘텐츠가 포함될 수 있는 가능성이 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 콘텐츠를 지원하는 경우에만 `site:`를 사용해야 합니다.

## <a name="limiting-the-number-of-answers-in-the-response"></a>응답의 답변 수 제한

Bing은 순위에 따라 응답에 답변을 포함합니다. 예를 들어 *sailing+dinghies*를 쿼리하는 경우 Bing은 `webpages`, `images`, `videos` 및 `relatedSearches`를 반환합니다.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Bing에서 반환하는 답변 수를 상위 두 개 답변(웹 페이지 및 이미지)으로 제한하려면 [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) 쿼리 매개 변수를 2로 설정합니다.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

응답에는 `webPages` 및 `images`만 포함됩니다.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

이전 쿼리에 `responseFilter` 쿼리 매개 변수를 추가하고 웹 페이지 및 뉴스로 설정하는 경우 뉴스에 순위가 지정되지 않았기 때문에 응답에는 웹 페이지만 포함됩니다.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>순위가 지정되지 않은 답변의 수준 올리기

Bing에서 쿼리에 대해 반환하는 상위 순위 답변이 웹 페이지, 이미지, 비디오 및 relatedSearches인 경우 응답에는 해당 답변이 포함됩니다. [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount)를 두 개(2)로 설정하면 Bing은 두 개의 상위 순위 답변인 웹 페이지와 이미지를 반환합니다. Bing에서 이미지 및 비디오를 응답에 포함하려면 [promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) 쿼리 매개 변수를 지정하고 이미지 및 비디오로 설정합니다.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

다음은 위 요청에 대한 응답입니다. Bing은 두 개의 상위 답변인 웹 페이지와 이미지를 반환하고, 비디오 수준을 올려 답변에 포함합니다.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

`promote`를 뉴스로 설정하는 경우 뉴스는 순위가 지정된 답변이 아니므로 응답에 뉴스 답변이 포함되지 않습니다. 순위가 지정된 답변만 수준을 올릴 수 있습니다.

수준을 올리려는 답변은 `answerCount` 제한에 계산되지 않습니다. 예를 들어 순위가 지정된 답변이 뉴스, 이미지 및 비디오이고 `answerCount`를 1로, `promote`를 뉴스로 설정하는 경우 응답에는 뉴스와 이미지가 포함됩니다. 또는 순위가 지정된 답변이 비디오, 이미지 및 뉴스인 경우 응답에는 비디오와 뉴스가 포함됩니다.

`answerCount` 쿼리 매개 변수를 지정하는 경우에만 `promote`를 사용할 수 있습니다.
