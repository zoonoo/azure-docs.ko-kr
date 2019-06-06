---
title: Bing News Search API를 사용하여 뉴스 검색
titlesuffix: Azure Cognitive Services
description: 일반적인 뉴스, 최신 항목 및 헤드라인에 대한 검색 쿼리를 보내는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/11/2019
ms.author: scottwhi
ms.openlocfilehash: 77f8e41b2271770e49c2e5c4d1591c213eaa257d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383410"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Bing News Search API를 사용하여 뉴스 검색

Bing Image Search API를 통해 Bing의 인지적 뉴스 검색 기능을 애플리케이션으로 쉽게 통합할 수 있습니다.

Bing News Search API에서는 주로 관련 뉴스 문서를 찾고 반환하는 반면 웹에서 지능적이고 포커스가 있는 뉴스 검색을 제공합니다.

## <a name="suggest-and-use-search-terms"></a>검색 용어 제안 및 사용

사용자가 자신의 검색 용어를 입력할 수 있는 검색 상자를 제공하는 경우 [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md)를 사용하여 환경을 개선합니다. API는 부분 검색 용어 기반의 제안된 쿼리 문자열을 사용자 형식으로 반환합니다.

사용자가 검색어를 입력하면 URL에서 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) 쿼리 매개 변수를 설정하기 전에 해당 용어를 인코딩합니다. 예를 들어 사용자가 입력 *소형 범선*을 입력한 경우 `q`를 `sailing+dinghies` 또는 `sailing%20dinghies`로 설정합니다.

## <a name="get-general-news"></a>일반 뉴스 가져오기

웹에서 사용자의 검색 용어와 관련된 뉴스 기사를 가져오려면 다음 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Bing API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 전에 Bing API를 호출하고 Bing이 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다.

특정 도메인에서 뉴스를 가져오려면 [site:](https://msdn.microsoft.com/library/ff795613.aspx) 쿼리 연산자를 사용합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

아래에서는 이전 쿼리에 대한 응답을 보여 줍니다. Bing search API에 대한 [요구 사항 사용 및 표시](../useanddisplayrequirements.md)의 일부로 응답에 제공된 순서대로 각 뉴스 기사를 표시해야 합니다. 기사에 클러스터형 기사가 포함된 경우 관련 기사가 있음을 나타내야 하며 요청에 따라 해당 기사를 표시해야 합니다.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

[news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) 응답은 Bing이 인식하기에 쿼리와 관련이 있는 뉴스 기사를 나열합니다. `totalEstimatedMatches` 필드에는 볼 수 있는 기사의 예상 수가 포함됩니다. 기사를 페이징하는 방법은 [뉴스 페이징](../paging-news.md)을 참조하세요.

목록에서 각 [뉴스 기사](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle)는 기사의 제목, 설명 및 호스트의 웹 사이트에서 기사에 대한 URL을 포함합니다. 기사가 이미지를 포함하는 경우 개체는 이미지의 썸네일을 포함합니다. 호스트의 사이트에 있는 기사로 사용자를 이동시키는 하이퍼링크를 만들려면 `name` 및 `url`을 사용합니다. 기사에 이미지가 포함된 경우 `url`을 사용하여 이미지도 클릭할 수 있게 만들어야 합니다. `provider`를 사용하여 기사의 특성을 확인합니다.

Bing이 뉴스 기사의 범주를 확인할 수 있으면 기사에 `category` 필드가 포함됩니다.

## <a name="get-todays-top-news"></a>오늘의 주요 뉴스 가져오기

오늘의 주요 뉴스 기사를 가져오려면 일반 뉴스를 가져올 때와 동일한 요청을 만들되, `q`를 설정하지 말고 그대로 둡니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

주요 뉴스 가져오기에 대한 응답은 일반 뉴스 가져오기와 거의 동일합니다. 그러나 정해진 수의 결과가 있기 때문에 `news` 응답에 `totalEstimatedMatches` 필드가 포함되지 않습니다. 주요 뉴스 기사의 수는 뉴스 주기에 따라 달라질 수 있습니다. `provider`를 사용하여 기사의 특성을 확인합니다.

## <a name="get-news-by-category"></a>범주별 뉴스 가져오기

주요 스포츠 또는 엔터테인먼트 기사처럼 범주별 뉴스 기사를 가져오려면 Bing에 다음 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

[category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) 쿼리 매개 변수를 사용하여 가져올 기사 범주를 지정합니다. 지정할 수 있는 뉴스 범주 목록은 [시장별 뉴스 범주](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market)를 참조하세요.

범주별 뉴스 가져오기에 대한 응답은 일반 뉴스 가져오기와 거의 동일합니다. 그러나 모든 기사를 지정된 범주에서 가져옵니다.

## <a name="get-headline-news"></a>헤드라인 뉴스 가져오기

헤드라인 뉴스 기사를 요청하여 모든 뉴스 범주에서 기사를 가져오려면 Bing에 다음 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

[category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) 쿼리 매개 변수를 포함하지 마세요.

헤드라인 뉴스 가져오기에 대한 응답은 오늘의 주요 뉴스 가져오기와 거의 동일합니다. 기사가 헤드라인 기사인 경우 `headline` 필드가 **true**로 설정됩니다.

기본적으로 응답에 최대 12개의 헤드라인 기사가 포함됩니다. 반환할 헤드라인 기사의 수를 변경하려면 [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount) 쿼리 매개 변수를 지정하세요. 응답에는 뉴스 범주당 최대 4개의 비 헤드라인 기사도 포함됩니다.

응답은 클러스터를 하나의 기사로 계산합니다. 클러스터에 여러 기사가 포함될 수 있으므로 응답에 12개를 초과하는 헤드라인 기사와 4개를 초과하는 범주별 비 헤드라인 기사가 포함될 수 있습니다.

## <a name="get-trending-news"></a>최신 뉴스 가져오기

소셜 네트워크에서 유행하는 뉴스 토픽을 가져오려면 Bing에 다음 GET 요청을 보냅니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> 유행 토픽은 en-US 및 zh-CN 시장에서만 사용할 수 있습니다.

다음 JSON은 이전 요청에 대한 응답입니다. 각 유행 뉴스 기사에는 관련 이미지, 뉴스 속보 플래그, 기사에 대한 Bing 검색 결과의 URL이 포함됩니다. `webSearchUrl` 필드에 URL을 사용하여 사용자를 Bing 검색 결과 페이지로 보낼 수 있습니다. 또는 쿼리 텍스트로 [Web Search API](../../bing-web-search/search-the-web.md)를 호출하여 직접 결과를 표시할 수 있습니다.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>관련 뉴스 가져오기

뉴스 기사와 관련된 다른 기사가 있는 경우 뉴스 기사에 [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles) 필드가 포함될 수 있습니다. 다음은 클러스터형 기사가 포함된 기사를 보여줍니다.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing News Search 결과를 통해 페이징하는 방법](../paging-news.md)
