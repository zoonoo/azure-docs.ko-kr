---
title: '빠른 시작: Node.js를 사용하여 Bing Entity Search REST API에 검색 요청 보내기'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 C#을 통해 Bing Entity Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 177e0ef0adab3b683657a9b872300cb38185d795
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384564"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Bing Entity Search REST API에 검색 요청 보내기

이 빠른 시작을 사용하여 Bing Entity Search API를 처음 호출하고 JSON 응답을 봅니다. 이 간단한 JavaScript 애플리케이션은 뉴스 검색 쿼리를 API에 보내고, 응답을 표시합니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js)에 제공됩니다.

이 애플리케이션은 JavaScript에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>필수 조건

* 최신 버전의 [Node.js](https://nodejs.org/en/download/).

* [JavaScript 요청 라이브러리](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 엄격성 및 HTTPS 요구 사항을 설정합니다.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. API 엔드포인트, 구독 키 및 검색 쿼리에 대한 변수를 만듭니다.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. `query`라는 문자열에 시장 및 쿼리 매개 변수를 추가합니다. `encodeURI()`를 사용하여 쿼리를 url로 인코딩하도록 하세요.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>응답 처리 및 구문 분석

1. HTTP 호출, `response`를 매개 변수로 사용하는 `response_handler` 함수를 정의합니다. 이 함수 내에서 다음 단계를 수행합니다.

    1. JSON 응답 본문을 포함할 변수를 정의합니다.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. **data** 플래그가 호출될 때 응답 본문을 저장합니다.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. **end** 플래그가 신호로 전송되면 JSON을 구문 분석하고 출력합니다.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>요청 보내기

1. `Search`라는 함수를 만들어 검색 요청을 보냅니다. 그 과정에서 다음 단계를 수행합니다.

   1. 요청 매개 변수를 포함하는 JSON 개체를 만듭니다. 메서드에 `Get`를 사용하고 호스트 및 경로 정보를 추가하세요. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가합니다. 
   2. `https.request()`를 사용하여 앞서 만든 응답 처리기와 검색 매개 변수가 포함된 요청을 보냅니다.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. `Search()` 함수를 호출합니다.

## <a name="example-json-response"></a>예제 JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API란?](../overview.md )
* [Bing Entity Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
