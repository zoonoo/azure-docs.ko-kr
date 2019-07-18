---
title: OData order by 참조-Azure Search
description: Azure Search 쿼리에 order by 구문에 대 한 OData 언어 참조입니다.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079757"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Azure Search의 OData $orderby 구문

 사용할 수는 [OData **$orderby** 매개 변수](query-odata-filter-orderby-syntax.md) Azure Search에서 검색 결과 대 한 사용자 지정 정렬 순서를 적용 합니다. 이 문서에서는 설명의 구문을 **$orderby** 자세히 설명에서 합니다. 사용 하는 방법에 대 한 보다 일반적인 정보에 대 한 **$orderby** 검색 결과 표시 하는 경우 참조 [Azure Search에서 검색을 사용 하는 방법을 결과](search-pagination-page-layout.md)합니다.

## <a name="syntax"></a>구문

합니다 **$orderby** 매개 변수는 최대 32의 쉼표로 구분 된 목록을 허용 **order by 절**합니다. Order by 절 구문의 다음 EBNF에 의해 설명 됩니다 ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

각 절은 정렬 방향이 여 필요에 따라 정렬 조건 (`asc` 오름차순 또는 `desc` 내림차순). 방향을 지정 하지 않으면, 기본값은 오름차순입니다. 정렬 기준 수의 경로 `sortable` 필드나가 호출 합니다 [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) 또는 [ `search.score` ](search-query-odata-search-score-function.md) 함수입니다.

여러 문서에는 동일한 정렬 조건이 경우 및 `search.score` 함수가 사용 되지 않습니다 (숫자로 정렬할 경우에 예를 들어 `Rating` 필드 및 3 개 문서로 등급이 4), 내림차순 문서 점수에 의해 중단 됩니다 동률입니다. 문서 점수 동일 (예: 요청에 지정 된 전체 텍스트 검색 쿼리가 없는 경우) 하는 경우 다음 연결 된 문서의 상대적 순서 결정 되지 않습니다.

여러 정렬 기준을 지정할 수 있습니다. 식의 순서는 최종 정렬 순서를 결정합니다. 예를 들어, 점수를 뒤에 등급을 기준으로 내림차순으로 정렬 구문에 게 `$orderby=search.score() desc,Rating desc`합니다.

**$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. **$orderby**에서 `geo.distance`를 사용하는 경우 적용되는 필드는 `Edm.GeographyPoint` 형식이어야 하고 `sortable`이어야 합니다.

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score` 매개 변수를 사용 하지 않습니다.

## <a name="examples"></a>예

호텔을 기본 요금의 오름차순으로 정렬합니다.

    $orderby=BaseRate asc

호텔을 등급의 내림차순으로 정렬한 후 기본 요금의 오름차순으로 정렬합니다(오름차순이 기본값임).

    $orderby=Rating desc,BaseRate

호텔 등급을 지정 된 좌표에서 거리를 기준 오름차순에서 내림차순으로 정렬 합니다.

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

호텔 내림차순으로 search.score 및 등급을 선택한 다음 지정 된 좌표에서 거리를 기준으로 오름차순 정렬 합니다. 동일한 관련성 점수를 사용 하 여 두 개 호텔 및 등급 간의 가장 가까운 것이 먼저 나열 합니다.

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure Search에서 결과 검색을 사용 하는 방법](search-pagination-page-layout.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
