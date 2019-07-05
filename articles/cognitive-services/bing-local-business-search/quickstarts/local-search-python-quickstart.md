---
title: 빠른 시작 - Python에서 Bing Local Business Search API에 쿼리 보내기 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 Python에서 Bing Local Business Search API를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: a8a21c0904bd750cdb59c03a36d23a8c32c9e8cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051538"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>빠른 시작: Python에서 Bing Local Business Search API에 쿼리 보내기

이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청 전송을 시작합니다. 이 샘플 애플리케이션은 Python에서 작성되었지만 API는 RESTful 웹 서비스로, HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다.

이 예제 애플리케이션은 검색 쿼리 `hotel in Bellevue`에 대한 API에서 로컬 응답 데이터를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

* [Python](https://www.python.org/) 2.x 또는 3.x
 
Bing API를 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)이면 충분합니다. 평가판에서 제공되는 액세스 키를 사용합니다.  [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="run-the-complete-application"></a>전체 애플리케이션 실행

다음 코드는 지역화된 결과를 가져옵니다. 다음 단계로 구현됩니다.
1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 쿼리 매개 변수를 지정합니다. 
3. 요청을 만들고 Ocp-Apim-Subscription-Key 헤더를 추가하는 검색 기능을 정의합니다.
4. Ocp-Apim-Subscription-Key 헤더를 설정합니다. 
5. 연결하고 요청을 보냅니다.
6. JSON 결과를 인쇄합니다.

이 데모의 전체 코드는 다음과 같습니다.

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>다음 단계
- [Local Business Search Java 빠른 시작](local-search-java-quickstart.md)
- [Local Business Search C# 빠른 시작](local-quickstart.md)
- [Local Business Search Node 빠른 시작](local-search-node-quickstart.md)
