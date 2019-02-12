---
title: '빠른 시작: Bing Video Search REST API 및 Node.js를 사용하여 비디오 검색'
titlesuffix: Azure Cognitive Services
description: JavaScript를 통해 Bing Video Search REST API로 비디오 검색 요청을 보내려면 이 빠른 시작을 사용합니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: f593863b86bf5170c4d5c19cece622c1dcafc89c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568026"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-nodejs"></a>빠른 시작: Bing Video Search REST API 및 Node.js를 사용하여 비디오 검색

Bing Video Search API를 처음 호출하고 JSON 응답에서 검색 결과를 확인하려면 이 빠른 시작을 사용합니다. 이 간단한 JavaScript 애플리케이션은 HTTP 비디오 검색 쿼리를 API에 보내고, 응답을 표시합니다. 이 애플리케이션은 JavaScript에서 작성되고 Node.js를 사용하지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플에 대한 소스 코드는 추가 오류 처리 및 코드 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingVideoSearchv7.js)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Node.JS](https://nodejs.org/en/download/)

* JavaScript에 대한 요청 모듈
    * `npm install request`를 사용하여 이 모듈을 설치할 수 있습니다.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만듭니다. 엄격하게 설정하고 다음 요구 사항을 추가합니다.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. API 엔드포인트, 구독 키 및 검색어에 대한 변수를 만듭니다.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/videos/search';
    let term = 'kittens';
    ```

## <a name="create-a-response-handler"></a>응답 처리기 만들기

1. `response_handler`라는 함수를 만들어 API에서 JSON 응답을 가져옵니다. 응답 본문에 대한 변수를 만듭니다. `data` 플래그가 수신되면 `response.on()`을 사용하여 응답을 추가합니다.

    ```javascript
    let response_handler = function (response) {
        let body = '';
        response.on('data', function (d) {
            body += d;
        });
    };
    ```
    
    2. `end`가 표시되면 `response.on()`을 사용하여 bing과 관련된 헤더를 저장합니다(`bingapis` 또는 `x-msedge-`로 시작). 그런 다음, `JSON.parse()`를 사용하여 JSON을 구문 분석하고, `JSON.stringify()`를 사용하여 문자열로 변환한 후, 출력합니다.

        ```javascript
        response.on('end', function () {
            for (var header in response.headers)
                // header keys are lower-cased by Node.js
                if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                     console.log(header + ": " + response.headers[header]);
            body = JSON.stringify(JSON.parse(body), null, '  ');
            //JSON Response body
            console.log(body);
        });
        ```

# <a name="create-and-send-the-search-request"></a>검색 요청 만들기 및 보내기

1. `bing_video_search()`라는 함수를 만듭니다. 호스트 이름 및 헤더를 포함한 요청에 대한 매개 변수를 추가합니다. 검색어를 인코딩하고 `?q=` 매개 변수를 통해 경로 매개 변수에 추가합니다. 그런 다음, `req.end()`를 통해 요청을 보냅니다.

    ```javascript
    let bing_video_search = function (search_term) {
      console.log('Searching videos for: ' + term);
      let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search_term),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
        let req = https.request(request_params, response_handler);
        req.end();
    }
    ```

## <a name="json-response"></a>JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

 [Bing Video Search API란?](../overview.md)
