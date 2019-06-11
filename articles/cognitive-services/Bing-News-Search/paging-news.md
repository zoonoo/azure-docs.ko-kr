---
title: Bing News Search 결과를 통해 페이징하는 방법
titlesuffix: Azure Cognitive Services
description: Bing News Search API가 반환하는 뉴스 기사를 통해 페이징하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1eab92dcc9c1890e82f9999e26e54378a3687c6d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390489"
---
# <a name="how-to-page-through-news-search-results"></a>뉴스 검색 결과를 통해 페이징하는 방법

News Search API를 호출하면 Bing이 쿼리와 관련된 결과 목록을 반환합니다. 예상되는 총 사용 가능한 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) 필드에 액세스해야 합니다.  
  
다음 예제는 뉴스 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
사용 가능한 문서를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
 

|매개 변수  |설명  |
|---------|---------|
|`count`     | 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 100개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.        |
|`offset`     | 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.          |

예를 들어 페이지당 20개의 문서를 표시하려면 `count`를 20으로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 20씩 증가합니다(예: 20, 40).  
  
다음은 오프셋 40에서 시작하여 뉴스 기사 20개를 요청하는 예제입니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
기본 `count` 값이 구현에서 작동하는 경우 다음 예제와 같이 `offset` 쿼리 매개 변수만 지정하면 됩니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> 페이징은 뉴스 검색(/news/search)에만 적용되고 추세 항목(/news/trendingtopics) 또는 뉴스 카테고리(/news)에는 적용되지 않습니다.

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 
