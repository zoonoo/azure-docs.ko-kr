---
title: Python - Bing Custom Search - Microsoft Cognitive Services를 사용하여 엔드포인트 호출
description: 이 빠른 시작에서는 Python으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "35382956"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Bing Custom Search 엔드포인트 호출(Python)

이 빠른 시작에서는 Python으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 다음이 필요합니다.

- 사용자 지정 검색 인스턴스. [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.

-  설치된 [Python](https://www.python.org/).

- [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 및 **Bing Search API**. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다. 

## <a name="run-the-code"></a>코드 실행

Bing Custom Search 엔드포인트를 호출하려면 다음 단계를 수행합니다.

1. 코드 폴더를 만듭니다.
2. 관리자 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.
3. **요청** python 모듈을 설치합니다.
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. BingCustomSearch.py 파일을 만들고 다음 코드를 파일에 복사합니다.
8. **YOUR-SUBSCRIPTION-KEY** 및 **YOUR-CUSTOM-CONFIG-ID**를 해당 키 및 구성 ID로 바꿉니다(1단계 참조).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. 다음 명령을 사용하여 코드를 실행합니다.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>다음 단계
- [호스트된 UI 환경 구성](./hosted-ui.md)
- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)