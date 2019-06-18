---
title: OData 컬렉션 연산자 참조-Azure Search
description: OData 컬렉션 연산자, 모든, 및 Azure Search 쿼리에 람다 식입니다.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079939"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Azure Search-에서 OData 컬렉션 연산자 `any` 및 `all`

작성 하는 경우는 [OData 필터 식](query-odata-filter-orderby-syntax.md) Azure Search를 사용 하려면 경우가 컬렉션 필드를 필터링 하는 데 유용 합니다. 사용 하 여 얻을 수 있습니다 합니다 `any` 고 `all` 연산자입니다.

## <a name="syntax"></a>구문

다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))를 사용 하는 OData 식 문법 정의 `any` 또는 `all`합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

컬렉션을 필터링 하는 식의 세 가지 형식이 있습니다.

- 처음 두 컬렉션의 각 요소에는 람다 식의 형태로 지정 된 조건자를 적용 하는 컬렉션 필드를 반복 합니다.
  - 사용 하는 식 `all` 반환 `true` 조건자가 컬렉션의 모든 요소에 대해 true입니다.
  - 사용 하는 식 `any` 반환 `true` 조건자가 컬렉션의 요소를 하나 이상에 대해 true입니다.
- 컬렉션의 세 번째 폼에 사용 하 여 필터링 `any` 컬렉션 필드가 비어 있는지 여부를 테스트 하려면 람다 식을 없이 합니다. 반환 하는 경우 컬렉션에 요소가 하나라도 `true`합니다. 반환 하는 경우 컬렉션은 비어, `false`합니다.

A **람다 식** 컬렉션 필터는 같은 프로그래밍 언어로 루프의 본문입니다. 변수를 정의 호출을 **범위 변수**를 반복 하는 동안 컬렉션의 현재 요소를 보유 하는 합니다. 또한 컬렉션의 각 요소에 대 한 범위 변수에 적용할 필터 조건을 다른 부울 식을 정의 합니다.

## <a name="examples"></a>예

일치 된 문서 `tags` 필드에 문자열 "wifi" 정확 하 게 합니다.

    tags/any(t: t eq 'wifi')

일치 위치를 문서화의 모든 요소는 `ratings` 필드 3 및 5 (포함) 사이가:

    ratings/all(r: r ge 3 and r le 5)

좌표가 지역 중 하나는 여기서 일치 문서는 `locations` 필드는 지정 된 다각형 내:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

일치 문서 위치를 `rooms` 필드가 비어:

    not rooms/any()

위치에 대 한 모든 대화방이 문서와 일치 합니다 `rooms/amenities` 필드에 "tv" 및 `rooms/baseRate` 100 보다 작으면:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>제한 사항

필터 식의 일부 기능은 람다 식의 본문 내에서 사용할 수 있습니다. 제한 사항으로 필터링 하려는 컬렉션 필드의 데이터 형식에 따라 다릅니다. 다음 표에서 제한 사항을 보여 줍니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

예제 뿐만 아니라 이러한 제한 사항에 대 한 자세한 내용은 참조 하세요. [Azure Search의 필터 컬렉션 문제 해결](search-query-troubleshoot-collection-filters.md)합니다. 이유에 대 한 자세한 정보에 대 한 이러한 제한 사항이 존재를 참조 하십시오 [Azure Search의 필터 컬렉션 이해](search-query-understand-collection-filters.md)합니다.

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
