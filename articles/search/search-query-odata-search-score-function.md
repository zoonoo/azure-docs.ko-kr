---
title: OData 검색. 점수 함수 참조-Azure Search
description: Azure Search 쿼리의 OData 검색. 점수 함수
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647528"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Azure Search `search.score` 의 OData 함수

[ **$Orderby** 매개 변수](search-query-odata-orderby.md)없이 Azure Search로 쿼리를 보내면 반환 되는 결과는 관련성 점수를 기준으로 내림차순으로 정렬 됩니다. **$Orderby**사용 하는 경우에도 관련성 점수를 사용 하 여 기본적으로 동률을 나눕니다. 그러나 경우에 따라 관련성 점수를 초기 정렬 조건으로 사용 하 고 일부 다른 기준은 연결 차단기로 사용 하는 것이 유용 합니다. `search.score` 함수를 사용 하면이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다. **$Orderby** 매개 변수의 다른 절과 `asc` 마찬가지로 `desc` 또는 정렬 순서 지정자와 함께 사용할 수 있습니다. 정렬 기준 목록에서 아무 곳에 나 나타날 수 있습니다.

## <a name="example"></a>예제

및를 기준 `search.score` 으로 내림차순으로 호텔 `rating`을 정렬 한 다음, 동일한 등급이 지정 된 두 호텔 사이에서 가장 근접 한 항목을 가장 먼저 나열 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
