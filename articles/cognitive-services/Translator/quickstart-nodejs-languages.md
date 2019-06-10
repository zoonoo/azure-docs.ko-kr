---
title: '빠른 시작: 지원되는 언어 목록 가져오기, Node.js - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Node.js와 함께 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 1341307ef3868e5398f8ee6583c8bec9759832f5
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515153"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>빠른 시작: Translator Text API를 사용하여 Node.js를 사용하는 지원되는 언어 목록 가져오기

이 빠른 시작에서는 Node.js 및 Translator Text REST API를 사용하여 지원되는 언어의 목록을 반환하는 GET 요청을 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Node 8.12.x 이상](https://nodejs.org/en/)

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

선호하는 IDE 또는 편집기를 사용하여 새 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `get-languages.js`라는 파일의 프로젝트에 복사합니다.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `npm install request uuidv4`를 실행합니다.

이러한 모듈은 HTTP 요청을 생성하고 `'X-ClientTraceId'` 헤더의 고유 식별자를 만드는 데 필요합니다.

## <a name="configure-the-request"></a>요청 구성

요청 모듈을 통해 제공되는 `request()` 메서드를 사용하면 HTTP 메서드, URL, 요청 매개 변수, 헤더 및 JSON 본문을 `options` 개체로 전달할 수 있습니다. 이 코드 조각에서 다음 요청을 구성합니다.

>[!NOTE]
> 엔드포인트, 루트 및 요청 매개 변수에 대한 자세한 내용은 [Translator Text API 3.0: 언어](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)를 참조하세요.

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

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
node get-languages.js
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)에 있습니다.

## <a name="sample-response"></a>샘플 응답

국가/지역 약어는 이 [언어 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)에서 확인하세요.

이 샘플은 결과 조각을 표시하도록 잘렸습니다.

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

## <a name="clean-up-resources"></a>리소스 정리

구독 키를 프로그램으로 하드 코딩한 경우 이 빠른 시작을 마치면 구독 키를 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 예제 살펴보기](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>참고 항목

언어 검색뿐만 아니라 다음과 같은 Translator Text API 사용 방법을 알아봅니다.

* [텍스트 번역](quickstart-nodejs-translate.md)
* [텍스트 음역](quickstart-nodejs-transliterate.md)
* [입력으로 언어 식별](quickstart-nodejs-detect.md)
* [대체 번역 가져오기](quickstart-nodejs-dictionary.md)
* [입력으로 문장 길이 확인](quickstart-nodejs-sentences.md)
