---
title: OData 검색.점수 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 쿼리에서 search.score 함수를 사용하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113128"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure `search.score` 인지 검색의 OData 함수

[ **$orderby** 매개 변수](search-query-odata-orderby.md)없이 Azure Cognitive Search에 쿼리를 보내면 돌아오는 결과는 관련성 점수별로 내림차순으로 정렬됩니다. **$orderby**사용하는 경우에도 관련성 점수는 기본적으로 동점을 끊는 데 사용됩니다. 그러나 경우에 따라 관련성 점수를 초기 정렬 기준으로 사용하고 다른 기준을 타이 브레이커로 사용하는 것이 유용합니다. 이 `search.score` 기능을 사용하면 이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다. $orderby 매개 변수의 `asc` `desc` 다른 절과 마찬가지로 정렬 순서 지정기와 함께 사용할 수 **있습니다.** 정렬 기준 목록의 아무 곳에나 나타날 수 있습니다.

## <a name="example"></a>예제

내림차순으로 `search.score` `rating`호텔을 정렬한 다음 지정된 좌표에서 의 거리별로 오름차순으로 호텔을 정렬하여 동일한 등급을 가진 두 호텔 사이에 가장 가까운 호텔이 먼저 나열되도록 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 EST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
