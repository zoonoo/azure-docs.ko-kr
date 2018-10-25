---
title: Bing Entity Search 엔드포인트
titlesuffix: Azure Cognitive Services
description: Entity Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: b1b89cbacf43df544261847d4e3fae396a52f423
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816279"
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
