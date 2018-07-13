---
title: Video Search 엔드포인트 | Microsoft Docs
description: Video Search API 엔드포인트에 대한 요약 정보입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372894"
---
# <a name="video-search-endpoints"></a>Video Search 엔드포인트
**Video Search API**는 세 개의 엔드포인트를 포함합니다.  엔드포인트 1은 쿼리에 따라 웹의 비디오를 반환합니다. 엔드포인트 2는 `modules` URL 매개 변수에 따라 비디오에 대한 인사이트를 반환합니다.  엔드포인트 3은 최신 이미지를 반환합니다.

## <a name="endpoints"></a>Endpoints
Bing API를 사용하여 비디오 결과를 가져오려면 다음 엔드포인트 중 하나로 `GET` 요청을 보냅니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트 1**: `?q=""`에서 정의한 사용자의 검색 쿼리와 관련된 비디오를 반환합니다.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**엔드포인트 2:** 관련 비디오처럼 비디오에 대한 인사이트를 반환합니다. 요청할 인사이트를 쉼표로 구분한 목록인 `modules` [쿼리 매개 변수](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters)를 포함하세요.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**엔드포인트 3:** 다른 사용자가 한 검색 요청을 기반으로 최신 비디오를 반환합니다. 비디오는 여러 범주로 나뉩니다. 예를 들어 주목할 만한 사람 또는 이벤트에 따라 나뉩니다.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) 참조에서 확인할 수 있습니다.
## <a name="response-json"></a>응답 JSON
비디오 검색 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과 구문 분석의 예제는 [자습서](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) 및 [소스 코드](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source)를 참조하세요.

## <a name="next-steps"></a>다음 단계
**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.  모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Video Search API를 사용하는 기본 요청의 예는 [Video Search 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-video-search)을 참조하세요.