---
title: Image Search SDK Python 빠른 시작 | Microsoft Docs
description: Image Search SDK 콘솔 응용 프로그램을 설치합니다.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377894"
---
# <a name="image-search-sdk-python-quickstart"></a>Image Search SDK Python 빠른 시작

Bing Image Search SDK는 웹 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다. 

[Python Bing Image Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py)은 Git Hub에서 얻을 수 있습니다.

## <a name="application-dependencies"></a>응용 프로그램 종속성
Python이 없을 경우 설치하세요. SDK는 Python 2.7 3.3, 3.4, 3.5 및 3.6과 호환됩니다.

Python 개발에 대한 일반 권장 사항은 [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것입니다. [venv module](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화합니다. Python 2.7용 virtualenv를 설치해야 합니다.
```
python -m venv mytestenv
```
Bing Image Search SDK 종속성을 설치합니다.
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Image Search 클라이언트
*검색* 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. 가져오기를 추가합니다.
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
`CognitiveServicesCredentials`의 인스턴스를 만들고 클라이언트를 인스턴스화합니다.
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
쿼리(Yosemite)에 대한 이미지를 검색하고 애니메이트된 gif 및 넓은 가로 세로 비율 이미지를 필터링합니다. 결과의 수를 확인하고 첫 번째 결과의 인사이트 토큰, 축소판 그림 URL 및 웹 URL을 출력합니다.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
(Yosemite)에 대한 이미지를 검색하고 애니메이트된 gif 및 넓은 가로 세로 비율 이미지를 필터링합니다.  결과의 수를 확인합니다.  첫 번째 결과의 `insightsToken`, `thumbnail url` 및 `web url`을 출력합니다.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

최신 결과를 가져옵니다.
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>다음 단계

[Cognitive Services Python SDK 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


