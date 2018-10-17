---
title: '빠른 시작: Bing Spell Check API'
titlesuffix: Azure Cognitive Services
description: Bing Spell Check API 사용을 시작하는 방법을 보여줍니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 29ee7cb4ee648d20b425939553ba31cd9ac150f0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804194"
---
# <a name="quickstart-your-first-spell-check-request"></a>빠른 시작: 첫 번째 맞춤법 검사 요청

첫 번째 호출을 수행하기 전에 먼저 Cognitive Services 구독 키를 가져와야 합니다. 키를 얻는 방법은 [Cognitive Services 체험하기](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)를 참조하세요.

텍스트 문자열의 맞춤법 및 문법 오류를 검사하려면 다음 엔드포인트로 GET 요청을 보냅니다.  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> V7 미리 보기 엔드포인트:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
요청은 HTTPS 프로토콜을 사용해야 합니다.

모든 요청은 서버에서 시작되는 것이 좋습니다. 클라이언트 응용 프로그램의 일부로 키를 배포하면 제3자가 나쁜 목적을 갖고 응용 프로그램에 액세스할 가능성이 높아집니다. 또한 서버에서 호출을 만들면 향후 API 버전을 업그레이드하기 위한 단일 업그레이드 지점이 제공됩니다.

요청에서는 검증할 텍스트 문자열을 포함하고 있는 [텍스트](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) 쿼리 매개 변수를 지정해야 합니다. 선택 사항이지만, 요청에서 결과를 가져올 지역/국가를 식별하는 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) 쿼리 매개 변수도 지정해야 합니다. `mode` 같은 선택적 쿼리 매개 변수 목록은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters)를 참조하세요. 모든 쿼리 매개 변수 값은 URL로 인코드되어야 합니다.  
  
요청에서 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) 헤더를 지정해야 합니다. 선택 사항이지만, 다음 헤더도 지정하는 것이 좋습니다.  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

모든 요청 및 응답 헤더 목록은 [헤더](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers)를 참조하세요.

## <a name="the-request"></a>요청

다음은 모든 제안된 쿼리 매개 변수 및 헤더를 포함하는 요청을 보여줍니다. Bing API 중 하나를 처음으로 호출하는 경우 클라이언트 ID 헤더를 포함하지 마세요. 이전에 Bing API를 호출했고 Bing이 사용자 및 장치 조합에 대한 클라이언트 ID를 반환한 경우 클라이언트 ID만 포함하세요. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> V7 미리 보기 요청:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

다음은 이전 쿼리에 대한 응답을 보여줍니다. 또한 이 예제는 Bing 관련 응답 헤더를 보여 줍니다.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

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


## <a name="next-steps"></a>다음 단계

API를 사용해 봅니다. [Spell Check API 테스트 콘솔](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358)로 이동합니다. 

응답 개체 사용에 대한 자세한 내용은 [텍스트 문자열 맞춤법 검사](./proof-text.md)를 참조하세요.

