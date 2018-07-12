---
title: Autosuggest 끝점 | Microsoft Docs
description: Autosuggest API 끝점에 대한 요약입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376886"
---
# <a name="autosuggest-endpoint"></a>Autosuggest 끝점

**Autosuggest API**에는 부분 검색어에서 제안된 쿼리 목록을 반환하는 하나의 끝점이 포함됩니다.

## <a name="endpoint"></a>끝점

Bing API를 사용하여 제안된 쿼리를 가져오려면 `GET` 요청을 다음 끝점에 전송합니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

**끝점:** `?q=""`에서 정의된 사용자 입력과 관련된 JSON 결과로 검색 제안을 반환합니다.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

헤더, 매개 변수, 지역/국가 코드, 응답 개체, 오류 등에 대한 자세한 내용은 [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) 참조에서 확인할 수 있습니다.

## <a name="response-json"></a>응답 JSON

자동 제안 요청에 대한 응답에는 결과가 JSON 개체로 포함됩니다. 결과 구문 분석의 예제는 [자습서](tutorials/autosuggest.md) 및 [소스 코드](tutorials/autosuggest-source.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

**Bing** API는 해당 유형에 따라 결과를 반환하는 검색 작업을 지원합니다. 모든 검색 끝점은 결과를 JSON 응답 개체로 반환합니다.
모든 끝점은 경도, 위도 및 검색 반경으로 특정 언어 및/또는 위치를 반환하는 쿼리를 지원합니다.

각 끝점에서 지원하는 매개 변수에 대한 자세한 내용은 각 유형에 대한 참조 페이지를 참조하세요.
Autosuggest API를 사용하는 기본 요청의 예는 [Autosuggest 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)을 참조하세요.