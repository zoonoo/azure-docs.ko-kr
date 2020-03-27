---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: eb6c8164cc577af6023c64112f09f36a2f37fa05
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "69907089"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

즐겨찾는 IDE 또는 편집기를 사용하여 새 프로젝트를 만들거나 데스크톱에 새 폴더를 만듭니다. 이 코드 조각을 `alt-translations.js`라는 파일의 프로젝트/폴더에 복사합니다.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `npm install request uuidv4`를 실행합니다.

이러한 모듈은 HTTP 요청을 생성하고 `'X-ClientTraceId'` 헤더의 고유 식별자를 만드는 데 필요합니다.

## <a name="set-the-subscription-key-and-endpoint"></a>구독 키 및 엔드포인트 설정

이 샘플에서는 `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` 및 `TRANSLATOR_TEXT_ENDPOINT` 환경 변수에서 Translator Text 구독 키와 엔드포인트를 읽으려고 합니다. 환경 변수에 익숙하지 않은 경우 `subscriptionKey` 및 `endpoint`를 문자열로 설정하고 조건문을 주석으로 처리할 수 있습니다.

이 코드를 프로젝트에 복사합니다.

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>요청 구성

요청 모듈을 통해 제공되는 `request()` 메서드를 사용하면 HTTP 메서드, URL, 요청 매개 변수, 헤더 및 JSON 본문을 `options` 개체로 전달할 수 있습니다. 이 코드 조각에서 다음 요청을 구성합니다.

>[!NOTE]
> 엔드포인트, 루트 및 요청 매개 변수에 대한 자세한 내용은 [Translator Text API 3.0: 사전 조회](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup)를 참조하세요.

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'dictionary/lookup',
    qs: {
      'api-version': '3.0',
      'from': 'en',
      'to': 'es'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Elephants'
    }],
    json: true,
};
```

요청을 인증하는 가장 쉬운 방법은 구독 키에서 `Ocp-Apim-Subscription-Key` 헤더로 전달하는 것이며, 이 샘플에서 이 방법을 사용할 것입니다. 대안으로, 액세스 토큰에 구독 키를 대체하고 `Authorization` 헤더로서 액세스 토큰을 전달하여 요청의 유효성을 검사할 수 있습니다.

Cognitive Services 다중 서비스 구독을 사용하는 경우 요청 헤더에 `Ocp-Apim-Subscription-Region`도 포함해야 합니다.

자세한 내용은 [인증](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)을 참조하세요.

## <a name="make-the-request-and-print-the-response"></a>요청 만들기 및 응답 출력

다음으로, `request()` 메서드를 사용하여 요청을 만들겠습니다. 이전 섹션에서 첫 번째 인수로 만든 `options` 개체를 가져온 다음, prettified JSON 응답을 출력합니다.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> 이 샘플에서는 `options` 개체에 HTTP 요청을 정의합니다. 그러나 요청 모듈에서 `.post` 및 `.get` 같은 편의 메서드도 지원됩니다. 자세한 내용은 [편의 메서드](https://github.com/request/request#convenience-methods)를 참조하세요.

## <a name="put-it-all-together"></a>모든 요소 결합

이것으로, Translator Text API를 호출하여 JSON 응답을 반환하는 간단한 프로그램이 만들어집니다. 이제 프로그램을 실행해 보겠습니다.

```console
node alt-translations.js
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)에 있습니다.

## <a name="sample-response"></a>샘플 응답

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>리소스 정리

구독 키를 프로그램으로 하드 코딩한 경우 이 빠른 시작을 마치면 구독 키를 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계

Translator Text API로 할 수 있는 모든 것에 대해 알아보려면 API 참조를 살펴보세요.

> [!div class="nextstepaction"]
> [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
