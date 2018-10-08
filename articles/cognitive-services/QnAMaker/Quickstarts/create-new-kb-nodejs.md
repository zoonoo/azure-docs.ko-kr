---
title: '빠른 시작: API Node.js - 기술 자료 만들기 - QnA Maker'
description: 이 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 샘플 QnA Maker 기술 자료를 프로그래밍 방식으로 만드는 과정을 단계별로 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 435c937e64d0befc5a3bf30f9d58ccd303247b9e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037306"
---
# <a name="create-a-new-knowledge-base-in-nodejs"></a>Node.js에 새 기술 자료 만들기

이 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 샘플 QnA Maker 기술 자료를 프로그래밍 방식으로 만드는 과정을 단계별로 안내합니다.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

콘텐츠를 제공할 두 개의 샘플 FAQ URL이 아래에(**req={}** 의 'urls'에) 있습니다. [데이터 원본](../Concepts/data-sources-supported.md) 문서에 설명된 대로, QnA Maker는 반구조화된 컨텐츠에서 질문과 답변을 자동으로 추출합니다. 이 빠른 시작에서는 자신의 FAQ URL도 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6+](https://nodejs.org/en/download/)가 필요합니다.

**Microsoft QnA Maker API**를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. [Azure 대시보드](https://portal.azure.com/#create/Microsoft.CognitiveServices)에서 유료 구독 키가 필요합니다. 어느 키든지 빠른 시작에서 작동합니다.

![Azure 대시보드 서비스 키](../media/sub-key.png)

Visual Studio 및 Node.js에 대한 추가 도움말은 [빠른 시작: Visual Studio를 사용하여 첫 번째 Node.js 앱 만들기](https://docs.microsoft.com/en-us/visualstudio/ide/quickstart-nodejs)를 참조하세요.

## <a name="create-knowledge-base"></a>기술 자료 만들기

다음 코드에서는 [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 메서드를 사용하여 새 기술 자료를 만듭니다.

1. 즐겨 찾는 IDE에 새 Node.js 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 유효한 구독 키로 바꿉니다.
4. 프로그램을 실행합니다.

```nodejs
'use strict';

let fs = require('fs');
let https = require('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE';

// Components used to create HTTP request URIs for QnA Maker operations.
let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/create';

// Formats and indents JSON for display.
let pretty_print = function (s) {
    return JSON.stringify(JSON.parse(s), null, 4);
};

// The 'callback' is called from the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        // Call the 'callback' with the status code, headers, and body of the response.
        callback({ status: response.statusCode, headers: response.headers, body: body });
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

// Get an HTTP response handler that calls 'callback' from the entire response.
let get_response_handler = function (callback) {
    // Return a function that takes an HTTP response and is closed over the specified callback.
    // This function signature is required by https.request, hence the need for the closure.
    return function (response) {
        response_handler(callback, response);
    };
};

// Call 'callback' when we have the entire response from the POST request.
let post = function (path, content, callback) {
    let request_params = {
        method: 'POST',
        hostname: host,
        path: path,
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': content.length,
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the 'callback' function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.write(content);
    req.end();
};

// Call 'callback' when we have the entire response from the GET request.
let get = function (path, callback) {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.end();
};

// Call 'callback' when we have the response from the /knowledgebases/create POST method.
let create_kb = function (path, req, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the POST request.
    post(path, req, function (response) {
        // Extract the data we want from the POST response and pass it to the callback function.
        callback({ operation: response.headers.location, response: response.body });
    });
};

// Call 'callback' when we have the response from the GET request to check the status.
let check_status = function (path, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the GET request.
    get(path, function (response) {
        // Extract the data we want from the GET response and pass it to the callback function.
        callback({ wait: response.headers['retry-after'], response: response.body });
    });
};

// Dictionary that holds the knowledge base.
// The data source includes a QnA pair with metadata, the URL for the
// QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
let req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
        {
            "id": 0,
            "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
            "source": "Custom Editorial",
            "questions": [
                "How do I programmatically update my Knowledge Base?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ],
    "urls": [
        "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
};

// Build your path URL.
var path = service + method;
// Convert the request to a string.
let content = JSON.stringify(req);
create_kb(path, content, function (result) {
    // Formats and indents the JSON response from the /knowledgebases/create method for display.
    console.log(pretty_print(result.response));
    // Loop until the operation is complete.
    let loop = function () {
        path = service + result.operation;
        // Check the status of the operation.
        check_status(path, function (status) {
            // Formats and indents the JSON for display.
            console.log(pretty_print(status.response));
            // Convert the status into an object and get the value of the 'operationState'.
            var state = (JSON.parse(status.response)).operationState;
            // If the operation isn't complete, wait and query again.
            if (state === 'Running' || state === 'NotStarted') {
                console.log('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    };
    // Begin the loop.
    loop();
});
```

## <a name="understand-what-qna-maker-returns"></a>QnA Maker에서 반환되는 내용 이해하기

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 결과가 약간 다를 수도 있습니다. 최종 호출이 "성공" 상태를 반환하면 기술 자료가 생성된 것입니다. 문제를 해결하려면 QnA Maker API의 [작업 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)를 참조하세요.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917tt4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)