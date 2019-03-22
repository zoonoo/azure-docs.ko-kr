---
title: Bing Search API란?
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Bing Search API에 대해 알아보고, 앱 및 서비스에서 인지적 인터넷 검색을 사용하는 방법에 대해 자세히 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903116"
---
# <a name="what-are-the-bing-search-apis"></a>Bing Search API란?

Bing Search Api를 통해 웹에 연결 된 앱과 웹 페이지, 이미지, 뉴스, 위치 및 광고 없이 서비스를 빌드할 수 있습니다. Bing Search REST API 또는 SDK를 사용하여 검색 요청을 보내면 웹 검색으로 관련 정보와 콘텐츠를 얻을 수 있습니다. 이 문서를 사용 하 여 다른 Bing search Api 및 cognitive 검색 응용 프로그램 및 서비스에 통합할 수 있습니다 하는 방법에 대해 알아봅니다. 가격 책정 및 금액 한도는 API마다 다를 수 있습니다.

## <a name="the-bing-web-search-api"></a>Bing Web Search API

[Bing Web Search API](../Bing-Web-Search/index.yml)는 웹 페이지, 이미지, 비디오, 뉴스 등을 반환합니다. 검색 쿼리를 특정 콘텐츠 형식을 포함할지 제외할지이 API를 필터링 할 수 있습니다.

모든 형식의 관련 웹 콘텐츠를 검색해야 하는 애플리케이션에서 Bing Web Search API를 사용하는 것이 좋습니다. 애플리케이션이 특정 형식의 온라인 콘텐츠를 검색하는 경우 아래 검색 API 중 하나를 사용하는 것이 좋습니다.

## <a name="content-specific-bing-search-apis"></a>콘텐츠 관련 Bing Search API

다음 Bing search Api는 이미지, 뉴스, 현지 업무 및 비디오와 같은 웹에서 특정 콘텐츠를 반환 합니다.

| Bing API | 설명 |
| -- | -- |
| [Entity Search](../Bing-Entities-Search/index.yml) | Bing Entity Search API는 사람, 장소 또는 사물일 수 있는 엔터티를 포함하는 검색 결과를 반환합니다. 쿼리에 따라 API는 검색 쿼리를 충족 하는 하나 이상의 엔터티를 반환 합니다. 중요 한 개인, 현지 업무, 랜드마크, 대상 및 자세히 검색 쿼리를 포함할 수 있습니다. |
| [Image Search](../Bing-Image-Search/index.yml) | Bing Image Search API 사용 하면 검색 및 찾기 정적 및 애니메이션 이미지 품질 비슷합니다 [Bing.com/images](https://www.Bing.com/images)합니다. 크기, 색, 라이선스 및 최신 여부 등의 특성으로 이미지를 포함하거나 제외하도록 검색을 구체화할 수 있습니다. 또한 최신 트렌드의 이미지를 검색하고, 이미지를 업로드하여 이미지에 대한 인사이트를 얻고, 썸네일 미리 보기를 표시할 수 있습니다. |
| [News Search](../Bing-News-Search/index.yml) | Bing News Search API를 사용 하면 비슷한 소식이 [Bing.com/news](https://www.Bing.com/news)합니다. API는 여러 소스 또는 특정 도메인의 뉴스 기사를 반환합니다. 범주별로 검색하여 최신 트렌드 문서, 주요 기사 및 헤드라인을 얻을 수 있습니다. |
| [Video Search](../Bing-Video-Search/index.yml) | Bing Video Search API를 사용 하면 웹에서 비디오를 찾을 수 있습니다. 최신 트렌드의 비디오, 관련 콘텐츠 및 썸네일 미리 보기를 얻을 수 있습니다. |
| [Visual Search](../Bing-visual-search/index.yml) | 이미지를 업로드하거나 URL을 사용하여 시각적으로 유사한 제품, 이미지 및 관련 검색과 같은 유용한 정보를 얻을 수 있습니다. |
 [Local Business Search](../bing-local-business-search/index.yml) | Bing 로컬 비즈니스 Search API에 응용 프로그램을 검색 쿼리를 기반으로 하는 현지 업무에 대 한 연락처 및 위치 정보를 찾을 수 있습니다. |

## <a name="the-bing-custom-search-api"></a>Bing Custom Search API

사용 하 여 사용자 지정 검색 인스턴스를 만드는 합니다 [Bing Custom Search](../Bing-Custom-Search/index.yml) API를 사용 하면 설명서 내용 및 관심 항목에만 초점을 맞춘 검색 환경을 만들 수 있습니다. 예를 들어, 도메인, 웹 사이트 및 Bing 검색 하는 특정 웹 페이지를 지정한 후 Bing Custom Search 결과를 특정 콘텐츠 조정 됩니다. Bing Custom Autosuggest, Image 및 Video Search API를 통합하여 검색 환경을 더욱 사용자 지정할 수 있습니다.

## <a name="additional-bing-search-apis"></a>추가 Bing Search API

다음 Bing Search Api를 통해 다른 Bing search Api와 결합 하 여 검색 환경을 개선할 수 있습니다.

| API | 설명 |
| -- | -- |
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | 추천된 검색어를 실시간으로 반환 하 여 Bing Autosuggest API를 사용 하 여 응용 프로그램의 검색 환경을 개선 합니다.  |
| [Bing Statistics](bing-web-stats.md) | Bing Statistics는 애플리케이션에서 사용하는 Bing Search API에 대한 분석을 제공합니다. 일부 제공되는 분석에는 호출량, 주요 쿼리 문자열 및 지리적 분포가 포함됩니다. |

## <a name="next-steps"></a>다음 단계

* Bing Search API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing 사용 및 표시 요구 사항](./use-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.
