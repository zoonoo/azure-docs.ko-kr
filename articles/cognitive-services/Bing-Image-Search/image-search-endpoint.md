---
title: Bing Image Search API에 대한 엔드포인트
titleSuffix: Azure Cognitive Services
description: Image Search API에는 세 개의 끝점이 포함 되어 있습니다. 끝점 1은 웹에서 이미지를 반환 합니다. 엔드포인트 2는 ImageInsights를 반환합니다. 엔드포인트 3은 최신 이미지를 반환합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593521"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing Image Search API에 대한 엔드포인트

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

**Image Search API** 는 세 개의 엔드포인트를 포함합니다.  엔드포인트 1은 쿼리를 기반으로 하는 웹에서 이미지를 반환합니다. 엔드포인트 2는 [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)를 반환합니다.  엔드포인트 3은 최신 이미지를 반환합니다.

## <a name="endpoints"></a>엔드포인트

Bing API를 사용하여 이미지 결과를 가져오려면 다음 엔드포인트 중 하나로 요청을 보냅니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트 1:**`?q=""`에서 정의한 사용자의 검색 쿼리와 관련된 이미지를 반환합니다.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**엔드포인트 2:**`GET` 또는 `POST` 중 하나를 사용하여 이미지에 대한 정보를 반환합니다.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 요청은 이미지가 포함된 웹 페이지와 같이 이미지에 대한 정보를 반환합니다. `GET` 요청에 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 매개 변수를 포함합니다.

또는 `POST` 요청의 본문에 이진 이미지를 포함하고 [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 매개 변수를 `RecognizedEntities`로 설정할 수 있습니다. 그러면 후속 `GET` 요청에서 매개 변수로 사용할 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken)이 반환되며 이미지에 있는 인물에 대한 정보가 반환됩니다.  `insightsToken`을 사용하여 다른 호출을 하지 않고 `POST`의 결과에서 `RecognizedEntities`을 제외한 모든 정보를 얻으려면 `modules`를 `All`로 설정합니다.


**엔드포인트 3:** 다른 사용자가 한 검색 요청을 기반으로 최신 이미지를 반환합니다. 이미지는 예를 들어 주목할 만한 사람이나 이벤트를 기반으로 다양한 범주로 구분됩니다.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

최신 이미지를 지원하는 지역/국가 목록은 [최신 이미지](./trending-images.md)를 참조하세요.

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 참조를 참조하세요.
## <a name="response-json"></a>응답 JSON
이미지 검색 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과 구문 분석의 예제는 [자습서](./tutorial-bing-image-search-single-page-app.md) 및 [소스 코드](./tutorial-bing-image-search-single-page-app.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.  모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Image Search API를 사용하는 기본 요청의 예는 [Image Search 빠른 시작](./overview.md)을 참조하세요.