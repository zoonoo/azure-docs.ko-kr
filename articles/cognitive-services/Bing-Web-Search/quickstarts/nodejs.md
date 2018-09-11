---
title: '빠른 시작: Node.js를 사용하여 Bing Web Search API 호출'
description: 이 빠른 시작에서는 Node.js를 사용하여 Bing Web Search API를 처음 호출하고 JSON 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 7a46500f7cbf319c788761bccfaa92197ef67490
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886934"
---
# <a name="quickstart-use-nodejs-to-call-the-bing-web-search-api"></a>빠른 시작: Node.js를 사용하여 Bing Web Search API 호출  

이 빠른 시작을 사용하여 Bing Web Search API를 처음 호출하고 10분 내에 JSON 응답을 받습니다.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 실행하기 전에 필요한 몇 가지 조건은 다음과 같습니다.

* [Node.js 6](https://nodejs.org/en/download/) 이상
* 구독 키  

## <a name="create-a-project-and-declare-required-modules"></a>프로젝트 만들기 및 필요한 모듈 선언

즐겨 찾는 IDE 또는 편집기에서 새 Node.js 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 프로젝트에 복사합니다. 이 빠른 시작에서는 strict 모드를 사용하며, 데이터를 보내고 받기 위해 `https` 모듈이 필요합니다.

```javascript
// Use strict mode.
'use strict';

// Require the https module.
let https = require('https');
```

## <a name="define-variables"></a>변수 정의

계속하기 전에 몇 가지 변수를 설정해야 합니다. `host` 및 `path`가 올바른지 확인하고 `subscriptionKey` 값을 Azure 계정의 유효한 구독 키로 바꿉니다. `term` 값을 바꿔 검색 쿼리를 사용자 지정할 수 있습니다.

```javascript
// Replace with a valid subscription key.
let subscriptionKey = 'enter key here';

/*
 * Verify the endpoint URI. If you
 * encounter unexpected authorization errors, double-check this host against
 * the endpoint for your Bing Web search instance in your Azure dashboard.  
 */
let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/search';
let term = 'Microsoft Cognitive Services';

// Validate the subscription key.
if (subscriptionKey.length === 32) {
    bing_web_search(term);
} else {
    console.log('Invalid Bing Search API subscription key!');
    console.log('Please paste yours into the source code.');
}
```

## <a name="create-a-response-handler"></a>응답 처리기 만들기

응답을 문자열로 변환하고 구문 분석할 처리기를 만듭니다. 다음 섹션에서 살펴보겠지만 `response_handler`는 Bing Web Search API에 요청할 때마다 호출됩니다.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        console.log('\nRelevant Headers:\n');
        for (var header in response.headers)
            // Headers are lowercased by Node.js.
            if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                 console.log(header + ": " + response.headers[header]);
        // Stringify and parse the response body.
        body = JSON.stringify(JSON.parse(body), null, '  ');
        console.log('\nJSON Response:\n');
        console.log(body);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};
```

## <a name="make-a-request-and-print-the-response"></a>요청 및 응답 출력

요청을 구성하고 Bing Web Search API를 호출합니다. 요청되면 `response_handler` 함수가 호출되고 응답이 출력됩니다.

```javascript
let bing_web_search = function (search) {
    console.log('Searching the Web for: ' + term);
        // Declare the method, hostname, path, and headers.
        let request_params = {
            method : 'GET',
            hostname : host,
            path : path + '?q=' + encodeURIComponent(search),
            headers : {
                'Ocp-Apim-Subscription-Key' : subscriptionKey,
            }
        };
    // Request to the Bing Web Search API.
    let req = https.request(request_params, response_handler);
    req.end();
}
```

## <a name="put-it-all-together"></a>모든 요소 결합

마지막 단계는 코드를 실행하는 것입니다! 자신의 코드와 비교하려면 [GitHub에 있는 샘플 코드를 사용할 수 있습니다](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="sample-response"></a>샘플 응답

Bing Web Search API의 응답은 JSON으로 반환됩니다. 이 샘플 응답은 단일 결과를 표시하도록 잘렸습니다.  

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Web Search 단일 페이지 앱 자습서](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
