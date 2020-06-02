---
title: 빠른 시작 - Node.js를 사용하여 API에 쿼리 보내기 - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청 전송을 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873068"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Bing Local Business Search API에 쿼리 보내기

이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청을 보내는 방법을 알아봅니다. 이 간단한 애플리케이션은 Node.js에서 작성되었지만 API는 RESTful 웹 서비스로, HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다.

이 예제 애플리케이션은 검색 쿼리에 대한 API에서 로컬 응답 데이터를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

* 최신 버전의 [Node.js](https://nodejs.org/en/download/).
* [JavaScript 요청 라이브러리](https://github.com/request/request).
* Bing Search API를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 API 키를 저장합니다. 자세한 내용은 [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)를 참조하세요.


## <a name="code-scenario"></a>코드 시나리오

다음 코드는 다음 단계에서 구현된 요청을 정의하고 보냅니다.

1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 쿼리를 지정하고 쿼리 매개 변수를 추가합니다.
3. 응답에 대한 처리기 함수를 만듭니다.
4. 요청을 만들고 `Ocp-Apim-Subscription-Key` 헤더를 추가하는 검색 기능을 정의합니다.
5. 검색 함수를 실행합니다.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>다음 단계

* [Local Business Search C# 빠른 시작](local-quickstart.md)
* [Local Business Search Java 빠른 시작](local-search-java-quickstart.md)
* [Local Business Search Python 빠른 시작](local-search-python-quickstart.md)
