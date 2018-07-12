---
title: 'Bing Custom Search: Custom Autosuggest 제안 가져오기 | Microsoft Docs'
description: Custom Autosuggest 제안을 검색하는 방법을 설명합니다.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374710"
---
# <a name="get-custom-suggestions"></a>사용자 지정 제안 가져오기
쿼리를 Bing Custom Search에 전송하기 전에 Custom Autosuggest API를 호출하여 검색어 제안을 만들고 검색 환경을 향상합니다. Custom Autosuggest API는 사용자가 제공하는 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. 지정한 모든 관련 사용자 지정 쿼리 용어가 Autosuggest에서 생성하는 제안보다 먼저 표시됩니다. 자세한 내용은 [사용자 지정 검색 제안 정의](define-custom-suggestions.md)를 참조하세요.

## <a name="endpoint"></a>끝점
Bing Custom Search API를 사용하여 제안된 쿼리를 가져오려면 `GET` 요청을 다음 끝점에 전송합니다.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>응답 JSON
응답에는 제안된 쿼리 용어를 포함하는 SearchAction 개체 목록이 포함됩니다.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

각 제안에는 `displayText` 및 `query` 필드가 포함됩니다. `displayText` 필드에는 검색 상자의 드롭다운 목록을 채우는 데 사용하는 제안된 쿼리가 포함됩니다.

사용자가 드롭다운 목록에서 제안된 쿼리를 선택하는 경우 [Bing Custom Search API](overview.md)를 호출할 때 `query` 필드의 쿼리 용어를 사용합니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

- [사용자 지정 검색 호출](./search-your-custom-view.md)
- [호스트된 UI 환경 구성](./hosted-ui.md)