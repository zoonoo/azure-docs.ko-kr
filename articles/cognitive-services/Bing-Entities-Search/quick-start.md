---
title: '빠른 시작: Bing Entity Search API'
description: Bing Entity Search API를 사용하여 시작하는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: ffc9ebb21c6646b1a39af4659053adf4157d204b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813960"
---
# <a name="quickstart-making-your-first-bing-entity-search-request"></a>빠른 시작: 첫 번째 Bing Entity Search 요청 만들기

Bing Entity Search API는 Bing에 검색 쿼리를 보내고 엔터티 및 장소가 포함된 결과를 가져옵니다. 장소 결과에는 식당, 호텔 또는 다른 현지 회사가 포함됩니다. 장소의 경우 쿼리에서 현지 회사의 이름을 지정하거나 목록을 요청할 수 있습니다(예: 근처의 식당). 엔터티 결과에는 사람, 장소 또는 사물이 포함됩니다. 이 컨텍스트의 장소는 관광 명소, 주, 국가 등입니다. 

## <a name="first-steps"></a>첫 번째 단계

첫 번째 호출을 수행하기 전에 먼저 Cognitive Services 구독 키를 가져와야 합니다. 키를 얻는 방법은 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api)를 참조하세요. (맨 위에 Entities Search API가 보이지 않는 경우 **검색** 탭을 클릭하고 API가 보일 때까지 아래로 스크롤합니다.)

## <a name="the-endpoint"></a>엔드포인트

엔터티 및 장소 검색 결과를 가져오려면 다음 엔드포인트로 GET 요청을 보냅니다.  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청이 서버에서 시작되는 것이 좋습니다. 클라이언트 응용 프로그램의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 응용 프로그램에 액세스할 가능성이 높아집니다. 또한 서버에서 호출을 만들면 향후 API 버전을 업그레이드하기 위한 단일 업그레이드 지점이 제공됩니다.

## <a name="specifying-query-parameters-and-headers"></a>쿼리 매개 변수 및 헤더 지정

요청에서는 사용자의 검색어가 포함된 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) 쿼리 매개 변수를 지정해야 합니다. 또한 요청에서는 결과를 가져올 지역/국가를 식별하는 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) 쿼리 매개 변수를 지정해야 합니다. 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters)를 참조하세요. URL은 모든 쿼리 매개 변수를 인코딩합니다.  
  
요청에서는 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

클라이언트 IP 및 위치 헤더는 위치 인식 콘텐츠를 반환하는 데 중요합니다.  

모든 요청 및 응답 헤더 목록은 [헤더](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers)를 참조하세요.

## <a name="the-request"></a>요청

다음은 모든 제안된 쿼리 매개 변수 및 헤더를 포함하는 엔터티 요청을 보여줍니다. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Bing API 중 하나를 처음으로 호출하는 경우 클라이언트 ID 헤더를 포함하지 마세요. 이전에 Bing API를 호출했고 Bing이 사용자 및 디바이스 조합에 대한 클라이언트 ID를 반환한 경우 클라이언트 ID만 포함하세요.

## <a name="the-response"></a>응답

다음은 이전 쿼리에 대한 응답을 보여 줍니다. 또한 이 예제는 Bing 관련 응답 헤더를 보여줍니다. 응답 개체에 대한 정보는 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse)를 참조하세요.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>다음 단계

API를 사용해 봅니다. [Entities Search API 테스트 콘솔](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/)로 이동합니다. 

응답 개체 사용에 대한 자세한 내용은 [엔터티 및 장소에 대한 웹 검색](./search-the-web.md)을 참조하세요.

검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./use-display-requirements.md)을 반드시 읽도록 합니다.
