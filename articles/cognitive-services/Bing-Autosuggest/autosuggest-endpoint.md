---
title: Bing Autosuggest 엔드포인트
titlesuffix: Azure Cognitive Services
description: Bing Autosuggest API 엔드포인트에 대한 요약입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: c2d1c97ad2af266558f9b664162526d5006d2092
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830447"
---
# <a name="bing-autosuggest-endpoint"></a>Bing Autosuggest 엔드포인트

**Bing Autosuggest API**에는 부분 검색어에서 제안된 쿼리 목록을 반환하는 하나의 엔드포인트가 포함됩니다.

## <a name="endpoint"></a>엔드포인트

Bing API를 사용하여 제안된 쿼리를 가져오려면 `GET` 요청을 다음 엔드포인트에 전송합니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**엔드포인트:**`?q=""`에서 정의된 사용자 입력과 관련된 JSON 결과로 검색 제안을 반환합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) 참조에서 확인할 수 있습니다.

## <a name="response-json"></a>응답 JSON

자동 제안 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과 구문 분석의 예제는 [자습서](tutorials/autosuggest.md) 및 [소스 코드](tutorials/autosuggest-source.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 엔드포인트는 결과를 JSON 응답 개체로 반환합니다.
모든 엔드포인트는 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 엔드포인트에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Autosuggest API를 사용하는 기본 요청의 예는 [Autosuggest 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)을 참조하세요.