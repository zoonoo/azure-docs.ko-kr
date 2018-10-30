---
title: '빠른 시작: 기술 자료 게시 - REST, Python - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 REST 기반 빠른 시작에서는 테스트된 최신 버전의 기술 자료를 게시된 기술 자료를 나타내는 Azure Search 인덱스에 푸시하는 방법(KB 게시)을 안내합니다. 또한 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e58b344102eb900ffe41bb90e541258eb3b59286
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646817"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>빠른 시작: Python을 사용하여 QnA Maker 기술 자료 업데이트

이 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Search 인덱스에 푸시하고, 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [게시](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

1. 즐겨 찾는 IDE에서 새 Python 프로젝트를 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `key` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

## <a name="the-publish-a-knowledge-base-response"></a>기술 자료 게시 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

[QnA Maker 개요](../Overview/overview.md)