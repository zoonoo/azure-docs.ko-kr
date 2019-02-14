---
title: '빠른 시작: 뉴스 검색 수행 - Python용 Bing News Search SDK'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python용 Bing News Search SDK를 통해 뉴스를 검색하고 응답을 처리합니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 4b4706baa5148bba955a980b6184e0747f023066
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875096"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>빠른 시작: Python용 Bing News Search SDK로 뉴스 검색 수행

Python용 Bing News Search SDK를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing News Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Python](https://www.python.org/) 2.x 또는 3.x

python 개발에 대해 [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것이 좋습니다. [venv module](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화할 수 있습니다. Python 2.7용 virtualenv를 설치해야 합니다. 다음을 사용하여 가상 환경을 만들 수 있습니다.

```console
python -m venv mytestenv
```

이 명령을 사용하여 Bing News Search SDK 종속성을 설치할 수 있습니다.
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다. 구독 키 및 검색 용어에 대한 변수를 만듭니다.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>클라이언트 초기화 및 요청 보내기

1. `CognitiveServicesCredentials`의 인스턴스를 만듭니다. 클라이언트를 인스턴스화합니다.
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. News Search API에 검색 쿼리를 보내고, 응답을 저장합니다.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>응답 구문 분석

검색 결과가 발견되면 첫 번째 웹 페이지 결과를 출력합니다.

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
[단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)
