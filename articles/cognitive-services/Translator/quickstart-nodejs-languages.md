---
title: Translator Text Node.js를 사용하여 지원되는 언어 가져오기 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Node.js와 함께 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: dd37aab3707c6f06b8cc2e942366e19746694252
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2018
ms.locfileid: "43750450"
---
# <a name="quickstart-get-supported-languages-with-nodejs"></a>빠른 시작: Node.js를 사용하여 지원되는 언어 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)가 필요합니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="languages-request"></a>언어 요청

다음 코드는 [Languages](./reference/v3-0-languages.md) 메서드를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

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
let path = '/languages?api-version=3.0';

let output_path = 'output.txt';
let ws = fs.createWriteStream(output_path);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        ws.write (json);
        ws.close ();
        console.log("File written.");

    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let GetLanguages = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

GetLanguages ();
```

## <a name="languages-response"></a>언어 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>다음 단계

번역 및 음역을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=javascript)
