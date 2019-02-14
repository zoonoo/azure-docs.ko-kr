---
title: '빠른 시작: Bing Entity Search SDK, Python'
titlesuffix: Azure Cognitive Services
description: Bing Entity Search SDK 콘솔 애플리케이션을 설정합니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: a2d901c1cec4556e4061c21548d7a695c06e8cf8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861768"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>빠른 시작: Python을 사용하는 Bing Entity Search SDK

Python용 Bing Entity Search SDK를 사용하여 엔터티 검색을 시작하려면 이 빠른 시작을 사용하세요. Bing Entity Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Python [2.x 또는 3.x](https://www.python.org/)

* [Python용 Bing Entity Search SDK](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Python 가상 환경을 사용하는 것이 좋습니다. venv 모듈을 사용하여 가상 환경을 설치하고 초기화할 수 있습니다. 다음을 사용하여 virtualenv for Python 2.7을 설치할 수 있습니다.

```Console
python -m venv mytestenv
```

다음을 사용하여 Bing Entity Search SDK를 설치합니다.

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 다음 import 문을 추가합니다. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 구독 키에 대한 변수를 만들고, 해당 변수로 새 `CognitiveServicesCredentials` 개체를 만들어서 클라이언트를 인스턴스화합니다.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>검색 요청을 보내고 응답 수신

1. `client.entities.search()` 및 검색 쿼리를 사용하여 Bing Entity Search에 검색 요청을 보냅니다. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. 엔터티가 반환되는 경우 `entity_data.entities.value`를 목록으로 변환하고 첫 번째 결과를 인쇄합니다.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API란?](../overview.md )