---
title: '빠른 시작: Bing Spell Check REST API 및 Python으로 맞춤법 검사'
titlesuffix: Azure Cognitive Services
description: Bing Spell Check REST API를 사용하여 맞춤법 및 문법 검사를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: e74cc01d9104ed4f26d857f3a99eb21e312a7f59
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389742"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>빠른 시작: Bing Spell Check REST API 및 Python으로 맞춤법 검사

이 빠른 시작을 사용하여 Bing Spell Check REST API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 Python 애플리케이션은 API에 요청을 보내고 제안된 수정 사항을 반환합니다. 이 애플리케이션은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 애플리케이션의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 다음 import 문을 추가합니다.

   ```python
   import requests
   import json
   ```

2. 맞춤법 검사를 원하는 텍스트, 구독 키 및 Bing Spell Check 엔드포인트에 대한 변수를 만듭니다.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>요청에 대한 매개 변수 만들기

1. `text`를 키로 사용하고 사용자의 텍스트를 값으로 사용하여 새 사전을 만듭니다.

    ```python
    data = {'text': example_text}
    ```

2. 요청에 대한 매개 변수를 추가합니다. `mkt=` 뒤에 시장 코드를 추가합니다. 시장 코드는 요청을 수행한 국가입니다. 또한 `&mode=` 뒤에 맞춤법 검사 모드를 추가합니다. 모드는 `proof`(대부분의 맞춤법/문법 오류 catch) 또는 `spell`(대부분의 맞춤법은 catch하지만 문법 오류는 많지 않음)입니다.

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. `Content-Type` 헤더를 추가하고 `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가합니다.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>요청 보내기 및 응답 읽기

1. 요청 라이브러리를 사용하여 POST 요청을 보냅니다.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. JSON 응답을 받아서 출력합니다.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>예제 JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorials/spellcheck.md)

- [Bing Spell Check API란?](../overview.md)
- [Bing Spell Check API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
