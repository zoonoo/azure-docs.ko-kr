---
title: Bing Web Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Web Search API는 사용자 쿼리에 즉각적인 답변을 제공하는 RESTful 서비스입니다. 검색 결과는 웹 페이지, 이미지, 비디오, 뉴스, 번역 등을 포함하도록 쉽게 구성됩니다. 결과는 JSON으로 제공되며, 검색 관련성 및 Bing Web Search 구독을 기반으로 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 65e28b557a3708c155bf80f21a784374c4497783
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383047"
---
# <a name="what-is-the-bing-web-search-api"></a>Bing Web Search API란?

Bing Web Search API는 사용자 쿼리에 즉각적인 답변을 제공하는 RESTful 서비스입니다. 검색 결과는 웹 페이지, 이미지, 비디오, 뉴스, 번역 등을 포함하도록 쉽게 구성됩니다. Bing Web Search는 검색 관련성 및 Bing Web Search 구독에 기반한 JSON으로 결과를 제공합니다.

이 API는 사용자의 검색 쿼리와 관련된 모든 콘텐츠에 액세스해야 하는 애플리케이션에 가장 적합합니다. 특정 유형의 결과만 필요한 애플리케이션을 빌드하는 경우 [Bing Image Search API](../Bing-Image-Search/overview.md), [Bing Video Search API](../Bing-Video-Search/search-the-web.md) 또는 [Bing News Search API](../Bing-News-Search/search-the-web.md)를 사용하는 것이 좋습니다. Bing Search API의 전체 목록은 [Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services)를 참조하세요.

작동 방식에 대해 알고 싶은가요? [Bing Web Search API 데모](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)를 사용해 보세요.

## <a name="features"></a>기능  

Bing Web Search는 즉각적인 답변에 대한 액세스 권한만 주는 것이 아니라, 사용자의 검색 결과를 사용자 지정할 수 있는 추가 특성과 기능을 제공합니다.

| 기능 | 설명 |
|---------|-------------|
| [실시간 검색 용어 제안](../bing-autosuggest/get-suggested-search-terms.md) | Bing Autosuggest API를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 애플리케이션 환경을 향상시킵니다. |
| [콘텐츠 형식별 결과 필터링 및 제한](filter-answers.md) | 웹 페이지, 이미지, 비디오, 안전 검색 등에 대한 필터 및 쿼리 매개 변수를 사용하여 검색 결과를 사용자 지정하고 구체화합니다. |
| [유니코드 문자 강조 표시](hit-highlighting.md) | 검색 결과에서 원하지 않는 유니코드 문자를 식별하고 제거한 다음, 적중 항목 강조 표시가 있는 검색 결과를 사용자에게 표시합니다. |
| [국가, 지역 및/또는 시장별 검색 결과 지역화](supported-countries-markets.md) | Bing Web Search는 36개가 넘는 국가 또는 지역을 지원합니다. 이 기능을 사용하여 특정 국가/지역 또는 시장에 대한 검색 결과를 구체화할 수 있습니다. |
| [Bing 통계를 통한 검색 메트릭 분석](bing-web-stats.md) | Bing 통계는 호출량, 상위 쿼리 문자열, 지리적 분포 등에 대한 분석을 제공하는 유료 구독입니다. |

## <a name="workflow"></a>워크플로

Bing Web Search API는 HTTP 요청을 수행하고 JSON 응답을 구문 분석할 수 있는 모든 프로그래밍 언어에서 쉽게 호출할 수 있습니다. 이 서비스는 [REST API](quickstarts/python.md) 또는 [Bing Web Search SDK](web-sdk-python-quickstart.md)를 사용하여 액세스할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. [Bing Web Search API에 요청을 보냅니다](quickstarts/python.md).
3. JSON 응답을 구문 분석합니다.

## <a name="next-steps"></a>다음 단계

* [Python 빠른 시작](quickstarts/python.md)을 사용하여 Bing Web Search API를 처음 호출합니다.  
* [단일 페이지 웹앱을 빌드](tutorial-bing-web-search-single-page-app.md)합니다.
* [Web Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 설명서를 검토합니다.  
* Bing Web Search에 대한 [사용 및 표시 요구 사항](UseAndDisplayRequirements.md)에 대해 자세히 알아봅니다.  
