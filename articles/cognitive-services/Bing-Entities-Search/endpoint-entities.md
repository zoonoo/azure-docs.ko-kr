---
title: Bing Entity Search 엔드포인트
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816585"
---
# <a name="entity-search-endpoint"></a>Entity Search 엔드포인트
**Entity Search API**는 쿼리를 기반으로 웹에서 엔터티를 반환하는 하나의 엔드포인트를 포함합니다.

## <a name="endpoint"></a>엔드포인트
**Bing API**를 사용하여 엔터티 결과를 가져오려면 `GET` 요청을 다음 엔드포인트로 전송합니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트**: `?q=""`에서 정의한 사용자의 검색 쿼리와 관련된 엔터티를 반환합니다.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) 참조를 참조하세요.

## <a name="response-json"></a>응답 JSON
엔터티 검색 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과에 대한 예는 [시작하기](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start)를 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.  모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Entity Search API를 사용하는 예는 [시작하기](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) 및 [미리 보기 이미지의 크기 조정 및 자르기](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails)를 참조하세요.