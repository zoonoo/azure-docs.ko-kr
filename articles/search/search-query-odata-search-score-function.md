---
title: OData 검색. 점수 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 OData 검색. 점수 함수
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
ms.openlocfilehash: 500ac4f3a44d54e367ddc4ee5efc9514d603cab6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793265"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData `search.score` 함수

[ **$Orderby** 매개 변수](search-query-odata-orderby.md)없이 Azure Cognitive Search로 쿼리를 보내면 반환 되는 결과는 관련성 점수를 기준으로 내림차순으로 정렬 됩니다. **$Orderby**사용 하는 경우에도 관련성 점수를 사용 하 여 기본적으로 동률을 나눕니다. 그러나 경우에 따라 관련성 점수를 초기 정렬 조건으로 사용 하 고 일부 다른 기준은 연결 차단기로 사용 하는 것이 유용 합니다. `search.score` 함수를 사용 하 여이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다. **$Orderby** 매개 변수의 다른 절과 마찬가지로 `asc` 또는 `desc` 정렬 순서 지정자와 함께 사용할 수 있습니다. 정렬 기준 목록에서 아무 곳에 나 나타날 수 있습니다.

## <a name="example"></a>예제

`search.score` 및 `rating`를 기준으로 내림차순으로 호텔을 정렬 한 다음, 동일한 등급이 있는 두 호텔 사이에 가장 가까운 것이 먼저 표시 되도록 지정 된 좌표에서 거리로 오름차순으로 정렬 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 &#40;검색 AZURE COGNITIVE SEARCH EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
