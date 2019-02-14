---
title: '빠른 시작: Python을 사용하여 뉴스 검색 수행 - Bing News Search REST API'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing News Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f6ae2315c14a92d72d2efa03b96397512120ab06
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866239"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>빠른 시작: Python 및 Bing News Search REST API를 사용하여 뉴스 검색 수행

이 빠른 시작을 사용하여 Bing News Search API를 처음 호출하고 JSON 응답을 받습니다. 이 간단한 JavaScript 애플리케이션은 검색 쿼리를 API에 보내고 결과를 처리합니다. 이 애플리케이션은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 코드 샘플을 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py)에서도 제공됩니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 요청 모듈을 가져옵니다. 구독 키, 엔드포인트 및 검색 용어에 대한 변수를 만듭니다. Azure 대시보드에서 엔드포인트를 찾을 수 있습니다.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>요청에 대한 매개 변수 만들기

1. 키로 `"Ocp-Apim-Subscription-Key"`를 사용하여 구독 키를 새 사전에 추가합니다. 검색 매개 변수에 대해 동일한 작업을 수행합니다.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>요청 보내기 및 응답 받기

1. 요청 라이브러리를 사용하여 사용자의 구독 키 및 마지막 단계에서 만든 사전 개체를 통해 Bing Visual Search API를 호출합니다.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`는 JSON 개체로 API의 응답을 포함합니다. 응답에 포함된 문서의 설명에 액세스합니다.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>결과 표시

이러한 설명은 **굵게** 강조 표시된 검색 키워드를 사용하여 테이블로 렌더링될 수 있습니다.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
[단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)
