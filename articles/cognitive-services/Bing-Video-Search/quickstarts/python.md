---
title: '빠른 시작: Bing Video Search REST API 및 Python을 사용하여 비디오 검색'
titlesuffix: Azure Cognitive Services
description: Python을 통해 Bing Video Search REST API로 비디오 검색 요청을 보내려면 이 빠른 시작을 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 1ac4a2ce5027606706367b43eea759fb953994c6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876879"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>빠른 시작: Bing Video Search REST API 및 Python을 사용하여 비디오 검색

Bing Video Search API를 처음 호출하고 JSON 응답에서 검색 결과를 확인하려면 이 빠른 시작을 사용합니다. 이 간단한 Python 애플리케이션은 HTTP 비디오 검색 쿼리를 API에 보내고, 응답을 표시합니다. 이 애플리케이션은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플에 대한 소스 코드는 추가 오류 처리 및 코드 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py)에서 사용할 수 있습니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>필수 조건

* Python [2.x 또는 3.x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  구독 키, 검색 엔드포인트 및 검색어에 대한 변수를 만듭니다.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. 새 사전을 만들어 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가하여 헤더 문자열을 키에 연결합니다.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>요청 보내기

1. `params`라고 명명된 사전을 만들어 요청에 매개 변수를 추가합니다. 검색어를 `q` 매개 변수에 추가하고, 비디오 수에 5, 반환된 비디오의 가격 책정에 `free`, 비디오 길이에 `short`를 입력합니다.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. Python에서 `requests` 라이브러리를 사용하여 Bing Video Search API를 호출합니다. `headers` 및 `params` 사전을 사용하여 API 키 및 검색 매개 변수를 전달합니다.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. 반환된 비디오 중 하나를 보려면 `search_results` 개체에서 검색 결과를 가져옵니다. 결과의 `embedHtml` 속성을 `IFrame`에 삽입합니다.  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
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
> [단일 페이지 웹앱 빌드](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

 [Bing Video Search API란?](../overview.md)
