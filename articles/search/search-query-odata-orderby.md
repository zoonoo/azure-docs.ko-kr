---
title: OData 순서별 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 순서별로 사용하기 위한 구문 및 언어 참조 설명서입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113155"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 인지 검색에서 OData $orderby 구문

 [OData **$orderby** 매개 변수를](query-odata-filter-orderby-syntax.md) 사용하여 Azure 인지 검색에서 검색 결과에 대한 사용자 지정 정렬 순서를 적용할 수 있습니다. 이 문서에서는 **$orderby** 구문을 자세히 설명합니다. 검색 결과를 표시할 때 **$orderby** 사용하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에서 검색 결과로 작업하는 방법을](search-pagination-page-layout.md)참조하세요.

## <a name="syntax"></a>구문

**$orderby** 매개 변수는 최대 32개의 **순서별 절의**쉼표로 구분된 목록을 허용합니다. 주문별 절의 구문은 다음 EBNF(확장[백투스-Naur 양식)에](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)설명되어 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

각 절에는 정렬 기준이 있으며 선택적으로`asc` 정렬 방향(오름차순 또는 `desc` 내림차순)이 뒤따릅니다. 방향을 지정하지 않으면 기본값은 오름차순입니다. 정렬 조건은 `sortable` 필드의 경로이거나 또는 함수에 대한 [`geo.distance`](search-query-odata-geo-spatial-functions.md) 호출일 [`search.score`](search-query-odata-search-score-function.md) 수 있습니다.

여러 문서에 동일한 정렬 조건이 `search.score` 있고 함수가 사용되지 않는 경우(예: 숫자 `Rating` 필드로 정렬하고 세 개의 문서가 모두 4등급인 경우) 내림차순으로 문서 점수별로 관계가 끊어집니다. 문서 점수가 같으면(예: 요청에 지정된 전체 텍스트 검색 쿼리가 없는 경우) 연결된 문서의 상대 순서가 확정되지 않습니다.

여러 정렬 기준을 지정할 수 있습니다. 식의 순서는 최종 정렬 순서를 결정합니다. 예를 들어 점수별로 내림차순을 정렬한 다음 등급 `$orderby=search.score() desc,Rating desc`다음에 구문이 됩니다.

**$orderby**에서 `geo.distance`에 대한 구문은 **$filter**에 있을 때와 같습니다. **$orderby**에서 `geo.distance`를 사용하는 경우 적용되는 필드는 `Edm.GeographyPoint` 형식이어야 하고 `sortable`이어야 합니다.

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수는 `search.score` 매개 변수를 고려하지 않습니다.

## <a name="examples"></a>예

호텔을 기본 요금의 오름차순으로 정렬합니다.

    $orderby=BaseRate asc

호텔을 등급의 내림차순으로 정렬한 후 기본 요금의 오름차순으로 정렬합니다(오름차순이 기본값임).

    $orderby=Rating desc,BaseRate

등급별로 내림차순 호텔을 정렬한 다음 지정된 좌표에서 거리로 오름차순으로 오름차순:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

search.score 및 등급별로 내림차순으로 호텔을 정렬한 다음 지정된 좌표에서 의 거리별로 오름차순으로 호텔을 정렬합니다. 관련성 점수와 평점이 동일한 두 호텔 간에 가장 가까운 호텔이 먼저 나열됩니다.

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색에서 검색 결과 작업 하는 방법](search-pagination-page-layout.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
