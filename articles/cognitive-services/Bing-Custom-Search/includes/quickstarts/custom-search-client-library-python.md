---
title: Bing Custom Search Python 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 92df45e38a926b4a791315f731e1d1cb078d5852
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371597"
---
Python용 Bing Custom Search 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Bing Custom Search API를 사용하면 관심 있는 토픽에 대한 맞춤형 광고 없는 검색 경험을 만들 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)에서 확인할 수 있습니다.

Python용 Bing Custom Search 클라이언트 라이브러리를 사용하여 다음을 수행합니다.
* Bing Custom Search 인스턴스에서 웹의 검색 결과를 찾습니다.

[참조 설명서](/python/api/azure-cognitiveservices-search-customsearch/?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [패키지(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>사전 요구 사항

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](../../quick-start.md)에서 자세한 내용을 참조하세요.
- Python [2.x 또는 3.x](https://www.python.org/) 
- [Python용 Bing Custom Search SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Python 클라이언트 라이브러리 설치

다음 명령을 사용하여 Bing Custom Search 클라이언트 라이브러리를 설치합니다.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>새 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 새 Python 파일을 만들고, 다음 가져오기를 추가합니다.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>검색 클라이언트 만들기 및 요청 보내기

1. 구독 키 및 엔드포인트에 대한 변수를 만듭니다.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. 구독 키와 `CognitiveServicesCredentials` 개체를 사용하여 `CustomSearchClient` 인스턴스를 만듭니다. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. `client.custom_instance.search()`를 사용하여 검색 요청을 보냅니다. `query` 매개 변수에 검색 용어를 추가하고 `custom_config`를 검색 인스턴스를 사용하기 위한 사용자 지정 구성 ID로 설정합니다. **프로덕션** 탭을 클릭하여 [Bing Custom Search 포털](https://www.customsearch.ai/)에서 ID를 가져올 수 있습니다.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>검색 결과 보기

웹 페이지 검색 결과가 발견되면 첫 번째 결과를 가져온 후 해당 이름, URL 및 찾은 전체 웹 페이지를 인쇄합니다.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](../../tutorials/custom-search-web-page.md)