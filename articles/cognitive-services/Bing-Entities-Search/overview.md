---
title: Bing Entity Search API란?
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API를 사용하여 검색 쿼리에서 엔터티 및 위치를 추출하고 검색합니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 679a3d9efbeeb75e0aa8e3986fa85b7ecf0d77bd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388492"
---
# <a name="what-is-bing-entity-search-api"></a>Bing Entity Search API란?

Bing Entity Search API는 Bing에 검색 쿼리를 보내고 엔터티 및 장소가 포함된 결과를 가져옵니다. 장소 결과에는 식당, 호텔 또는 다른 현지 회사가 포함됩니다. Bing은 쿼리에서 현지 회사의 이름을 지정하거나 비즈니스 유형(예: 주변 식당)을 요청하는 경우 장소를 반환합니다. 쿼리가 잘 알려진 사람, 장소(관광 명소, 시/도, 국가/지역 등) 또는 사물을 지정하는 경우 Bing은 엔터티를 반환합니다.

|기능  |설명  |
|---------|---------|
|[실시간 검색 제안](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | 사용자 유형의 드롭다운 목록으로 표시할 수 있는 검색 제안을 제공합니다.       | 
| [엔터티 명확성](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | 여러 의미로 해석이 가능한 쿼리에 대한 여러 엔터티를 가져옵니다. |
| [장소 찾기](concepts/search-for-entities.md#find-places) | 현지 비즈니스 및 엔터티에 대한 정보를 검색하여 반환합니다.  |

## <a name="workflow"></a>워크플로

Bing Entity Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. REST API 또는 SDK를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.
2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

* Bing Entity Search API에 대한 [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)를 사용해 보세요. 
* 첫 번째 요청을 신속하게 시작하려면 [빠른 시작](quickstarts/csharp.md)
* [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 참조 섹션을 시도해 보세요.
* [Bing 사용 및 표시 요구 사항](./use-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.
