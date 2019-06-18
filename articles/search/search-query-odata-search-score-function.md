---
title: OData search.score 함수 참조-Azure Search
description: Azure Search 쿼리에 OData search.score 함수입니다.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079692"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` Azure Search에서 함수

없이 Azure Search에는 쿼리를 보낼 때 합니다 [ **$orderby** 매개 변수](search-query-odata-orderby.md), 관련성 점수에 의해 반환 되는 결과 내림차순 정렬 됩니다. 수행할 사용 하더라도 **$orderby**, 관련성 점수를 결속을 기본적으로 사용 됩니다. 그러나 때로는 것 승리를 관련성 점수는 초기 정렬 조건 및 일부 다른 조건을 사용 하는 데 유용 합니다. `search.score` 함수를 사용 하면이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

**$orderby**에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다. 사용 될 수 있습니다 합니다 `asc` 또는 `desc` 의 다른 절에서와 마찬가지로 정렬 순서 지정자는 **$orderby** 매개 변수입니다. 정렬 기준 목록에서 아무 곳 이나 나타날 수 있습니다.

## <a name="example"></a>예

호텔 내림차순으로 정렬 `search.score` 및 `rating`, 및 다음 오름차순으로 거리를 지정 된 좌표에서 동일한 등급을 사용 하 여 두 개 호텔 간의 가장 가까운 것이 먼저 나열 되도록 합니다.

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>다음 단계  

- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
