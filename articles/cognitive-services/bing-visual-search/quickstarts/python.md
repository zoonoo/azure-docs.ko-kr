---
title: '빠른 시작: Bing Visual Search REST API 및 Python을 사용하여 이미지 인사이트 가져오기'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 873da64592c2c2e925d8731d4b1154db95bed31d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863230"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>빠른 시작: Python의 첫 번째 Bing Visual Search 쿼리

이 빠른 시작을 사용하여 Bing Visual Search API를 처음 호출하고 검색 결과를 확인합니다. 이 간단한 JavaScript 애플리케이션은 API에 이미지를 업로드하고, 이미지에 대한 반환된 정보를 표시합니다. 이 애플리케이션은 JavaScript에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

로컬 이미지를 업로드하는 경우 POST 영식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>필수 조건

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 다음 import 문을 추가합니다.

    ```python
    import requests, json
    ```

2. 구독 키, 엔드포인트 및 업로드할 이미지의 경로에 대한 변수를 만듭니다.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. 요청의 헤더 정보를 보관할 사전 개체를 만듭니다. 아래와 같이 구독 키를 문자열 `Ocp-Apim-Subscription-Key`에 바인딩합니다.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. 이미지를 포함할 또 다른 사전을 만듭니다. 이 사전은 사용자가 요청을 보낼 때 열리고 업로드됩니다. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>JSON 응답 구문 분석

1. API 응답에 사용할 `print_json()`이라는 메서드를 만들고 JSON을 인쇄합니다.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>요청 보내기

1. `requests.post()` 명령을 사용하여 Bing Visual Search API로 요청을 보냅니다. 엔드포인트, 헤더 및 파일 정보에 대한 문자열을 포함합니다. `print_json()`을 사용하여 `response.json()`을 인쇄합니다.

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)
