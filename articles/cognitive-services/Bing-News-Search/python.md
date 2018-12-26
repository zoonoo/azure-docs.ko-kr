---
title: '빠른 시작: Python을 사용하여 뉴스 검색 수행 - Bing News Search REST API'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing News Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8ce8353df9a6f8354c56d9c9115645c0b7f2136a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251660"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>빠른 시작: Python 및 Bing News Search REST API를 사용하여 뉴스 검색 수행

이 연습에서는 Bing News Search API를 호출하고 결과 JSON 개체를 사후 처리하는 간단한 예를 보여 줍니다. 자세한 내용은 [Bing News Search 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)를 참조하세요.  

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>필수 조건

**Bing Search API**를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요합니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="running-the-walkthrough"></a>연습 실행
먼저 Bing API 서비스에 대한 API 키에 `subscription_key`를 설정합니다.


```python
subscription_key = None
assert subscription_key
```

다음으로, `search_url` 엔드포인트가 올바른지 확인합니다. 이 문서 작성 시 Bing Search API에 대해 하나의 엔드포인트만 사용됩니다. 권한 부여 오류가 발생하는 경우 Azure 대시보드에서 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Microsoft에 대한 뉴스 기사를 찾아보려면 `search_term`을 설정합니다.


```python
search_term = "Microsoft"
```

다음 블록은 Python에서 `requests` 라이브러리를 사용하여 Bing 검색 API를 호출하고 JSON 개체로 결과를 반환합니다. `headers` 사전을 통해 API 키를 전달하고 `params` 사전을 통해 검색 용어를 전달합니다. 검색 결과를 필터링하는 데 사용할 수 있는 옵션의 전체 목록을 보려면 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) 설명서를 참조합니다.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 개체에는 풍부한 메타데이터와 함께 관련 뉴스 기사가 포함됩니다. 예를 들어 다음 코드 줄은 기사에 대한 설명을 추출합니다.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

이러한 설명은 **굵게** 강조 표시된 검색 키워드를 사용하여 테이블로 렌더링될 수 있습니다.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [뉴스 페이징](paging-news.md)
> [장식 표식을 사용하여 텍스트 강조 표시](hit-highlighting.md)

## <a name="see-also"></a>참고 항목 

 [웹에서 뉴스 검색](search-the-web.md)  
 [사용해 보세요](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
