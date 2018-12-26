---
title: Bing Entity Search 엔드포인트
titlesuffix: Azure Cognitive Services
description: Entity Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: aahi
ms.openlocfilehash: 578791c8beb34b08b28037977147d41db83f4a4d
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164919"
---
# <a name="entity-search-endpoints"></a>Entity Search 엔드포인트
**Entity Search API**는 한 개의 엔드포인트를 포함합니다.

## <a name="endpoint"></a>엔드포인트
엔터티 검색 결과를 요청하려면 다음 엔드포인트로 요청을 보냅니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

엔드포인트 `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

다음 URL 매개 변수가 필요합니다.
- mkt. 결과가 나오는 지역/국가입니다. 
- q. 엔터티 검색 쿼리입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Entity Search 빠른 시작](quickstarts/csharp.md)

## <a name="see-also"></a>참고 항목 

[Bing Entity Search 개요](search-the-web.md )
[API 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
