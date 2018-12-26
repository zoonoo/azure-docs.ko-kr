---
title: '빠른 시작: 텍스트 스크립트 변환, Ruby - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Ruby와 함께 Translator Text API를 사용하여 하나의 스크립트에서 한 언어의 텍스트를 다른 언어로 변환합니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: erhopf
ms.openlocfilehash: d78586243b47563440f629689800ad618cb411d8
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647088"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-ruby"></a>빠른 시작: Translator Text REST API(Ruby)로 텍스트 음역

이 빠른 시작에서는 Translator Text API를 사용하여 한 언어의 텍스트를 다른 언어로 변환합니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 이상이 필요합니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="transliterate-request"></a>음역 요청

다음은 [Transliterate](./reference/v3-0-transliterate.md) 메서드를 사용하여 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.

1. 원하는 코드 편집기에서 Ruby 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = "&language=ja&fromScript=jpan&toScript=latn";

uri = URI (host + path + params)

# Transliterate "good afternoon".
text = 'こんにちは'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
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
> [GitHub에서 Ruby 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=ruby)
