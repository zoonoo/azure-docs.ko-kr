---
title: '빠른 시작: 이미지 검색 - Bing Image Search REST API 및 Python'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing Image Search REST API로 이미지 검색 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 59c7a85ae564981029163bf99a8e44e72237f709
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383648"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>빠른 시작: Bing Image Search REST API 및 Python을 사용하여 이미지 검색

이 빠른 시작을 사용하여 검색 요청을 Bing Image Search API로 보낼 수 있습니다. 이 Python 애플리케이션에서는 검색 쿼리를 API로 보내고 결과에 첫 번째 이미지의 URL을 표시합니다. 이 애플리케이션은 Python으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py)에서 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

* [Python 2.x 또는 3.x](https://www.python.org/)
* [PIL(Python Imaging Library)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨찾는 IDE 또는 편집기에서 새 Python 파일을 만들고 다음 모듈을 가져옵니다. 구독 키, 검색 엔드포인트 및 검색 용어에 대한 변수를 만듭니다.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. 사전을 만들고 키를 값으로 추가하여 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>검색 요청 만들기 및 보내기

1. 검색 요청의 매개 변수에 대한 사전을 만듭니다. `q` 매개 변수에 검색 용어를 추가합니다. 공용 도메인에서 이미지를 검색하려면 `license` 매개 변수에 대해 "public"을 사용합니다. 사진만 검색하려면 `imageType`에 대해 "photo"를 사용합니다.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. `requests` 라이브러리를 사용하여 Bing Image Search API를 호출합니다. 헤더와 매개 변수를 요청에 추가하고 응답을 JSON 개체로 반환합니다. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>응답 보기

1. matplotlib 라이브러리를 사용하여 4개의 열과 4개의 행을 사용하여 새 그림을 만듭니다. 

2. 그림의 행과 열을 반복하고, PIL 라이브러리의 `Image.open()` 메서드를 사용하여 이미지 썸네일을 각 공간에 추가합니다. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. `plt.show()`를 사용하여 그림을 그리고 이미지를 표시합니다.

## <a name="example-json-response"></a>예제 JSON 응답

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
    }]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../tutorial-bing-image-search-single-page-app.md)

* [Bing Image Search API란?](../overview.md)  
* Bing Search API에 대한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [무료 Cognitive Services 액세스 키 가져오기](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
