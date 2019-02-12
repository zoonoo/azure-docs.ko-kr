---
title: '빠른 시작: Python용 Bing Video Search SDK를 사용하여 비디오 검색'
titleSuffix: Azure Cognitive Services
description: Python용 Bing Video Search SDK로 비디오 검색 요청을 보내려면 이 빠른 시작을 사용합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ef3e92ce708f2dfaeb38fcbb93dd0b34eb592032
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568023"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>빠른 시작: Python용 Bing Video Search SDK를 사용하여 비디오 검색 수행

Python용 Bing Video Search SDK를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing Video Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py)에서 확인할 수 있습니다. 자세한 주석 및 기능이 포함되어 있습니다.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>필수 조건

- [Python](https://www.python.org/) 2.x 또는 3.x
- Python용 Bing Video Search SDK

Python [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것이 좋습니다. [venv 모듈](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화할 수 있습니다. 다음을 사용하여 Python 2.7용 virtualenv를 설치합니다.

```console
python -m venv mytestenv
```
   
다음을 사용하여 Bing Video Search SDK를 설치합니다.

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 다음 import 문을 추가합니다. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. 구독 키에 대한 변수를 만듭니다. 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>검색 클라이언트 만들기

`CognitiveServicesCredentials`의 인스턴스를 만들고 클라이언트를 인스턴스화합니다.

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>검색 요청 보내기 및 응답 받기

1. 검색 쿼리와 함께 `client.videos.search()`를 사용하여 Bing Video Search API에 요청을 보내고 응답을 받습니다.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. 응답에 검색 결과가 포함되는 경우 첫 번째 응답을 가져와서 해당 ID, 이름 및 URL을 출력합니다.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

* [Bing Video Search API란?](../overview.md)
* [Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)