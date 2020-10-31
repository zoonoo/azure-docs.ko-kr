---
title: Web Search 엔드포인트
titleSuffix: Azure Cognitive Services
description: 웹 검색 결과를 가져오려면 `GET` 다음 끝점에 요청을 보냅니다. 헤더 및 URL 매개 변수는 추가 사양을 정의합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075673"
---
# <a name="web-search-endpoint"></a>Web Search 엔드포인트

> [!WARNING]
> Bing Search API Cognitive Services에서 Bing Search 서비스로 이동 합니다. **2020 년 10 월 30 일부 터** [여기](https://aka.ms/cogsvcs/bingmove)에 설명 된 프로세스에 따라 Bing Search의 새 인스턴스를 프로 비전 해야 합니다.
> Cognitive Services를 사용 하 여 프로 비전 된 Bing Search API는 향후 3 년 동안 또는 기업계약 종료 될 때까지 먼저 발생 합니다.
> 마이그레이션 지침은 [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)를 참조 하십시오.

**Web Search API** 는 웹 페이지, 뉴스, 이미지, 비디오 및 [엔터티](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)를 반환합니다. 엔터티에는 사람, 장소 또는 토픽에 대한 요약 정보가 포함됩니다.

## <a name="endpoint"></a>엔드포인트

Bing API를 사용하여 웹 검색 결과를 가져오려면 `GET` 요청을 다음 엔드포인트로 전송합니다. 헤더 및 URL 매개 변수는 추가 사양을 정의합니다.

**엔드포인트** : `?q=""`에서 정의한 사용자의 검색 쿼리와 관련된 웹 결과를 반환합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

엔드포인트: 헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 참조에서 확인할 수 있습니다.

## <a name="response-json"></a>응답 JSON

웹 검색 요청에 대한 응답에는 모든 결과가 JSON 개체로 포함됩니다. 결과를 구문 분석하려면 각 형식의 요소를 처리하는 프로시저가 필요합니다. 예제는 [자습서](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) 및 [소스 코드](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)를 참조하세요.

## <a name="next-steps"></a>다음 단계

**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.  모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Web Search API를 사용하는 기본 요청의 예는 [웹 검색 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)을 참조하세요.
