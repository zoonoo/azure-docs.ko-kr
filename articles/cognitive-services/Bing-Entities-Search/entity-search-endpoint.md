---
title: Bing Entity Search API 엔드포인트
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API 엔드포인트에 대해 알아보고 요청을 보냅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424047"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API 엔드포인트


Bing Entity Search API는 쿼리를 기반으로 웹의 엔터티를 반환하는 엔드포인트 하나를 포함하고 있습니다. 검색 결과는 JSON으로 반환됩니다.

## <a name="get-entity-results-from-the-endpoint"></a>엔드포인트에서 엔터티 결과 가져오기

**Bing API**를 사용하여 엔터티 결과를 가져오려면 `GET` 요청을 다음 엔드포인트로 전송합니다. [헤더](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) 및 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)를 사용하여 검색 요청을 사용자 지정할 수 있습니다. `?q=` 매개 변수를 사용하여 검색 요청을 보낼 수 있습니다.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Entity Search API란?](overview.md)

## <a name="see-also"></a>참고자료 

헤더, 매개 변수, 시장 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 참조 문서를 확인하세요.
