---
title: Bing News Search API란?
titlesuffix: Azure Cognitive Services
description: Bing News Search API를 사용하여 헤드라인 및 추세 토픽을 포함하여 여러 범주의 최신 헤드라인을 웹에서 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e02f81ef6bdf8bf4dc39b70cb7a2d9330660d1d6
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390302"
---
# <a name="what-is-the-bing-news-search-api"></a>Bing News Search API란?

Bing News Search API를 통해 Bing의 인지적 뉴스 검색 기능을 애플리케이션으로 쉽게 통합할 수 있습니다. API는 검색 쿼리를 보내고 관련 뉴스 문서를 보낼 수 있도록 하는 [Bing News](https://www.bing.com/news)와 유사한 환경을 제공합니다.

Bing News Search API는 검색 결과만을 제공합니다. 다른 형식의 웹 콘텐츠에 대해서는 [Bing Web Search API](../bing-web-search/search-the-web.md), [Video Search API](../bing-video-search/search-the-web.md) 및 [Image Search API](../bing-image-search/overview.md)를 사용합니다.

## <a name="bing-news-search-api-features"></a>Bing News Search API 기능

Bing News Search API에서는 주로 관련 뉴스 문서를 찾고 반환하는 반면 웹에서 지능적이고 포커스가 있는 뉴스 검색을 제공합니다.

|기능  |설명  |
|---------|---------|
|[검색 용어 제안 및 사용](concepts/search-for-news.md#suggest-and-use-search-terms)     | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 검색 환경을 향상시킵니다.         |
|[일반 뉴스 가져오기](concepts/search-for-news.md#get-general-news)     | Bing News Search API에 검색 쿼리를 보내고, 관련 뉴스 문서의 목록을 다시 가져와서 뉴스를 찾습니다.           |
|[오늘의 주요 뉴스](concepts/search-for-news.md#get-todays-top-news)      | 모든 범주에서 오늘의 주요 뉴스를 가져옵니다.       |
|[범주별 뉴스](concepts/search-for-news.md)     | 특정 범주에서 뉴스를 검색합니다.        | 
|[헤드라인 뉴스](concepts/search-for-news.md)     | 모든 범주에서 주요 헤드라인을 검색합니다.         |

## <a name="workflow"></a>워크플로

Bing News Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. REST API 또는 SDK를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 Cognitive Services API 계정을 만듭니다. Azure 구독이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api) 수 있습니다.

2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.

3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

먼저 Bing News Search API에 대한 [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)를 사용해 보세요. 이 데모에서는 검색 쿼리를 빠르게 사용자 지정하고 웹에서 뉴스를 찾는 방법을 보여 줍니다.

첫 번째 API 요청을 신속하게 시작하려면 [REST API](quickstart.md)에 대한 빠른 시작 또는 [SDK](sdk.md) 중 하나를 사용해 보세요.

## <a name="see-also"></a>참고 항목

* [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 참조 섹션에는 이미지 기반 검색 결과를 요청하는 데 사용할 수 있는 엔드포인트, 헤더, API 응답 및 쿼리 매개 변수에 대한 정의 및 정보가 있습니다.

* [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.
