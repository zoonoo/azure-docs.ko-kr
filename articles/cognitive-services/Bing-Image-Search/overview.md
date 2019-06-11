---
title: Bing Image Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하면 애플리케이션에서 Bing의 인지 이미지 검색 기능을 사용할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 Bing 이미지와 비슷한 관련 고품질 이미지를 가져와서 표시할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c8213e7d725c7c06d40555176fff24be4cff7607
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388669"
---
# <a name="what-is-the-bing-image-search-api"></a>Bing Image Search API란?

Bing Image Search API를 사용하면 애플리케이션에서 Bing의 이미지 검색 기능을 사용할 수 있습니다. 검색 쿼리를 API로 보내면 [bing.com/images](https://www.bing.com/images)와 비슷한 고품질 이미지를 얻을 수 있습니다.

Bing Image Search API에서 이미지 전용 검색 결과를 제공하지만, 다른 사용 가능한 [Bing Search API](../bing-web-search/bing-api-comparison.md)를 결합하거나 사용하여 웹에서 다양한 형식의 콘텐츠를 찾을 수 있습니다.

## <a name="bing-image-search-features"></a>Bing Image Search 기능

| 기능                                                                                                                                                                                 | 설명                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 응용 프로그램 환경을 향상시킵니다. |
| [이미지 결과 필터링 및 제한](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | 쿼리 매개 변수를 편집하여 Bing에서 반환하는 이미지를 필터링합니다.                                                                                                       |
| [썸네일 자르기, 크기 조정 및 표시](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Bing Image Search에서 반환하는 이미지에 대한 썸네일 미리 보기를 편집하고 표시합니다.                                                                                      |
| [사용자 검색 쿼리 피벗 및 확장](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | 쿼리에 Bing 제안 검색 용어를 포함시키고 표시하여 검색 기능을 확장합니다.                                                                    |
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

* Bing Search API에 대한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 

* [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 참조 섹션에는 API의 엔드포인트, 헤더, API 응답 및 쿼리 매개 변수에 대한 정보가 나와 있습니다.

* [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.

* [Bing Image Search API를 사용하여 웹에서 이미지 가져오기](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) 문서에서는 웹에서 이미지를 검색하고 가져오는 방법에 대해 설명합니다.

* [검색 쿼리 보내기 및 사용](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) 문서에서는 검색 쿼리를 만들고, 사용자 지정하고, 피벗하는 방법에 대해 설명합니다.
