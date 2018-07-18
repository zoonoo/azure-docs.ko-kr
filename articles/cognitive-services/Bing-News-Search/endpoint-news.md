---
title: Bing News Search 엔드포인트 | Microsoft Docs
description: News Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372910"
---
# <a name="news-search-endpoints"></a>News Search 엔드포인트
**News Search API**는 뉴스 기사, 웹 페이지, 이미지, 비디오 및 [엔터티](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)를 반환합니다. 엔터티에는 사람, 장소 또는 토픽에 대한 요약 정보가 포함됩니다. 
## <a name="endpoints"></a>Endpoints
Bing API를 사용하여 뉴스 검색 결과를 가져오려면 다음 엔드포인트 중 하나로 `GET` 요청을 보냅니다. 헤더 및 URL 매개 변수는 추가 사양을 정의합니다.

**엔드포인트 1:** 사용자의 검색 쿼리를 기반으로 새로운 뉴스 항목을 반환합니다. 검색 쿼리가 비어 있으면 호출이 상위 뉴스 기사를 반환합니다. 쿼리 `?q=""` 옵션을 `/news` URL에 사용할 수도 있습니다. 가용성 정보는 [지원되는 국가 및 마켓](supported-countries-markets.md#supported-markets-for-news-search-endpoint)을 참조하세요.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

**엔드포인트 2:** 범주별 상위 뉴스 항목을 반환합니다. `category=business`, `category=sports` 또는 `category=entertainment`를 사용하여 상위 비즈니스, 스포츠 또는 엔터테인먼트 기사를 구체적으로 요청할 수 있습니다.  `category` 매개 변수는 `/news` URL에만 사용할 수 있습니다. 범주를 지정하기 위한 몇 가지 공식적인 요구 사항이 있습니다. 자세한 내용은 [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) 설명서의 `category`를 참조하세요. 가용성 정보는 [지원되는 국가 및 마켓](supported-countries-markets.md#supported-markets-for-news-endpoint)을 참조하세요. 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**엔드포인트 3:** 현재 소셜 네트워크에서 유행하고 있는 최신 토픽을 반환합니다. `/trendingtopics` 옵션이 포함되면 Bing 검색은 `freshness` 및 `?q=""` 같은 여러 가지 다른 매개 변수를 무시합니다. 가용성 정보는 [지원되는 국가 및 마켓](supported-countries-markets.md#supported-markets-for-news-trending-endpoint)을 참조하세요.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) 참조에서 확인할 수 있습니다.
## <a name="response-json"></a>응답 JSON
뉴스 검색 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과를 구문 분석하려면 각 형식의 요소를 처리하는 프로시저가 필요합니다. 예제는 [자습서](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) 및 [소스 코드](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source)를 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.  모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
News Search API를 사용하는 기본 요청의 예는 [Bing News Search 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)을 참조하세요.