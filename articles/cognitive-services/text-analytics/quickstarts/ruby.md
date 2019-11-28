---
title: '빠른 시작: Ruby를 사용하여 텍스트 분석 API 호출'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 텍스트 분석 API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 924f3a9019d3ddac0b8f1f942028e78f9a5a432e
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286485"
---
# <a name="quickstart-using-ruby-to-call-the-text-analytics-cognitive-service"></a>빠른 시작: Ruby를 사용하여 텍스트 분석 Cognitive Service 호출
<a name="HOLTop"></a>

이 문서에서는  [Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) 및 Ruby를 사용하여 [언어 감지](#Detect), [감정 분석](#SentimentAnalysis), [핵심 구 추출](#KeyPhraseExtraction) 및 [연결된 엔터티 식별](#Entities)을 수행하는 방법을 보여 줍니다.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>언어 검색

언어 감지 API는 [언어 감지 메서드](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)를 사용하여 텍스트 문서의 언어를 감지합니다.

1. 리소스의 Azure 엔드포인트 및 구독 키에 대한 환경 변수 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 및 `TEXT_ANALYTICS_ENDPOINT`를 만듭니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 환경 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.
1. 즐겨 찾는 IDE에서 새 Ruby 프로젝트를 만듭니다.
1. 아래 제공된 코드를 추가합니다.
1. 프로그램을 실행합니다.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/languages'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**언어 감지 응답**

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>감정 분석

감정 분석 API는 [감정 메서드](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)를 사용하여 텍스트 레코드 집합의 감정을 감지합니다. 다음 예제에서는 두 개의 문서(영어 문서와 스페인어 문서 각 1개)에 점수를 매깁니다.

1. 리소스의 Azure 엔드포인트 및 구독 키에 대한 환경 변수 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 및 `TEXT_ANALYTICS_ENDPOINT`를 만듭니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.
1. 즐겨 찾는 IDE에서 새 Ruby 프로젝트를 만듭니다.
1. 아래 제공된 코드를 추가합니다.
1. 프로그램을 실행합니다.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/sentiment'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**감정 분석 응답**

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>핵심 구 추출

핵심 구 추출 API는 [핵심 구 메서드](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)를 사용하여 텍스트 문서에서 핵심 구를 추출합니다. 다음 예제에서는 영어 문서와 스페인어 문서 둘 다에서 핵심 구를 추출합니다.

1. 리소스의 Azure 엔드포인트 및 구독 키에 대한 환경 변수 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 및 `TEXT_ANALYTICS_ENDPOINT`를 만듭니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.
1. 즐겨 찾는 IDE에서 새 Ruby 프로젝트를 만듭니다.
1. 아래 제공된 코드를 추가합니다.
1. 프로그램을 실행합니다.


```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/keyPhrases'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**핵심 구 추출 응답**

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="entity-recognition"></a>엔터티 인식

엔터티 API는 [엔터티 메서드](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)를 사용하여 텍스트 문서에서 엔터티를 추출합니다. 다음 예제에서는 영어 문서의 엔터티를 식별합니다.

1. 리소스의 Azure 엔드포인트 및 구독 키에 대한 환경 변수 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 및 `TEXT_ANALYTICS_ENDPOINT`를 만듭니다. 애플리케이션 편집을 시작한 후 이러한 환경 변수를 만든 경우 변수에 액세스하기 위해 사용 중인 편집기, IDE 또는 셸을 닫았다가 다시 열어야 합니다.
1. 즐겨 찾는 IDE에서 새 Ruby 프로젝트를 만듭니다.
1. 아래 제공된 코드를 추가합니다.
1. 프로그램을 실행합니다.

```ruby
# encoding: UTF-8

require 'net/https'
require 'uri'
require 'json'

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

path = '/text/analytics/v2.1/entities'

uri = URI(endpoint + path)

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is an It company.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = subscription_key
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**엔터티 추출 응답**

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 및 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>참고 항목 

 [Text Analytics 개요](../overview.md)  
 [FAQ(질문과 대답)](../text-analytics-resource-faq.md)
