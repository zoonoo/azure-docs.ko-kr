---
title: Entity Search 엔드포인트 | Microsoft Docs
description: Entity Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: v-jaswel
manager: kaiq
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: 3d5da30102712baf399c245cc7678eeddbce796a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372790"
---
# <a name="entity-search-endpoints"></a>Entity Search 엔드포인트
**Entity Search API**는 한 개의 엔드포인트를 포함합니다.

## <a name="endpoint"></a>끝점
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
