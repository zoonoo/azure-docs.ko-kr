---
title: 호출 및 응답 - Azure Cognitive Services용 Python 빠른 시작, Bing Web Search API | Microsoft Docs
description: Azure의 Microsoft Cognitive Services에서 Bing Web Search API를 사용하여 신속하게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374143"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>호출 및 응답: Python에서 첫 번째 Bing Web Search 쿼리

Bing Web Search API는 사용자의 쿼리와 관련있다고 Bing이 확인하는 검색 결과를 반환하여 Bing.com/Search와 유사한 환경을 제공합니다. 결과에는 웹 페이지, 이미지, 비디오, 뉴스 및 엔터티와 함께 관련 검색 쿼리, 맞춤법 수정, 표준 시간대, 단위 변환, 번역 및 계산이 포함될 수 있습니다. 사용자가 얻는 결과의 종류는 사용자가 구독한 Bing Search API의 계층과 관련성에 따릅니다.

API에 대한 기술 세부 정보는 [API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)를 참조합니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>필수 조건
**Bing Search API**를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다.

## <a name="running-the-walkthrough"></a>연습 실행

Bing API 서비스에 대한 API 키에 `subscription_key`를 설정합니다.


```python
subscription_key = None
assert subscription_key
```

다음으로, `search_url` 엔드포인트가 올바른지 확인합니다. 이 문서 작성 시 Bing Search API에 대해 하나의 엔드포인트만 사용됩니다. 권한 부여 오류가 발생하는 경우 Azure 대시보드에서 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Microsoft Cognitive Services에 대해 Bing을 쿼리하려면 `search_term`을 설정합니다.


```python
search_term = "Microsoft Cognitive Services"
```

다음 블록은 Python에서 `requests` 라이브러리를 사용하여 Bing 검색 API를 호출하고 JSON 개체로 결과를 반환합니다. `headers` 사전을 통해 API 키를 전달하고 `params` 사전을 통해 검색 용어를 전달합니다. 검색 결과를 필터링하는 데 사용할 수 있는 옵션의 전체 목록을 보려면 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) 설명서를 참조합니다.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 개체에는 검색 결과와 함께 관련 쿼리 및 페이지 같은 풍부한 메타데이터가 포함됩니다. 다음 코드 줄은 쿼리에서 반환한 상단 페이지를 포맷합니다.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Web Search 단일 페이지 앱 자습서](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

[Bing Web Search 개요](../overview.md)  
[사용해 보세요](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
