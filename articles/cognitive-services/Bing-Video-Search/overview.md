---
title: Bing Video Search API란?
titlesuffix: Azure Cognitive Services
description: Bing Video Search API를 사용하여 웹을 통해 비디오를 검색하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2f6be01a3aee4e156b7af184fa2537455fc73f3f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386529"
---
# <a name="what-is-the-bing-video-search-api"></a>Bing Video Search API란?

Bing Video Search API를 통해 쉽게 서비스 및 애플리케이션에 비디오 검색 기능을 추가할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 [Bing Video](https://www.bing.com/video)와 비슷한 관련 고품질 비디오를 가져와서 표시할 수 있습니다. 비디오만 포함하는 검색 결과에 이 API를 사용합니다. [Bing Web Search API](../bing-web-search/search-the-web.md)는 웹 페이지, 비디오, 뉴스 및 이미지를 비롯한 웹 콘텐츠의 다른 유형을 반환할 수 있습니다.

## <a name="bing-video-search-api-features"></a>Bing Video Search API 기능

| 기능                                                                                                                                                                                 | 설명                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 응용 프로그램 환경을 향상시킵니다. |
| [비디오 결과 필터링 및 제한](concepts/get-videos.md#filtering-videos)                      | 쿼리 매개 변수를 편집하여 반환된 비디오를 필터링합니다.                                                                                                       |
| [썸네일 자르기, 크기 조정 및 표시](resize-and-crop-thumbnails.md)                                                | Bing Video Search API에서 반환하는 비디오에 대한 썸네일 미리 보기를 편집하고 표시합니다.                                                                                      |
| [인기 비디오 가져오기](trending-videos.md) | 전 세계의 최신 비디오를 검색합니다.                                                                                                          |
| [비디오 인사이트 가져오기](video-insights.md) | 전 세계로부터의 최신 비디오 검색을 사용자 지정합니다.                                                                                                          |

## <a name="workflow"></a>워크플로

Bing Video Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. [REST API](csharp.md) 또는 [SDK](video-search-sdk-quickstart.md)를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.
2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.


## <a name="next-steps"></a>다음 단계

Bing Video Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)는 검색 쿼리를 사용자 지정하고 비디오에 대해 웹을 검색하는 방법을 보여줍니다.

API를 호출할 준비가 되면 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.

첫 번째 API 요청을 빠르게 시작하려면 [빠른 시작](csharp.md)을 사용합니다.

## <a name="see-also"></a>참고 항목

* [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) 참조 페이지에는 검색 결과를 요청하는 데 사용되는 엔드포인트, 헤더 및 쿼리 매개 변수의 목록이 포함되어 있습니다.

* [Bing 사용 및 표시 요구 사항](./useanddisplayrequirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.