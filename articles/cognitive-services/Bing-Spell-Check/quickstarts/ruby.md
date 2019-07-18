---
title: '빠른 시작: Bing Spell Check REST API 및 Ruby로 맞춤법 검사'
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
ms.openlocfilehash: a3e65f9ad8e8a9c6876d1588ecaa94531206c6f4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389699"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>빠른 시작: Bing Spell Check REST API 및 Ruby로 맞춤법 검사

이 빠른 시작을 통해 Ruby를 사용하여 Bing Spell Check REST API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 애플리케이션은 API에 요청을 보내고 인식하지 못한 단어 목록과 제안된 수정 사항을 반환합니다. 이 애플리케이션은 Ruby에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 애플리케이션의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 이상.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 편집기 또는 IDE에서 새 Ruby 파일을 만들고, 다음 요구 사항을 추가합니다. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. 구독 키, 엔드포인트 URI 및 경로에 대한 변수를 만듭니다. `mkt=` 매개 변수를 지역/국가에 추가하고 `&mode`를 `proof` 증명 모드에 추가하여 요청 매개 변수를 만듭니다.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>맞춤법 검사 요청 보내기

1. 호스트 URI, 경로 및 매개 변수 문자열을 통해 URI를 만듭니다. 맞춤법을 검사할 텍스트가 포함되도록 쿼리를 설정합니다.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. 위에 생성된 URI를 사용하여 요청을 만듭니다. `Ocp-Apim-Subscription-Key` 헤더에 키를 추가합니다.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. 요청을 보냅니다.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. JSON 응답을 받아서 콘솔에 출력합니다. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
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
