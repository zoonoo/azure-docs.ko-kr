---
title: 검색 결과를 필터링하는 방법 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: "Bing이 응답에 포함하는 답변 유형(예: 이미지, 비디오 및 뉴스)은 'responseFilter' 쿼리 매개 변수를 사용하여 필터링할 수 있습니다."
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221380"
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

## <a name="query-parameters"></a>쿼리 매개 변수

Bing에서 반환된 답변을 필터링하려면 API를 호출할 때 아래 쿼리 매개 변수를 사용합니다.  

### <a name="responsefilter"></a>응답 필터

bing이 응답에 포함되는 답변 유형(예: 이미지, 비디오 및 뉴스)을 쉼표로 구분한 답변 목록인 [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) 쿼리 매개 변수를 사용하여 필터링할 수 있습니다. Bing이 관련 콘텐츠를 발견하면 응답에 답변이 포함됩니다. 

이미지와 같은 응답에서 특정 답변을 제외하려면 `-` 문자를 답변 유형에 미리 준비합니다. 예를 들어:

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

### <a name="site"></a>사이트

특정 도메인에서 검색 결과를 얻으려면 `site:` 쿼리 문자열에 쿼리 매개 변수를 포함합니다.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> `site:` 쿼리 연산자를 사용하는 경우 쿼리에 따라 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) 설정에 관계없이 응답에 성인 콘텐츠가 포함될 수 있는 가능성이 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 콘텐츠를 지원하는 경우에만 `site:`를 사용해야 합니다.

### <a name="freshness"></a>새로 고침

Bing이 특정 기간 동안 검색한 웹 페이지로 웹 응답 결과를 제한하려면 [새로 고침](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) 쿼리 매개 변수를 다음 대/소문자 구분 되지 않는 값 중 하나로 설정합니다.

* `Day`— Bing이 지난 24시간 이내에 발견한 웹 페이지 반환
* `Week`— Bing이 지난 7일 이내에 발견한 웹 페이지 반환
* `Month`— 지난 30일 이내에 검색된 웹 페이지 반환

이 매개 변수를 양식의 사용자 지정 날짜 `YYYY-MM-DD..YYYY-MM-DD`범위로 설정할 수도 있습니다. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

결과를 단일 날짜로 제한하려면 새로 고침 매개 변수를 특정 날짜로 설정합니다.

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Bing이 필터 기준과 일치하는 웹 페이지 수가 요청한 웹 페이지 수(또는 Bing이 반환하는 기본 번호)보다 적은 경우 지정된 기간을 벗어나는 웹 페이지가 결과에 포함될 수 있습니다.

## <a name="limiting-the-number-of-answers-in-the-response"></a>응답의 답변 수 제한

Bing은 JSON 응답에서 여러 응답 유형을 반환할 수 있습니다. 예를 *들어, 항해 +dinghies를*쿼리하는 `webpages` `images`경우 `videos`Bing은 "를 반환할 수 있습니다. `relatedSearches`

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
