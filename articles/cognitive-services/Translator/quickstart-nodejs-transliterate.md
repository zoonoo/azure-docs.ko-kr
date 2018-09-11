---
title: Translator Text Node.js를 사용하여 텍스트 스크립트 변환 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Node.js와 함께 Translator Text API를 사용하여 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: b16af911e5822deaa7cc7bcfe792245ae154eb26
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2018
ms.locfileid: "43750445"
---
# <a name="quickstart-transliterate-text-with-nodejs"></a>빠른 시작: Node.js를 사용하여 텍스트 음역

이 빠른 시작에서는 Translator Text API를 사용하여 한 언어의 텍스트를 다른 언어로 변환합니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)가 필요합니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="transliterate-request"></a>음역 요청

다음은 [Transliterate](./reference/v3-0-transliterate.md) 메서드를 사용하여 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.

1. 원하는 코드 편집기에서 Node.js 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/transliterate?api-version=3.0';

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
let params = '&language=ja&fromScript=jpan&toScript=latn';

// Transliterate "good afternoon".
let text = 'こんにちは';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Transliterate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Transliterate (content);
```

## <a name="transliterate-response"></a>음역 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>다음 단계

번역 및 언어 식별을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=javascript)
