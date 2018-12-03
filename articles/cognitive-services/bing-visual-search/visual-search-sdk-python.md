---
title: '빠른 시작: Bing Visual Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: Visual Search SDK Python 콘솔 응용 프로그램을 설치합니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9f2a6d9b75ccf704862d169b96ea1a1f2edb9815
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445689"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>빠른 시작: Bing Visual Search SDK Python

Bing Visual Search SDK는 웹 요청 및 구문 분석 결과에 대한 REST API의 기능을 사용합니다.
[Python Bing Visual Search SDK 소스 코드 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py)은 Git Hub에서 얻을 수 있습니다.

코드 시나리오에 대해서는 다음 머리글 아래에서 설명합니다.
* [Visual Search 클라이언트](#client)
* [전체 콘솔 응용 프로그램](#complete-console)
* [cropArea를 사용하여 이진 이미지 게시](#binary-crop)
* [KnowledgeRequest 매개 변수](#knowledge-req)
* [태그, 동작 및 actionType](#tags-actions)

## <a name="application-dependencies"></a>응용 프로그램 종속성
* 이 빠른 시작의 경우 [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/)에 표시된 대로 S9 가격 계층에서 구독을 시작해야 합니다. 

Azure Portal에서 구독을 시작하려면
1. `Search resources, services, and docs`라고 표시되는 Azure Portal의 맨 위에 있는 텍스트 상자에 'BingSearchV7'을 입력합니다.  
2. 드롭다운 목록의 Marketplace 아래에서 `Bing Search v7`을 선택합니다.
3. 새 리소스에 대해 `Name`을 입력합니다.
4. `Pay-As-You-Go` 구독을 선택합니다.
5. `S9` 가격 책정 계층을 선택합니다.
6. `Enable`을 클릭하여 구독을 시작합니다.
 
* Python이 없을 경우 설치하세요. SDK는 Python 2.7 3.3, 3.4, 3.5 및 3.6과 호환됩니다.
* Python 개발에 대한 일반 권장 사항은 [가상 환경](https://docs.python.org/3/tutorial/venv.html)을 사용하는 것입니다. [venv module](https://pypi.python.org/pypi/virtualenv)을 사용하여 가상 환경을 설치하고 초기화합니다. Python 2.7용 virtualenv를 설치합니다.
```
python -m venv mytestenv
```
Bing Visual Search SDK 종속성을 설치합니다.
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Visual Search 클라이언트
`VisualSearchAPI` 클라이언트의 인스턴스를 만들려면 다음 라이브러리를 가져옵니다.
```
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
subscriptionKey 문자열 값을 유효한 구독 키로 바꿉니다.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
그런 다음,클라이언트를 인스턴스화합니다.
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
클라이언트를 사용하여 이미지를 검색하고 결과를 구문 분석합니다.
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>전체 콘솔 응용 프로그램

다음 콘솔 응용 프로그램은 이전에 정의된 쿼리를 실행하고 결과를 구문 분석합니다.
```
import http.client, urllib.parse
import json
import os.path

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Bing Search 샘플에서는 SDK의 다양한 기능을 설명합니다.  다음 함수를 이전에 정의된 `VisualSrchSDK` 클래스에 추가합니다.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>cropArea를 사용하여 이진 이미지 게시

다음 코드는 cropArea 개체와 함께 게시 요청의 본문에 이진 이미지를 보냅니다.  그런 다음, imageInsightsToken, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

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
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>KnowledgeRequest 매개 변수

다음 코드는 \"site:pinterest.com\" 필터와 함께 `knowledgeRequest` 매개 변수에서 이미지 URL을 보냅니다. 그런 다음, `imageInsightsToken`, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>태그, 작업 및 actionType

다음 코드는 cropArea 개체와 함께 knowledgeRequest 매개 변수에서 이미지 정보 토큰을 보냅니다. 그런 다음, imageInsightsToken, 태그의 수, 작업의 수 및 첫 번째 actionType을 인쇄합니다.

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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

[Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)