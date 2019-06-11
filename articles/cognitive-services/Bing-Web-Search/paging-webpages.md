---
title: 검색 결과를 통해 페이징하는 방법 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API의 검색 결과를 통해 페이징하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384801"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Bing Web Search API의 결과를 통해 페이징하는 방법

Web Search API를 호출하면 Bing이 결과 목록을 반환합니다. 이 목록은 쿼리와 관련된 총 결과 수의 하위 집합입니다. 예상되는 총 사용 가능한 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 필드에 액세스해야 합니다.  

다음 예제는 웹 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

사용 가능한 웹 페이지를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  

`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 50개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  

페이지당 15개의 이미지를 표시하려면 `count`를 15로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 15씩 증가합니다(예: 15, 30).  

다음은 오프셋 45에서 시작하여 웹 페이지 15개를 요청하는 예제입니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

기본 `count` 값이 구현에서 작동하는 경우 `offset` 쿼리 매개 변수를 지정하기만 하면 됩니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web Search API는 웹 페이지를 포함하는 결과를 반환하며 이미지, 비디오 및 뉴스를 포함할 수 있습니다. 검색 결과를 페이징하는 것은 이미지나 뉴스 같은 다른 응답이 아닌 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) 응답을 페이징하는 것입니다. 예를 들어 `count`를 50으로 설정한 경우 50개 웹 페이지가 표시되지만, 응답에 다른 답변에 대한 결과도 포함될 수 있습니다. 예를 들어 응답에 이미지 15개와 뉴스 기사 4개가 포함될 수 있습니다. 또한 결과의 첫 번째 페이지에는 뉴스가 있지만 두 번째 페이지에는 뉴스가 없는 경우 또는 그 반대의 경우가 발생할 수 있습니다.   

`responseFilter` 쿼리 매개 변수를 지정하고 필터 목록에 웹 페이지를 포함하지 않는 경우 `count` 및 `offset` 매개 변수를 사용하지 마세요. 

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Bing Web Search API 란](overview.md)?
