---
title: '빠른 시작: Bing Visual Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: Visual Search SDK Python 콘솔 애플리케이션을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 91fddb6da12817428fef009e8720a37534a64f24
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743615"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>빠른 시작: Bing Visual Search SDK for Python을 사용하여 이미지 인사이트 가져오기

이 빠른 시작의 안내에 따라 Python SDK를 사용하여 Bing Visual Search 서비스에서 이미지 인사이트 가져오기를 시작하세요. Bing Visual Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py)에서 확인할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* [Python](https://www.python.org/) 2.x 또는 3.x
* [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것이 좋습니다. [venv module](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화합니다. Python 2.7용 virtualenv를 설치합니다.
* Bing Visual Search SDK for Python. 다음 명령을 실행하여 설치할 수 있습니다.
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 다음 import 문을 추가합니다. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    ```
2. 구독 키, 사용자 지정 구성 ID 및 업로드할 이미지에 대한 변수를 만듭니다. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. 클라이언트 인스턴스화

    ```python
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"))
    ```

## <a name="send-the-search-request"></a>검색 요청 보내기

1. 이미지 파일을 연 상태에서 `VisualSearchRequest()`를 직렬화하여 `visual_search()`의 `knowledge_request` 매개 변수로 전달합니다.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. 반환된 결과가 있으면 결과, 태그 및 첫 번째 태그의 작업을 인쇄합니다.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](tutorial-bing-visual-search-single-page-app.md)