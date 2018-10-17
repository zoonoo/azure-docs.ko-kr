---
title: Bing Image Search란?
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하면 응용 프로그램에서 Bing의 인지 이미지 검색 기능을 사용할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 Bing 이미지와 비슷한 관련 고품질 이미지를 가져와서 표시할 수 있습니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.openlocfilehash: 5d5d69eea3a064679cbc5ddc41891a73e77e55ea
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295362"
---
# <a name="what-is-bing-image-search"></a>Bing Image Search란?

Bing Image Search API를 사용하면 응용 프로그램에서 Bing의 인지 이미지 검색 기능을 사용할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 [Bing 이미지](https://www.bing.com/images)와 비슷한 관련 고품질 이미지를 가져와서 표시할 수 있습니다.

Bing Image Search API는 이미지 전용 검색 결과를 제공합니다. 다른 형식의 웹 콘텐츠에 대해서는 [Bing Web Search API](../bing-web-search/search-the-web.md), [Video Search API](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) 및 [News Search API](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search)를 사용합니다.

## <a name="bing-image-search-features"></a>Bing Image Search 기능

Bing Image Search는 주로 검색 쿼리에서 관련 이미지를 찾아서 반환하지만, 이 서비스는 웹에서 지능적이고 집중적인 이미지 검색을 위한 몇 가지 추가 기능도 제공합니다.


| 기능                                                                                                                                                                                 | 설명                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#using-and-suggesting-search-terms) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 응용 프로그램 환경을 향상시킵니다. |
| [이미지 결과 필터링 및 제한](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | 쿼리 매개 변수를 편집하여 Bing에서 반환하는 이미지를 필터링합니다.                                                                                                       |
| [썸네일 자르기, 크기 조정 및 표시](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Bing Image Search에서 반환하는 이미지에 대한 썸네일 미리 보기를 편집하고 표시합니다.                                                                                      |
| [사용자 검색 쿼리 피벗 및 확장](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | 쿼리에 Bing 제안 검색 용어를 포함시키고 표시하여 검색 기능을 확장합니다.                                                                    |
| [최신 이미지 가져오기](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | 전 세계로부터의 최신 이미지 검색을 사용자 지정합니다.                                                                                                          |

## <a name="workflow"></a>워크플로

Bing Image Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) 또는 [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.
2. 유효한 [검색 쿼리](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

먼저 Bing Image Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)를 사용해 보세요.
이 데모에서는 검색 쿼리를 빠르게 사용자 지정하고 웹에서 이미지를 샅샅이 검색하는 방법을 보여 줍니다.

API를 호출할 준비가 되면 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.

첫 번째 API 요청을 빠르게 시작하려면 다음을 알아볼 수 있습니다.

* REST API를 사용하여 [Bing에 검색 쿼리를 보냅니다](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp). 또는
* SDK를 사용하여 Bing에서 반환하는 이미지를 [요청하고 필터링합니다](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

## <a name="see-also"></a>참고 항목

* [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 참조 섹션에는 이미지 기반 검색 결과를 요청하는 데 사용할 수 있는 엔드포인트, 헤더, API 응답 및 쿼리 매개 변수에 대한 정의 및 정보가 있습니다.

* [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.

* [Bing Image Search API를 사용하여 웹에서 이미지 가져오기](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) 항목에서는 웹에서 이미지를 검색하고 가져오는 방법에 대해 설명합니다.

* [검색 쿼리 보내기 및 사용](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) 항목에서는 검색 쿼리를 작성, 사용자 지정 및 피벗하는 방법에 대해 설명합니다.
