---
title: '빠른 시작: Project URL Preview, Python'
titlesuffix: Azure Cognitive Services
description: Python을 통해 Project URL Preview를 사용하여 빠르게 시작하는 스크립트 샘플입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: fc684e4c17c437a6f2713628f35e3a2ab7aba241
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213654"
---
# <a name="quickstart-url-preview-with-python"></a>빠른 시작: Python을 통해 URL Preview 사용

다음 Python 예제에서는 SwiftKey 웹 사이트(https://swiftkey.com/en)에 대한 URL 미리 보기를 만듭니다.

## <a name="prerequisites"></a>필수 조건

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) 평가판에 대한 액세스 키 가져오기

이 예제에서는 Python 3.6을 사용합니다.

## <a name="code-scenario"></a>코드 시나리오 

다음 코드는 URL 미리 보기를 만듭니다.
다음 단계로 구현됩니다.
1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 미리 보기를 위한 쿼리 URL을 지정하고 쿼리 매개 변수를 추가합니다.  
3. 쿼리 매개 변수를 설정합니다.
4. 요청을 만들고 *Ocp-Apim-Subscription-Key* 헤더를 추가하는 검색 기능을 정의합니다.
5. *Ocp-Apim-Subscription-Key* 헤더를 설정합니다. 
6. 연결하고 요청을 보냅니다.
7. JSON 결과를 인쇄합니다.

이 데모의 전체 코드는 다음과 같습니다.

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](csharp.md)
- [Java 빠른 시작](java-quickstart.md)
- [JavaScript 빠른 시작](javascript.md)
- [Node URL 빠른 시작](node-quickstart.md)
