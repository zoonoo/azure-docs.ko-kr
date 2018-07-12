---
title: Microsoft Cognitive Services, Project Answer Search에 대한 Python 빠른 시작 | Microsoft Docs
description: Azure의 Microsoft Cognitive Services인 Project Answer Search 사용을 시작하기 위한 Python 예제입니다.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376135"
---
# <a name="project-answer-search-python-quickstart"></a>Project Answer Search Python 빠른 시작

다음 Python 예제에서는 “Rock of Gibraltar”에 대한 정보 요청을 만들고 전송합니다.

## <a name="prerequisites"></a>필수 조건

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) 체험 평가판에 대한 액세스 키 가져오기

이 예제에서는 Python 3.6.4를 사용합니다.

## <a name="code-scenario"></a>코드 시나리오 

다음 코드는 URL 미리 보기를 만듭니다.
다음 단계로 구현됩니다.
1. 변수를 선언하여 호스트 및 경로별로 끝점을 지정합니다.
2. 미리 보기를 위한 쿼리 URL을 지정하고 쿼리 매개 변수를 추가합니다.  
3. 쿼리 매개 변수를 설정합니다.
4. 요청을 만들고 *Ocp-Apim-Subscription-Key* 헤더를 추가하는 검색 기능을 정의합니다.
5. *Ocp-Apim-Subscription-Key* 헤더를 설정합니다. 
6. 연결하고 요청을 보냅니다.
7. JSON 결과를 인쇄합니다.

이 데모의 전체 코드는 다음과 같습니다.

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````
## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](c-sharp-quickstart.md)
- [Java 빠른 시작](java-quickstart.md)
- [Node 빠른 시작](node-quickstart.md)