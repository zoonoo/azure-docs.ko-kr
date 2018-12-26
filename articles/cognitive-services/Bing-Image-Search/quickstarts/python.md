---
title: '빠른 시작: 이미지 검색 - Bing Image Search REST API 및 Python'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing Image Search REST API로 이미지 검색 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a93a044279cccd883de5f946bb236cad4b088ae2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261980"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>빠른 시작: Bing Image Search REST API 및 Python을 사용하여 이미지 검색

이 빠른 시작을 사용하여 Bing Image Search API를 처음 호출하고 JSON 응답을 받습니다. 이 간단한 Python 응용 프로그램은 검색 쿼리를 API에 보내고 원시 결과를 표시합니다.

이 응용 프로그램은 Python에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


또한 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

[Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="running-the-quickstart"></a>빠른 시작 실행

시작하려면 `subscription_key`를 Bing API 서비스의 유효한 구독 키로 설정합니다.

```python
subscription_key = None
assert subscription_key
```

다음으로, `search_url` 엔드포인트가 올바른지 확인합니다. 이 문서 작성 시 Bing Search API에 대해 하나의 엔드포인트만 사용됩니다. 권한 부여 오류가 발생하는 경우 Azure 대시보드에서 Bing 검색 엔드포인트에 대해 이 값을 다시 확인합니다.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

강아지의 이미지를 찾으려면 `search_term`을 설정합니다.


```python
search_term = "puppies"
```

다음 블록은 Python에서 `requests` 라이브러리를 사용하여 Bing 검색 API를 호출하고 JSON 개체로 결과를 반환합니다. `headers` 사전을 통해 API 키를 전달하고 `params` 사전을 통해 검색 용어를 전달합니다. 검색 결과를 필터링하는 데 사용할 수 있는 옵션의 전체 목록을 보려면 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 설명서를 참조합니다.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` 개체에는 실제 이미지와 함께 관련 항목 같은 풍부한 메타데이터가 포함됩니다. 예를 들어 다음 코드 줄은 처음 16개 결과에 대해 썸네일 URL을 추출할 수 있습니다.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

그런 다음, `PIL` 라이브러리를 사용하여 썸네일 이미지를 다운로드하고 `matplotlib` 라이브러리를 사용하여 썸네일 이미지를 4 X 4 그리드에 렌더링합니다.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
plt.show()
```

## <a name="sample-json-response"></a>샘플 JSON 응답

Bing Image Search API의 응답은 JSON으로 반환됩니다. 이 샘플 응답은 단일 결과를 표시하도록 잘렸습니다.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [무료 Cognitive Services 액세스 키 가져오기](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
