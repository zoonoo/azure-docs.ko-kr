---
title: 순위를 사용하여 답변 표시 | Microsoft Docs
description: 순위를 사용하여 Bing Entity Search API가 반환하는 응답을 표시하는 방법을 보여 줍니다.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059994"
---
# <a name="using-ranking-to-display-results"></a>순위를 사용하여 결과 표시  

각 엔터티 검색 응답에는 Bing Web Search 응답의 항목과 비슷한 [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)가 포함되어 있습니다. 이러한 항목은 검색 결과를 표시하는 방법을 지정합니다. 순위 응답은 결과를 폴, 메인라인 및 사이드바 콘텐츠로 그룹화합니다. 폴 결과는 가장 중요하거나 두드러진 결과로, 맨 처음에 표시됩니다. 나머지 결과를 기존의 메인라인 및 사이드바 형식으로 표시하지 않을 경우 사이드바 콘텐츠보다 가시성이 뛰어난 메인라인 콘텐츠를 제공해야 합니다. 
  
각 그룹 내에서 [항목](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) 배열은 콘텐츠를 표시해야 하는 순서를 식별합니다. 각 항목은 답변 내에서 결과를 식별하는 두 가지 방법을 제공합니다.  
  
-   `answerType` 및 `resultIndex` - `answerType` 필드는 응답(엔터티 또는 장소)을 식별하고 `resultIndex`는 응답 내의 결과(예: 엔터티)를 식별합니다. 인덱스는 0부터 시작합니다.  
  
-   `value` — `value` 필드는 응답 또는 응답 내 결과의 ID와 일치하는 ID를 포함합니다. 응답 또는 결과 중 하나만 ID를 포함합니다.  
  
해당 ID를 사용하려면 순위 ID가 응답 또는 결과 중 하나의 ID와 일치해야 합니다. 응답 개체에 `id` 필드가 포함되는 경우 모든 응답의 결과를 함께 표시합니다. 예를 들어, `Entities` 개체에 `id` 필드가 포함되면 모든 엔터티 아티클을 함께 표시합니다. `Entities` 개체에 `id` 필드가 포함되어 있지 않으면 각 엔터티는 `id` 필드를 포함하고 순위 응답은 엔터티와 장소 결과를 혼합합니다.  
  
`answerType` 및 `resultIndex`의 사용은 두 단계로 진행되는 프로세스입니다. 먼저, `answerType`을 사용하여 표시할 결과를 포함하는 응답을 식별합니다. 그런 후 `resultIndex`를 통해 응답 결과로 인덱싱하여 표시할 결과를 가져옵니다. (`answerType` 값은 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 개체에 있는 필드의 이름입니다.) 모든 응답의 결과를 함께 표시해야 할 경우 순위 응답 항목에 `resultIndex` 필드가 포함되지 않습니다.

## <a name="ranking-response-example"></a>순위 응답 예제

다음은 예제 [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)를 보여 줍니다.
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

이 순위 응답에 따라 사이드바에는 Jimi Hendrix와 관련된 두 가지 엔터티 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Entity Search 자습서](tutorial-bing-entities-search-single-page-app.md)
