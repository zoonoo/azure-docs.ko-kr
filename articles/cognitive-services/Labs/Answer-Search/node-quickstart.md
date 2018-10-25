---
title: '빠른 시작: Project Answer Search, Node'
description: Node를 통해 Project Answer Search를 사용하여 시작합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1afd029803fc7d2709a9a9abe840db6d7f52498d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465749"
---
# <a name="quickstart-project-answer-search-with-node"></a>빠른 시작: Node를 통해 Project Answer Search 사용

다음 Node 예제에서는 요세미티 국립 공원에 대한 정보 쿼리를 만듭니다.

## <a name="prerequisites"></a>필수 조건

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) 평가판에 대한 액세스 키 가져오기

이 예제에서는 Node v8.9.4를 사용합니다.

## <a name="code-scenario"></a>코드 시나리오 

다음 코드는 답변을 가져옵니다.
다음 단계로 구현됩니다.
1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 미리 보기를 위한 쿼리 URL을 지정하고 쿼리 매개 변수를 추가합니다.  
3. 응답에 대한 처리기 함수를 만듭니다.
4. 요청을 만들고 *Ocp-Apim-Subscription-Key* 헤더를 추가하는 검색 기능을 정의합니다.
5. 검색 함수를 실행합니다. 

이 데모의 전체 코드는 다음과 같습니다.

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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

````

## <a name="next-steps"></a>다음 단계
- [C# 예제 코드](c-sharp-quickstart.md)
- [Java 빠른 시작](java-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)