---
title: '빠른 시작: Bing Spell Check API, Python'
titlesuffix: Azure Cognitive Services
description: Bing Spell Check API를 사용하여 빠르게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 5aa67c0e582d64f258da7abd01a9492daaf91efd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882287"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>빠른 시작: Python을 통해 Bing Spell Check API 사용 

이 문서에서는 Python을 통해 [Bing Spell Check API](https://azure.microsoft.com/services/cognitive-services/spell-check/) 를 사용하는 방법을 보여줍니다. Spell Check API는 제안된 바꾸기와 함께 인식할 수 없는 단어 목록을 반환합니다. 일반적으로 텍스트를 이 API에 제출한 다음, 텍스트에서 제안된 바꾸기를 수행하거나 애플리케이션 사용자에게 표시하여 바꾸기 여부를 결정할 수 있습니다. 이 문서에서는 "Hello, wrld!" 텍스트가 포함된 요청을 보내는 방법을 보여 줍니다. 제안된 바꾸기는 "Hello" 및 "World"입니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Python 3.x](https://www.python.org/downloads/)가 필요합니다.

**Bing Spell Check API v7**을 사용하는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 있어야 합니다. 이 빠른 시작에는 [평가판](https://azure.microsoft.com/try/cognitive-services/#lang)이면 충분합니다. 평가판을 활성화할 때 제공된 액세스 키가 필요하며, Azure 대시보드에서 유료 구독 키를 사용해도 됩니다. [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)도 참조하세요.

## <a name="get-spell-check-results"></a>맞춤법 검사 결과 가져오기

1. 즐겨 찾는 IDE에서 새 Python 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```python
import http.client, urllib.parse, json

text = 'Hollo, wrld!'

data = {'text': text}

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

headers = {'Ocp-Apim-Subscription-Key': key,
'Content-Type': 'application/x-www-form-urlencoded'}

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>

conn = http.client.HTTPSConnection(host)
body = urllib.parse.urlencode (data)
conn.request ("POST", path + params, body, headers)
response = conn.getresponse ()
output = json.dumps(json.loads(response.read()), indent=4)
print (output)
```

**응답**

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Spell Check 자습서](../tutorials/spellcheck.md)

## <a name="see-also"></a>참고 항목

- [Bing Spell Check 개요](../proof-text.md)
- [Bing Spell Check API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
