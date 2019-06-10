---
title: Bing Autosuggest란?
titlesuffix: Azure Cognitive Services
description: Bing Autosuggest API 사용 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 24f35d795b34e7d9c214a23c040791841b4a711b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382567"
---
# <a name="what-is-bing-autosuggest"></a>Bing Autosuggest란?

애플리케이션에서 Bing Search API에 쿼리를 보내는 경우 Bing Autosuggest API를 사용하여 사용자의 검색 환경을 개선할 수 있습니다. Bing Autosuggest API는 검색 상자에 부분 쿼리 문자열을 기준으로 제안된 쿼리 목록을 반환합니다. 검색 상자에 문자를 입력하면 제안이 드롭다운 목록으로 표시될 수 있습니다.

## <a name="bing-autosuggest-api-features"></a>Bing Autosuggest API 기능

| 기능                                                                                                                                                                                 | 설명                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](concepts/get-suggestions.md) | Autosuggest API를 사용하여 입력하는 대로 제안되는 검색 용어를 표시하여 앱 환경을 향상시킵니다. |

## <a name="workflow"></a>워크플로

Bing Autosuggest API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. 

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.
2. 사용자가 애플리케이션의 검색 상자에 새 문자를 입력할 때마다 이 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

일반적으로 사용자가 애플리케이션의 검색 상자에 새 문자를 입력할 때마다 이 API를 호출합니다. 더 많은 문자를 입력하면 API는 관련성이 더 높은 제안된 검색 쿼리를 반환합니다. 예를 들어 API가 단일 `s`에 대해 반환할 수 있는 제안은 `sail`에 대해 반환하는 쿼리보다 관련성이 낮을 가능성이 큽니다.

다음 예제에서는 Bing Autosuggest API에서 제안된 쿼리 용어와 함께 드롭다운 검색 상자를 보여 줍니다.

![드롭다운 검색 상자 목록 자동 제안](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

사용자가 드롭다운 목록에서 제안을 선택하면 이 제안을 사용하여 Bing Search API 중 하나로 검색을 시작하거나 Bing 검색 결과 페이지로 바로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 빠르게 시작하려면 [첫 번째 쿼리 만들기](quickstarts/csharp.md)를 참조하세요.

[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 참조를 익숙하게 다룰 수 있도록 연습하세요. 참조에는 제안된 쿼리 용어를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수 목록과 응답 개체 정의가 포함되어 있습니다.

[Bing Web Search API](../bing-web-search/search-the-web.md)를 사용하여 웹을 검색하는 방법을 알아보세요.

검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)을 반드시 읽도록 합니다.
