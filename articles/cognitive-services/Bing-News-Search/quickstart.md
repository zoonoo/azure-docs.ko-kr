---
title: News Search API 빠른 시작 | Microsoft Docs
description: Bing News Search API 사용을 시작하는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377383"
---
# <a name="your-first-news-search-query"></a>첫 번째 뉴스 검색 쿼리

첫 번째 호출을 만들려면 Cognitive Services 구독 키가 필요합니다. 이 키를 얻는 방법은 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)를 참조하세요.

뉴스 전용 검색 결과를 가져오려면 다음 엔드포인트로 GET 요청을 보냅니다.

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청이 서버에서 시작되는 것이 좋습니다. 클라이언트 응용 프로그램의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 응용 프로그램에 액세스할 가능성이 높아집니다. 또한 서버에서 호출을 만들면 향후 API 버전을 업그레이드하기 위한 단일 업그레이드 지점이 제공됩니다.

요청에서는 사용자의 검색어가 포함된 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) 쿼리 매개 변수를 지정해야 합니다. 선택 사항이지만, 요청에서 결과를 가져올 지역/국가를 식별하는 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt) 쿼리 매개 변수도 지정해야 합니다. `freshness` 및 `textDecorations` 같은 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters)를 참조하세요. 모든 쿼리 매개 변수 값은 URL 인코딩되어야 합니다.

요청에서는 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

클라이언트 IP 및 위치 헤더는 위치 인식 콘텐츠를 반환하는 데 중요합니다.

모든 요청 및 응답 헤더 목록은 [헤더](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers)를 참조하세요.

## <a name="the-request"></a>요청

다음은 모든 제안된 쿼리 매개 변수 및 헤더를 포함하는 뉴스 요청을 보여줍니다. Bing API 중 하나를 처음으로 호출한 경우 클라이언트 ID 헤더를 포함하지 마세요. 전에 Bing API를 호출하고 Bing이 사용자 및 장치 조합에 대한 클라이언트 ID를 반환한 경우만 클라이언트 ID를 포함합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>응답

다음은 이전 쿼리에 대한 응답을 보여 줍니다. 또한 이 예제는 Bing 관련 응답 헤더를 보여 줍니다.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
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
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>다음 단계

API를 사용해 봅니다. [News Search API 테스트 콘솔](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553)로 이동합니다.

응답 개체 사용에 대한 자세한 내용은 [Bing News Search란?](./search-the-web.md)을 참조하세요. 또한 다음과 같은 일반 작업에 대한 자세한 정보를 찾을 수 있습니다.

- [오늘날의 최신 뉴스 가져오기](./search-the-web.md#getting-todays-top-news)
- [범주별 뉴스 가져오기](./search-the-web.md#getting-news-by-category)
- [추세 뉴스 가져오기](./search-the-web.md#getting-trending-news)
