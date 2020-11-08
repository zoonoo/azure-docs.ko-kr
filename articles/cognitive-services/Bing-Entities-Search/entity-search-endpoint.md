---
title: Bing Entity Search API 엔드포인트
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API는 쿼리를 기반으로 웹의 엔터티를 반환하는 엔드포인트 하나를 포함하고 있습니다. 검색 결과는 JSON으로 반환됩니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 1d9b7c79919569830834915fc609b849e717dce8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365842"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API 엔드포인트

> [!WARNING]
> Bing Search API Cognitive Services에서 Bing Search 서비스로 이동 합니다. **2020 년 10 월 30 일부 터** [여기](https://aka.ms/cogsvcs/bingmove)에 설명 된 프로세스에 따라 Bing Search의 새 인스턴스를 프로 비전 해야 합니다.
> Cognitive Services를 사용 하 여 프로 비전 된 Bing Search API는 향후 3 년 동안 또는 기업계약 종료 될 때까지 먼저 발생 합니다.
> 마이그레이션 지침은 [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)를 참조 하십시오.


Bing Entity Search API는 쿼리를 기반으로 웹의 엔터티를 반환하는 엔드포인트 하나를 포함하고 있습니다. 검색 결과는 JSON으로 반환됩니다.

## <a name="get-entity-results-from-the-endpoint"></a>엔드포인트에서 엔터티 결과 가져오기

**Bing API** 를 사용하여 엔터티 결과를 가져오려면 `GET` 요청을 다음 엔드포인트로 전송합니다. [헤더](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) 및 [쿼리 매개 변수](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)를 사용하여 검색 요청을 사용자 지정할 수 있습니다. `?q=` 매개 변수를 사용하여 검색 요청을 보낼 수 있습니다.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Entity Search API란?](overview.md)

## <a name="see-also"></a>참고 항목 

헤더, 매개 변수, 시장 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Entity Search API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 참조 문서를 확인하세요.