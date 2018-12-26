---
title: '빠른 시작: Video Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: Video Search SDK 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/15/2018
ms.author: rosh
ms.openlocfilehash: 2c099f3520b697e8ec2f7ef39c4525298182a697
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307919"
---
# <a name="quickstart-bing-video-search-sdk-with-python"></a>빠른 시작: Python을 사용하여 Bing Video Search SDK

Bing Image Search SDK는 웹 쿼리 및 구문 분석 결과에 대한 REST API 기능을 포함하고 있습니다.

[Python Bing Video Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py)은 Git Hub에서 얻을 수 있습니다.


## <a name="application-dependencies"></a>응용 프로그램 종속성
**검색** 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

Python이 없을 경우 설치하세요. SDK는 Python 2.7 3.3, 3.4, 3.5 및 3.6과 호환됩니다.

Python 개발에 대한 일반 권장 사항은 [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것입니다. [venv module](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화합니다. Python 2.7용 virtualenv를 설치합니다.
```
python -m venv mytestenv
```
Bing Video Search SDK 종속성을 설치합니다.
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```
## <a name="video-search-client"></a>Video Search 클라이언트
*검색* 아래에서 [Cognitive Services 액세스 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다. 가져오기를 추가합니다.
```
subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
`CognitiveServicesCredentials`의 인스턴스를 만들고 클라이언트를 인스턴스화합니다.
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```
(SwiftKey)에 대한 비디오를 검색한 다음, 결과의 수를 확인합니다. 첫 번째 비디오 결과의 `ID`, `name` 및 `URL`을 출력합니다.
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

try:
    video_result = client.videos.search(query="SwiftKey")
    print("Search videos for query \"SwiftKey\"")

    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")

except Exception as err:
    print("Encountered exception. {}".format(err))

```
무료이고 짧고 1080p 해상도를 갖는 (Bellevue Trailer)에 대한 비디오를 검색합니다. 결과 수를 확인하고 첫 번째 비디오 결과의 `ID`, `name` 및 `URL`을 출력합니다.
```
def video_search_with_filtering(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(
            query="Bellevue Trailer",
            pricing=VideoPricing.free,  # Can use the str "free" too
            length=VideoLength.short,   # Can use the str "short" too
            resolution=VideoResolution.hd1080p  # Can use the str "hd1080p" too
        )
        print("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution")

        if video_result.value:
            first_video_result = video_result.value[0]
            print("Video result count: {}".format(len(video_result.value)))
            print("First video id: {}".format(first_video_result.video_id))
            print("First video name: {}".format(first_video_result.name))
            print("First video url: {}".format(first_video_result.content_url))
        else:
            print("Didn't see any video result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

최신 결과를 가져옵니다. 배너 타일 및 범주를 확인합니다.
```
def video_trending(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_result = client.videos.trending()
        print("Search trending video")

        # Banner tiles
        if trending_result.banner_tiles:
            first_banner_tile = trending_result.banner_tiles[0]
            print("Banner tile count: {}".format(len(trending_result.banner_tiles)))
            print("First banner tile text: {}".format(first_banner_tile.query.text))
            print("First banner tile url: {}".format(first_banner_tile.query.web_search_url))
        else:
            print("Couldn't find banner tiles!")

        # Categorires
        if trending_result.categories:
            first_category = trending_result.categories[0]
            print("Category count: {}".format(len(trending_result.categories)))
            print("First category title: {}".format(first_category.title))
            if first_category.subcategories:
                first_subcategory = first_category.subcategories[0]
                print("Subcategory count: {}".format(len(first_category.subcategories)))
                print("First subcategory title: {}".format(first_subcategory.title))
                if first_subcategory.tiles:
                    first_tile = first_subcategory.tiles[0]
                    print("Subcategory tile count: {}".format(len(first_subcategory.tiles)))
                    print("First tile text: {}".format(first_tile.query.text))
                    print("First tile url: {}".format(first_tile.query.web_search_url))
                else:
                    print("Couldn't find subcategory tiles!")
            else:
                print("Couldn't find subcategories!")
        else:
            print("Couldn't find categories!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
(Bellevue Trailer)에 대한 비디오를 검색하고 첫 번째 비디오에 대한 세부 정보를 검색합니다.
```
def video_detail(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(query="Bellevue Trailer")
        first_video_result = video_result.value[0]

        video_details = client.videos.details(
            query="Bellevue Trailer",
            id=first_video_result.video_id,
            modules=[VideoInsightModule.all]  # Can use ["all"] too
        )
        print("Search detail for video id={}, name={}".format(
            first_video_result.video_id,
            first_video_result.name
        ))

        if video_details.video_result:
            print("Expected Video id: {}".format(video_details.video_result.video_id))
            print("Expected Video name: {}".format(video_details.video_result.name))
            print("Expected Video url: {}".format(video_details.video_result.content_url))
        else:
            print("Couldn't find expected video")

        if video_details.related_videos.value:
            first_related_video = video_details.related_videos.value[0]
            print("Related video count: {}".format(len(video_details.related_videos.value)))
            print("First related video id: {}".format(first_related_video.video_id))
            print("First related video name: {}".format(first_related_video.name))
            print("First related video content url: {}".format(first_related_video.content_url))
        else:
            print("Couldn't find any related video!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>다음 단계

[Cognitive Services Python SDK 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

