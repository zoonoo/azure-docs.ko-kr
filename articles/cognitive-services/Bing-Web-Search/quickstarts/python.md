---
title: '빠른 시작: Python을 사용하여 검색 수행 - Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing Web Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f26392bdac34dd831e04c772e5357f5e41fc746
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390221"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>빠른 시작: Python을 사용하여 Bing Web Search API 호출  

이 빠른 시작을 사용하여 Bing Web Search API를 처음 호출하고 JSON 응답을 받아보세요. 이 Python 애플리케이션은 검색 요청을 API에 보내고 응답을 표시합니다. 이 애플리케이션은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

이 예제는 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 실행됩니다. 바인더 배지 시작을 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>필수 조건

* [Python 2.x 또는 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>변수 정의

`subscription_key` 값을 Azure 계정의 유효한 구독 키로 바꿉니다.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Bing Web Search API 엔드포인트를 선언합니다. 권한 부여 오류가 발생하면 Azure 대시보드의 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

`search_term` 값을 바꿔 검색 쿼리를 사용자 지정할 수 있습니다.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>요청하기

다음 블록은 `requests` 라이브러리를 사용하여 Bing Web Search API를 호출하고 결과를 JSON 개체로 반환합니다. API 키는 `headers` 사전에 전달되고, 검색 용어와 쿼리 매개 변수는 `params` 사전에 전달됩니다. 옵션 및 매개 변수의 전체 목록은 [Bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 설명서를 참조하세요.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>응답 형식 지정 및 표시

`search_results` 개체에는 관련 쿼리 및 페이지와 같은 검색 결과와 메타데이터가 포함됩니다. 이 코드는 `IPython.display` 라이브러리를 사용하여 응답 형식을 지정하고 브라우저에 해당 응답을 표시합니다.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub의 샘플 코드

이 코드를 로컬로 실행하려면 [GitHub에 있는 완전한 샘플을 사용할 수 있습니다](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Web Search 단일 페이지 앱 자습서](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
