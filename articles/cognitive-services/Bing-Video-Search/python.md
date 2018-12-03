---
title: '빠른 시작: Bing Video Search, Python'
titlesuffix: Azure Cognitive Services
description: Bing Video Search API 사용을 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 확인합니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: e902ae940364810e634434fb1f08408fadea9c8a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310551"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>빠른 시작: Python을 사용한 Bing Video Search API

이 연습에서는 Azure에서 Microsoft Cognitive Services의 일부인 Bing Video Search API를 사용하는 방법을 보여줍니다. API에 대한 기술 세부 정보는 [API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)를 참조합니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. 

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>필수 조건
**Bing Search API**를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다. [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="running-the-walkthrough"></a>연습 실행

먼저 Bing API 서비스에 대한 API 키에 `subscription_key`를 설정합니다.


```python
subscription_key = None
assert subscription_key
```

다음으로, `search_url` 엔드포인트가 올바른지 확인합니다. 이 문서 작성 시 Bing Search API에 대해 하나의 엔드포인트만 사용됩니다. 권한 부여 오류가 발생하는 경우 Azure 대시보드에서 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

고양이 비디오를 검색하려면 `search_term` 설정


```python
search_term = "kittens"
```

다음 블록은 Python에서 `requests` 라이브러리를 사용하여 Bing 검색 API를 호출하고 JSON 개체로 결과를 반환합니다. `headers` 사전을 통해 API 키를 전달하고 `params` 사전을 통해 검색 용어를 전달합니다. 검색 결과를 필터링하는 데 사용할 수 있는 옵션의 전체 목록을 보려면 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 설명서를 참조합니다.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 개체에는 풍부한 메타데이터와 함께 관련 비디오가 포함됩니다. 비디오 중 하나를 보려면 해당 `embedHtml` 속성을 사용하여 `IFrame`에 삽입합니다.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비디오 페이징](paging-videos.md)
> [썸네일 이미지 크기 조정 및 자르기](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>참고 항목 

 [웹에서 비디오 검색](search-the-web.md) [체험해 보기](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
