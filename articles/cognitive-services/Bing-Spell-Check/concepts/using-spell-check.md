---
title: Bing Spell Check API 사용
titlesuffix: Azure Cognitive Services
description: Bing Spell Check 모드, 설정 및 API와 관련된 기타 정보에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: b847615e8440a8992c8130d12cd6111afe3d33d2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390234"
---
# <a name="using-the-bing-spell-check-api"></a>Bing Spell Check API 사용

이 문서를 통해 Bing Spell Check API를 사용하여 문맥 문법 및 맞춤법 검사를 수행하는 방법을 알아볼 수 있습니다. 대부분의 맞춤법 검사기는 사전 기반 규칙 세트에 의존하지만 Bing 맞춤법 검사기는 기계 학습 및 통계 기계 번역을 활용하여 정확한 문맥 교정을 제공합니다. 

## <a name="spell-check-modes"></a>맞춤법 검사 모드

API에서 지원하는 두 가지 교정 모드는 `Proof` 및 `Spell`입니다.  [여기](https://azure.microsoft.com/services/cognitive-services/spell-check/)에 있는 예제를 사용해 보세요.

### <a name="proof---for-documents"></a>Proof(교정) - 문서용 

기본 모드는 `Proof`입니다. `Proof` 맞춤법 모드는 대문자 표시, 기본 문장 부호 및 다른 기능을 추가하여 문서 작성에 유용한 가장 포괄적인 검사를 제공합니다. 그러나 이는 en-US(미국 영어), es-ES(스페인어), pt-BR(포르투갈어) 시장에서만 사용할 수 있습니다(참고: 스페인어 및 포르투갈어의 경우만 베타 버전임). 다른 모든 시장의 경우 모드 쿼리 매개 변수를 [맞춤법 검사]로 설정합니다. 

> [!NOTE]
> 쿼리 텍스트의 길이가 4,096자를 초과하면 4,096자까지 자른 텍스트를 처리합니다. 

### <a name="spell----for-web-searchesqueries"></a>Spell(맞춤법 검사) - 웹 검색/쿼리용

`Spell`은 더 나은 검색 결과를 더 적극적으로 반환할 수 있습니다. `Spell` 모드는 대부분의 맞춤법 오류를 찾지만, `Proof`에서 포착하는 문법 오류(예: 대문자 표시 및 반복 단어)는 찾지 못합니다.

> [!NOTE]
> * 지원되는 최대 쿼리 길이는 다음과 같습니다. 쿼리가 최대 길이를 초과할 경우 쿼리 및 해당 결과가 변경되지 않습니다.
>    * en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko 언어 코드의 경우 130자입니다. 
>    * 그 외 모든 언어 코드는 65자입니다.
> * 맞춤법 검사 모드는 쿼리에 대괄호 문자(`[` 및 `]`)를 지원하지 않으며 일치하지 않는 결과가 발생할 수 있습니다. 맞춤법 검사 모드를 사용할 때는 쿼리에서 제거하는 것이 좋습니다.

## <a name="market-setting"></a>시장 설정

[지역/국가 코드](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes)는 요청에 `mkt` 쿼리 매개 변수로 지정해야 합니다. 그렇지 않으면, API는 요청의 IP 주소를 기반으로 기본 지역/국가를 사용합니다.


## <a name="http-post-and-get-support"></a>HTTP POST 및 GET 지원

API는 HTTP POST 또는 HTTP GET을 지원합니다. 교정하려는 텍스트의 길이에 따라 둘 중 하나를 사용합니다. 문자열이 항상 1,500자 미만인 경우 GET을 사용합니다. 그러나 문자열을 최대 10,000자까지 지원하려면 POST를 사용합니다. 텍스트 문자열에는 유효한 UTF-8 문자가 모두 포함될 수 있습니다.

다음 예제에서는 텍스트 문자열의 맞춤법 및 문법을 검사하기 위한 POST 요청을 보여 줍니다. 이 예제에는 완성도에 대한 [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) 쿼리 매개 변수가 포함되어 있습니다(`mode`가 기본적으로 Proof로 설정되므로 생략되었을 수도 있음). [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) 쿼리 매개 변수에는 교정할 문자열이 포함됩니다.
  
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
  
다음은 이전 요청에 대한 응답을 보여줍니다. 응답에는 [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) 개체가 포함됩니다. 
  
```json
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
  
[flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) 필드에는 API를 통해 [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) 문자열에서 찾은 맞춤법 및 문법 오류가 나열됩니다. `token` 필드에는 바꿀 단어가 포함됩니다. `offset` 필드의 0부터 시작하는 오프셋을 사용하여 `text` 문자열의 토큰을 찾습니다. 그런 다음, 해당 위치의 단어를 `suggestion` 필드의 단어로 바꿉니다. 

`type` 필드가 RepeatedToken인 경우에도 토큰을 `suggestion`으로 바꾸지만 후행 공백을 제거해야 할 수도 있습니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

- [Bing Spell Check API란?](../overview.md)
- [Bing Spell Check API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
