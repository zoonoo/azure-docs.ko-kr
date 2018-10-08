---
title: '빠른 시작: Node.js를 사용하여 검색 수행 - Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Node.js를 사용하여 Bing Web Search API를 처음 호출하고 JSON 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 9/26/2018
ms.author: erhopf
ms.openlocfilehash: debaa63adeb97063d0ea42e1da36352dc2c9c4e7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405859"
---
# <a name="quickstart-use-nodejs-to-call-the-bing-web-search-api"></a>빠른 시작: Node.js를 사용하여 Bing Web Search API 호출  

이 빠른 시작을 사용하여 Bing Web Search API를 처음 호출하고 10분 내에 JSON 응답을 받습니다.

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 실행하기 전에 필요한 몇 가지 조건은 다음과 같습니다.

* [Node.js 6](https://nodejs.org/en/download/) 이상
* 구독 키

## <a name="create-a-project-and-declare-required-modules"></a>프로젝트 만들기 및 필요한 모듈 선언

즐겨 찾는 IDE 또는 편집기에서 새 Node.js 프로젝트를 만듭니다.
그런 다음, 아래 코드 조각을 `search.js`라는 파일의 프로젝트에 복사합니다.

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>구독 키 설정

이 코드 조각은 `AZURE_SUBSCRIPTION_KEY` 환경 변수를 사용하여 구독 키를 저장하고, 코드를 배포할 때 실수로 인한 키의 노출을 방지하도록 모범 사례를 사용합니다. [여기를 클릭](https://azure.microsoft.com/try/cognitive-services/my-apis/?apiSlug=search-api-v7)하여 구독 키를 조회합니다.

환경 변수 사용에 익숙하지 않거나 이 앱을 가능한 빨리 실행하려는 경우 `process.env['AZURE_SUBSCRIPTION_KEY']`를 문자열로 설정한 구독 키로 바꿀 수 있습니다.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>요청을 만드는 함수 만들기

이 함수는 보안 GET 요청을 만들어 검색 쿼리를 경로에 쿼리 매개 변수로 저장합니다. `encodeURIComponent`는 잘못된 문자를 이스케이프하는 데 사용되고, 구독 키는 헤더에 전달됩니다. 콜백은 `data` 이벤트를 구독하여 JSON 본문을 집계하는 [응답](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse), 모든 문제를 기록하는 `error` 이벤트 및 메시지를 완료로 간주해야 하는 시기를 알 수 있는 `end` 이벤트를 수신합니다. 완료되면 앱은 흥미로운 헤더 및 메시지 본문을 인쇄합니다. 색을 사용하여 재생하고 기본 설정에 맞도록 깊이를 설정할 수 있으며, `1`의 깊이는 응답의 유용한 요약을 제공합니다.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>쿼리 가져오기

쿼리를 찾기 위해 프로그램의 인수를 살펴보겠습니다. 첫 번째 인수는 노드에 대한 경로이고, 두 번째는 파일 이름이며, 세 번째는 쿼리입니다. 쿼리가 없는 경우 "Microsoft Cognitive Services"의 기본 쿼리가 사용됩니다.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>요청 및 응답 출력

이제 모두 정의되었으므로 함수를 호출해 보겠습니다!

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>모든 요소 결합

마지막 단계는 코드를 실행하는 것입니다. `node search.js "<your query>"`

자신의 코드와 비교하려면 여기에 있는 완전한 프로그램을 사용하세요.

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment varable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

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
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
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
