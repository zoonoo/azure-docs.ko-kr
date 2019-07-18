---
title: '빠른 시작: Bing Video Search REST API 및 Ruby를 사용하여 비디오 검색'
titlesuffix: Azure Cognitive Services
description: Ruby를 통해 Bing Video Search REST API로 비디오 검색 요청을 보내려면 이 빠른 시작을 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 2abab9db46c9ca5bf52ed613b28d698550adaf6a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447026"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-ruby"></a>빠른 시작: Bing Video Search REST API 및 Ruby를 사용하여 비디오 검색

Bing Video Search API를 처음 호출하고 JSON 응답에서 검색 결과를 확인하려면 이 빠른 시작을 사용합니다. 이 간단한 Ruby 애플리케이션은 HTTP 비디오 검색 쿼리를 API에 보내고, 응답을 표시합니다. 이 애플리케이션은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플에 대한 소스 코드는 추가 오류 처리 및 코드 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingVideoSearchv7.rb)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Ruby 2.4 이상

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 다음 패키지를 코드 파일로 가져옵니다.

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. API 엔드포인트, 비디오 API 검색 경로, 구독 키 및 검색어에 대한 변수를 만듭니다.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/videos/search"
    term = "kittens"
    accessKey = "your-subscription-key" 
    ```

## <a name="create-and-send-an-api-request"></a>API 요청 만들기 및 보내기

1. 마지막 단계에서 변수를 사용하여 요청에 대한 검색 URL의 형식을 지정합니다. URI 및 경로를 결합한 다음, `?q=` 매개 변수에 추가하기 전에 검색어를 URL로 인코딩합니다.

    ```ruby
    uri = URI(uri + path + "?q=" + URI.escape(term))
    ```

2. 전체 검색 URL을 요청에 추가하고 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다.
    
    ``` ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = accessKey
    ```

3. 요청을 보내고 응답을 저장합니다.
    
    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

## <a name="process-and-view-the-response"></a>응답 처리 및 보기

1. 응답을 받은 후에 JSON 응답을 출력할 수 있습니다.

    ```ruby
    puts JSON::pretty_generate(JSON(response.body))
    ```

## <a name="json-response"></a>JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

 [Bing Video Search API란?](../overview.md)

