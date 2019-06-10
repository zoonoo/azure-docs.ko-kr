---
title: Bing Image Search API로 반환된 이미지 페이징
titleSuffix: Azure Cognitive Services
description: Bing Image Search API로 반환된 이미지의 여러 페이지를 이동합니다.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 9368abe7d3b6ad6cf6e86b503dca4fca4f18739c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388468"
---
# <a name="page-through-the-images-results"></a>이미지 결과 페이징

Image Search API를 호출하는 경우 Bing은 결과 목록을 반환합니다. 목록은 쿼리와 관련된 결과의 총 수의 하위 집합입니다. 예상되는 총 사용 가능한 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#totalestimatedmatches) 필드에 액세스해야 합니다.  

다음 예제는 이미지 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

사용 가능한 이미지를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  

`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 150개입니다. 기본값은 35입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  

페이지당 20개의 이미지를 표시하려면 `count`를 20으로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 다음은 오프셋 40에서 시작하여 이미지 20개를 요청하는 예제입니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

기본 `count` 값을 구현에 사용하는 경우 `offset` 쿼리 매개 변수를 지정하기만 하면 됩니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

이미지 35개를 한 번에 페이징하는 경우 첫 번째 요청에서 `offset` 쿼리 매개 변수를 0으로 설정한 다음, 후속 요청마다 `offset`을 35씩 증가시키는 것을 예상할 수 있습니다. 그러나 후속 응답의 결과 중 일부는 이전 응답과 중복될 수 있습니다. 예를 들어 응답의 처음 두 이미지는 이전 응답의 마지막 두 이미지와 동일할 수 있습니다.

중복되는 결과를 제거하려면 `Images` 개체의 [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#nextoffset) 필드를 사용하면 됩니다. `nextOffset` 필드는 다음 요청에 사용할 `offset`을 알려줍니다. 예를 들어 한 번에 30개 이미지를 페이징하려면 첫 번째 요청에서 `count`를 30으로, `offset`을 0으로 설정합니다. 다음 요청에서, `count`를 30으로 설정하고 `offset`을 이전 응답의 `nextOffset` 값으로 설정합니다. 뒤로 페이징하려면 이전 오프셋의 스택을 유지하고 가장 최근의 오프셋을 꺼내는 것이 좋습니다.

> [!NOTE]
> 페이징은 이미지 검색(/images/search)에만 적용되며 이미지 인사이트 또는 최신 이미지(/images/trending)에는 적용되지 않습니다.

> [!NOTE]
> `TotalEstimatedAnswers` 필드는 현재 쿼리에 대한 검색할 수 있는 검색 결과의 총 예측치입니다.  `count` 및 `offset` 매개 변수를 설정한 경우 `TotalEstimatedAnswers` 번호가 변경될 수 있습니다. 
