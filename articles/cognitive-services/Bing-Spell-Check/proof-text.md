---
title: Bing Spell Check API란?
titlesuffix: Azure Cognitive Services
description: Bing Spell Check API는 문맥에 맞는 맞춤법 검사를 위해 기계 학습 및 통계 기계 번역을 사용합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 81c80ab6c8d10d263de96566f5554709a2404a24
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802596"
---
# <a name="what-is-bing-spell-check-api"></a>Bing Spell Check API란?

Bing Spell Check API를 사용하면 문맥에 맞는 문법 및 맞춤법 검사를 수행할 수 있습니다.

일반 맞춤법 검사기와 Bing의 3세대 맞춤법 검사기의 차이점은 무엇일까요? 현재 맞춤법 검사기는 정기적으로 업데이트되고 확장되는 사전 기반 규칙 집합에 대해 맞춤법 및 문법을 확인하는 데 사용됩니다. 즉 맞춤법 검사기는 지원하는 사전 및 사전을 지원하는 편집 직원만큼 강력합니다. Microsoft Word 및 기타 워드 프로세서를 통해 이러한 종류의 맞춤법 검사기를 알고 있습니다.

반면, Bing은 기계 학습 및 통계 기계 번역을 활용하여 지속적으로 진화하고 항상 문맥에 맞는 알고리즘을 동적으로 학습하는 웹 기반 맞춤법 검사기를 개발했습니다. 맞춤법 검사기는 웹 검색 및 문서의 대규모 언어 자료를 기반으로 합니다.

이 맞춤법 검사기는 다음과 같이 모든 워드 프로세싱 시나리오를 처리할 수 있습니다.

- 은어와 일상 회화를 인식합니다.
- 컨텍스트에서 일반적인 이름 오류를 인식합니다.
- 단일 플래그를 사용하여 단어 분리 문제를 해결합니다.
- 다른 검사기에서 오류를 찾아내기가 어려운 동음이의어를 문맥에 맞게 교정할 수 있습니다.
- 새 브랜드, 디지털 엔터테인먼트 및 인기 있는 표현이 나타날 때 이를 지원합니다.
- 소리는 같지만 의미와 철자가 다른 단어(예: "see"과 "sea")를 지원합니다.

## <a name="spell-check-modes"></a>맞춤법 검사 모드

API에서 지원하는 두 가지 교정 모드는 `Proof` 및 `Spell`입니다.  [여기](https://azure.microsoft.com/en-us/services/cognitive-services/spell-check/)에 있는 예제를 사용해 보세요.
### <a name="proof---for-documents-scenario"></a>Proof(교정) - 문서 시나리오용
기본 모드는 `Proof`입니다. `Proof` 맞춤법 모드는 대문자 표시, 기본 문장 부호 및 다른 기능을 추가하여 문서 작성에 유용한 가장 포괄적인 검사를 제공합니다. 그러나 이는 en-US(미국 영어), es-ES(스페인어), pt-BR(포르투갈어) 시장에서만 사용할 수 있습니다(참고: 스페인어 및 포르투갈어의 경우만 베타 버전임). 다른 모든 시장의 경우 모드 쿼리 매개 변수를 [맞춤법 검사]로 설정합니다. 
<br /><br/>**참고:** 쿼리 텍스트의 길이가 4,096자를 초과하면 4,096자까지 자른 텍스트를 처리합니다. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell(맞춤법 검사) - 웹 검색/쿼리 시나리오용
`Spell`은 더 나은 검색 결과를 더 적극적으로 반환할 수 있습니다. `Spell` 모드는 대부분의 맞춤법 오류를 찾지만, `Proof`에서 포착하는 문법 오류(예: 대문자 표시 및 반복 단어)는 찾지 못합니다.
<br /></br>**참고:** 지원되는 최대 쿼리 길이는 다음과 같습니다. 쿼리에서 한도를 초과하면 쿼리가 변경되지 않았음을 보여 주는 결과가 나타납니다.
<ul><li>en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko 언어 코드의 경우 130자입니다. </li>
<li>언어 코드의 경우 65자입니다.</li></ul>

## <a name="market-setting"></a>시장 설정
시장은 요청 URL의 쿼리 매개 변수에 지정되어야 합니다. 그렇지 않으면 맞춤법 검사기에서 IP 주소를 기반으로 기본 시장을 사용합니다.


## <a name="post-vs-get"></a>POST 및 GET

API는 HTTP POST 또는 HTTP GET을 지원합니다. 교정하려는 텍스트의 길이에 따라 둘 중 하나를 사용합니다. 문자열이 항상 1,500자 미만인 경우 GET을 사용합니다. 그러나 문자열을 최대 10,000자까지 지원하려면 POST를 사용합니다. 텍스트 문자열에는 유효한 UTF-8 문자가 모두 포함될 수 있습니다.

다음 예제에서는 텍스트 문자열의 맞춤법 및 문법을 검사하기 위한 POST 요청을 보여 줍니다. 이 예제에는 완성도에 대한 [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) 쿼리 매개 변수가 포함되어 있습니다(`mode`가 기본적으로 Proof로 설정되므로 생략되었을 수도 있음). [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) 쿼리 매개 변수에는 교정할 문자열이 포함됩니다.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

HTTP GET을 사용하는 경우 URL의 쿼리 문자열에 `text` 쿼리 매개 변수를 포함합니다.
  
다음은 이전 요청에 대한 응답을 보여줍니다. 응답에는 [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck) 개체가 포함됩니다. 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
[flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) 필드에는 API를 통해 [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) 문자열에서 찾은 맞춤법 및 문법 오류가 나열됩니다. `token` 필드에는 바꿀 단어가 포함됩니다. `offset` 필드의 0부터 시작하는 오프셋을 사용하여 `text` 문자열의 토큰을 찾습니다. 그런 다음, 해당 위치의 단어를 `suggestion` 필드의 단어로 바꿉니다. 

`type` 필드가 RepeatedToken인 경우에도 토큰을 `suggestion`으로 바꾸지만 후행 공백을 제거해야 할 수도 있습니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 빠르게 시작하려면 [첫 번째 요청 수행](quickstarts/csharp.md)을 참조하세요.

[Bing Spell Check API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)를 숙지합니다. 참조에는 검색 결과를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수의 목록과 응답 개체의 정의가 포함되어 있습니다. 

결과 사용에 관한 규칙을 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)을 반드시 참조하세요.
