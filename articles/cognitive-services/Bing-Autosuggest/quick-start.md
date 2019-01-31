---
title: '빠른 시작: Bing Autosuggest API'
titlesuffix: Azure Cognitive Services
description: Bing Autosuggest API 사용을 시작하는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: c748f58028d747b2d963d6ac28ea2661e41ec68c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184515"
---
# <a name="quickstart-making-your-first-autosuggest-query"></a>빠른 시작: 첫 번째 Autosuggest 쿼리 수행

첫 번째 호출을 수행하기 전에 먼저 Cognitive Services 구독 키를 가져와야 합니다. 키를 받으려면 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api)를 참조하세요.

웹 검색 결과를 가져오려면 다음 엔드포인트로 GET 요청을 보냅니다.

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> V7 미리 보기 엔드포인트:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청은 서버에서 시작되는 것이 좋습니다. 클라이언트 애플리케이션의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 애플리케이션에 액세스할 가능성이 높아집니다. 또한 서버에서 호출하면 향후 API 버전을 위한 단일 업그레이드 지점이 제공됩니다.

요청에서 사용자의 부분 검색어가 포함된 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) 쿼리 매개 변수를 지정해야 합니다. 선택 사항이지만, 요청에서 결과를 가져올 지역/국가를 식별하는 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) 쿼리 매개 변수도 지정해야 합니다. 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)를 참조하세요. 모든 쿼리 매개 변수 값은 URL로 인코드되어야 합니다.

요청에서 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

클라이언트 IP 및 위치 헤더는 위치 인식 콘텐츠를 반환하는 데 중요합니다.

모든 요청 및 응답 헤더 목록은 [헤더](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)를 참조하세요.

## <a name="the-request"></a>요청

요청에는 제안된 모든 쿼리 매개 변수 및 헤더가 포함되어야 합니다. 사용자가 검색 상자에 새 문자를 입력할 때마다 이 API를 호출합니다. 쿼리 문자열의 완전성은 API가 반환하는 제안된 쿼리 용어의 관련성에 영향을 줍니다. 쿼리 문자열이 완전할수록 제안된 쿼리 용어 목록의 관련성이 커집니다. 예를 들어 API가 *s*에 대해 반환할 수 있는 제안은 *sailing dinghies*에 대해 반환하는 쿼리보다 관련성이 낮을 가능성이 큽니다. 

다음 예제에서는 *sail*에 대해 제안된 쿼리 문자열을 반환하는 요청을 보여 줍니다.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> V7 미리 보기 요청:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Bing API 중 하나를 처음 호출하는 경우 클라이언트 ID 헤더를 포함하면 안 됩니다. 이전에 Bing API를 호출했으며 Bing에서 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우에만 클라이언트 ID 헤더를 포함합니다.

다음은 이전 요청에 대한 응답을 보여 줍니다. 응답에는 검색 쿼리 제안 목록을 포함하는 웹 제안 그룹이 포함됩니다. 각 제안에는 `displayText`, `query` 및 `url` 필드가 포함됩니다.

`displayText` 필드에는 검색 상자의 드롭다운 목록을 채우는 데 사용하는 제안된 쿼리가 포함됩니다. 응답에 포함된 모든 제안을 지정된 순서대로 표시해야 합니다.  

사용자가 드롭다운 목록에서 쿼리를 선택하면 `query` 필드에 쿼리 문자열을 사용하여 [Bing Search API](../bing-web-search/search-the-web.md)를 호출하고 결과를 직접 표시할 수 있습니다. 또는 `url` 필드에 URL을 사용하여 사용자를 Bing 검색 결과 페이지로 보낼 수 있습니다.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>다음 단계

API를 사용해 봅니다. [Autosuggest API 테스트 콘솔](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2)로 이동합니다.

응답 개체 사용에 대한 자세한 내용은 [제안된 검색어 가져오기](./get-suggested-search-terms.md)를 참조하세요.
