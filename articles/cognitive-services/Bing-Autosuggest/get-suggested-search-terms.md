---
title: Bing Autosuggest란?
titlesuffix: Azure Cognitive Services
description: Bing Autosuggest API 사용 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: e1e1ec2a9f5329f5d7331b7ce3ced4924d001a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174179"
---
# <a name="what-is-bing-autosuggest"></a>Bing Autosuggest란?

Bing Search API에 쿼리를 보내는 경우 Bing Autosuggest API를 사용하여 검색 상자 환경을 개선할 수 있습니다. Bing Autosuggest API는 사용자가 검색 상자에 입력하는 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. 검색 상자의 드롭다운 목록에 제안 사항을 표시합니다. 제안된 용어는 다른 사용자가 검색한 제안 쿼리 및 사용자 의도를 기반으로 합니다.

일반적으로 사용자가 검색 상자에 새 문자를 입력할 때마다 이 API를 호출합니다. 쿼리 문자열의 완전성은 API가 반환하는 제안된 쿼리 용어의 관련성에 영향을 줍니다. 쿼리 문자열이 완전할수록 제안된 쿼리 용어 목록의 관련성이 커집니다. 예를 들어 API가 *s*에 대해 반환할 수 있는 제안은 *sailing dinghies*에 대해 반환하는 쿼리보다 관련성이 낮을 가능성이 큽니다.

## <a name="getting-suggested-search-terms"></a>제안된 검색어 가져오기

다음 예제에서는 *sail*에 대해 제안된 쿼리 문자열을 반환하는 요청을 보여 줍니다. [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query) 쿼리 매개 변수를 설정할 때 사용자의 부분 쿼리 용어를 URL 인코딩해야 합니다. 예를 들어 사용자가 *sailing les*를 입력한 경우 `q`를 `sailing+les` 또는 `sailing%20les`로 설정합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

다음 응답에는 제안된 쿼리 용어를 포함하는 [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) 개체 목록이 포함됩니다.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

각 제안에는 `displayText`, `query` 및 `url` 필드가 포함됩니다. `displayText` 필드에는 검색 상자의 드롭다운 목록을 채우는 데 사용하는 제안된 쿼리가 포함됩니다. 응답에 포함된 모든 제안을 지정된 순서대로 표시해야 합니다.

다음은 제안된 쿼리 용어를 사용하는 드롭다운 검색 상자의 예를 보여줍니다.

![드롭다운 검색 상자 목록 자동 제안](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

사용자가 드롭다운 목록에서 제안된 쿼리를 선택하면 개발자는 `query` 필드의 쿼리 용어를 사용하여 [Bing Web Search API](../bing-web-search/search-the-web.md)를 호출하고 결과를 직접 표시할 수 있습니다. 또는 `url` 필드에 URL을 사용하여 사용자를 Bing 검색 결과 페이지로 보낼 수도 있습니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 빠르게 시작하려면 [첫 번째 쿼리 만들기](quickstarts/csharp.md)를 참조하세요.

[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) 참조를 익숙하게 다룰 수 있도록 연습하세요. 참조에는 제안된 쿼리 용어를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수 목록과 응답 개체 정의가 포함되어 있습니다.

[Bing Web Search API](../bing-web-search/search-the-web.md)를 사용하여 웹을 검색하는 방법을 알아보세요.

검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)을 반드시 읽도록 합니다.
