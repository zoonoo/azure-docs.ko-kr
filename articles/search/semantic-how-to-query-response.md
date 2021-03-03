---
title: 의미 체계 응답 구조
titleSuffix: Azure Cognitive Search
description: Cognitive Search의 의미 체계 순위 알고리즘과 결과 집합에서 ' 의미 체계 대답 ' 및 ' 의미 체계 캡션 '을 구조화 하는 방법에 대해 설명 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679865"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위 및 응답

> [!IMPORTANT]
> 의미 체계 순위 알고리즘 및 의미 체계 대답/캡션은 공개 미리 보기로 제공 되며 미리 보기 REST API 통해서만 제공 됩니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다.

의미 체계 순위는 키워드가 아니라 자연어로 표현 된 쿼리에 대해 학습 된 의미 체계 순위 모델을 사용 하 여 검색 결과의 전체 자릿수를 향상 시킵니다.

이 문서에서는 의미 체계 순위 알고리즘 및 의미 체계 응답의 모양을 지정 하는 방법을 설명 합니다. 응답에는 일반 텍스트의 캡션과 강조 표시 및 답변 (선택 사항)이 모두 포함 됩니다.

+ 의미 체계 캡션은 검색 결과에서 추출 된 쿼리와 관련 된 텍스트 통로입니다. 결과 페이지에 대 한 개별 콘텐츠 필드가 너무 클 경우 결과를 요약 하는 데 도움이 될 수 있습니다. 캡션 기능 의미 체계 하이라이트를 사용 하면 사용자가 쿼리 결과를 신속 하 게 skim 하 여 가장 관련성이 높은 문서를 찾고 전반적인 사용자 경험을 개선할 수 있습니다.

+ 의미 체계 답변은 Bing의 기계 학습 모델을 사용 하 여 질문 처럼 보이는 쿼리에 대 한 답변을 작성 합니다. 쿼리 결과 집합의 최상위 문서에서 추출 된 대로 쿼리와 가장 관련성이 높은 통로 목록에서 답이 선택 됩니다. 답변은 검색 페이지에서 렌더링 하도록 선택할 수 있는 쿼리 응답 페이로드의 독립적인 최상위 개체로 반환 됩니다 (검색 결과에 따라).

## <a name="prerequisites"></a>사전 요구 사항

+ 의미 체계 쿼리 유형을 사용 하 여 작성 된 쿼리입니다. 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조 하세요.

## <a name="understanding-a-semantic-response"></a>의미 체계 응답 이해

의미 체계 응답에는 점수, 캡션 및 답변에 대 한 새 속성이 포함 됩니다. 의미 체계 응답은 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)에서 반환 된 상위 50 결과를 사용 하 여 표준 응답에서 작성 된 다음 의미 체계 ranker를 사용 하 여 다시 순위가 매겨집니다. 50 개를 초과 하는 경우 추가 결과가 반환 되지만 의미상 다시 순위가 지정 되지 않습니다.

모든 쿼리와 마찬가지로 응답은 검색할 수 있는 것으로 표시 된 모든 필드 또는 select 문에 나열 된 필드만으로 구성 됩니다. 또한 "답변" 필드와 "캡션"을 포함 합니다.

+ 각 의미 체계 결과에 대해 기본적으로 하나의 "응답"이 있으며, 검색 페이지에서 렌더링 하도록 선택할 수 있는 고유한 필드로 반환 됩니다. 최대 5 개까지 지정할 수 있습니다. 답변의 공식화은 자동으로 수행 됩니다. 즉, 초기 결과의 모든 문서를 읽고, 기계 읽기 이해를 실행 하 고, 마지막으로 대답 필드에서 사용자의 질문에 대 한 직접 답변의 수준을 올리는 것입니다.

+ "Caption"은 문서 콘텐츠의 추출 기반 요약으로, 일반 텍스트 또는 강조 표시로 반환 됩니다. 캡션은 자동으로 포함 되며 표시 하지 않을 수 없습니다. 강조 표시 되는 문자열을 식별 하기 위해 기계 판독값 이해력을 사용 하 여 강조 표시 합니다. 강조 표시는 가장 관련성이 높은 통로에 주목 하 여 결과 페이지를 신속 하 게 검색 하 여 올바른 문서를 찾을 수 있도록 합니다.

의미 체계 다시 순위 결과 집합은 값을 기준으로 결과 집합을 정렬 합니다 @search.rerankerScore . OrderBy 절을 추가 하면 해당 절이 의미 체계 쿼리에서 지원 되지 않기 때문에 HTTP 400 오류가 반환 됩니다.

## <a name="example"></a>예제

는 @search.rerankerScore 표준과 함께 존재 @search.score 하며 결과의 순위를 다시 매기는 데 사용 됩니다.

다음 쿼리가 제공 됩니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

다음 결과, 의미 체계 응답 담당자가 표시 될 수 있습니다. 이러한 결과는 ""로 순위가 매겨진 상위 3 개의 응답만 표시 합니다 @search.rerankerScore . 이제 Oceanside 리조트의 순위가 가장 먼저 결정 됩니다. ""의 기본 순위에 따라 @search.score 이 결과는 추적 종료 후 두 번째 위치에서 반환 됩니다.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>다음 단계

+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [유사성 알고리즘](index-ranking-similarity.md)
+ [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)
+ [기본 쿼리 만들기](search-query-create.md)