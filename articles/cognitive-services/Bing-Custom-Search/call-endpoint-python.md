---
title: '빠른 시작: Python을 사용하여 엔드포인트 호출 - Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: e3e4256d3654f532f16d33c77f4c7e8cb7e93dd4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162639"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>빠른 시작: Bing Custom Search 엔드포인트 호출(Python)

이 빠른 시작에서는 Python으로 Bing Custom Search 엔드포인트를 호출하여 사용자 지정 검색 인스턴스에서 검색 결과를 요청하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 바로 사용할 수 있는 사용자 지정 검색 인스턴스. [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.
- 설치된 [Python](https://www.python.org/).
- 구독 키 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)을 정품 인증하면 구독 키를 받을 수 있습니다. Azure 대시보드에서 유료 구독 키를 사용할 수도 있습니다([Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 참조).    


## <a name="run-the-code"></a>코드 실행

이 예제를 실행하려면 다음 단계를 수행합니다.

1. 코드 폴더를 만듭니다.  
  
2. 관리자 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.  
  
3. **요청** python 모듈을 설치합니다.  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. 앞에서 만든 폴더에 BingCustomSearch.py 파일을 만들고 다음 코드를 해당 파일에 복사합니다. **YOUR-SUBSCRIPTION-KEY** 및 **YOUR-CUSTOM-CONFIG-ID**는 해 구독 키와 구성 ID로 바꿉니다.  
  
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
  
7. 다음 명령을 사용하여 코드를 실행합니다.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>다음 단계
- [호스트된 UI 환경 구성](./hosted-ui.md)
- [장식 표식을 사용하여 텍스트를 강조 표시](./hit-highlighting.md)
- [웹 페이지 페이징](./page-webpages.md)
