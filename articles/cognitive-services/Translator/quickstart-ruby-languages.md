---
title: '빠른 시작: 지원되는 언어 가져오기, Ruby - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Ruby와 함께 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: erhopf
ms.openlocfilehash: 1b92adcf528a1ccd00983e6c0dd952fefab5dd7d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55975469"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-ruby"></a>빠른 시작: Translator Text REST API(Ruby)로 지원되는 언어 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 이상이 필요합니다.

## <a name="languages-request"></a>언어 요청

다음 코드는 [Languages](./reference/v3-0-languages.md) 메서드를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

1. 원하는 코드 편집기에서 Ruby 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. 프로그램을 실행합니다.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

# Write response to file
File.open(output_path, 'w' ) do |output|
    output.print json
end
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
> [GitHub에서 Ruby 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=ruby)
