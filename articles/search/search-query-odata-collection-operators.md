---
title: OData 수집 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만들 때 필터가 컬렉션 또는 복잡한 컬렉션 필드에 있을 때 람다 식에서 "any" 및 "all" 연산자를 사용합니다.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113241"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure 인지 검색의 OData `any` 컬렉션 연산자 -`all`

Azure Cognitive Search와 함께 사용할 [OData 필터 식을](query-odata-filter-orderby-syntax.md) 작성할 때 컬렉션 필드를 필터링하는 것이 유용한 경우가 많습니다. `any` 및 연산자를 `all` 사용하여 이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF(확장[백투스-Naur 양식)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)또는 `any` `all`을 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

컬렉션을 필터링하는 세 가지 형태의 식이 있습니다.

- 처음 두 개는 컬렉션 필드를 반복하여 컬렉션의 각 요소에 람다 식 의 형태로 제공된 술어를 적용합니다.
  - 조건자 `all` 컬렉션의 모든 요소에 대 한 true인 경우 반환을 `true` 사용 하는 식입니다.
  - 조건자 `any` 컬렉션의 하나 이상의 요소에 대 한 true인 경우 반환을 `true` 사용 하는 식입니다.
- 세 번째 형식의 `any` 컬렉션 필터는 lambda 식 없이 컬렉션 필드가 비어 있는지 여부를 테스트합니다. 컬렉션에 요소가 있으면 반환합니다. `true` 컬렉션이 비어 있으면 반환됩니다. `false`

컬렉션 필터의 **람다 식은** 프로그래밍 언어의 루프 본문과 같습니다. 반복 하는 동안 컬렉션의 현재 요소를 보유 하는 **range 변수**라는 변수를 정의 합니다. 또한 컬렉션의 각 요소에 대한 범위 변수에 적용할 필터 기준인 또 다른 부울 식을 정의합니다.

## <a name="examples"></a>예

`tags` 그 필드가 정확히 문자열 "와이파이"를 포함 일치 :

    tags/any(t: t eq 'wifi')

`ratings` 필드의 모든 요소가 3에서 5 사이인 문서(포함)를 일치시다.

    ratings/all(r: r ge 3 and r le 5)

`locations` 필드의 지리적 좌표가 지정된 다각형 내에 있는 문서를 일치시다.

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

필드가 `rooms` 비어 있는 문서 일치:

    not rooms/any()

모든 방에 대해 `rooms/amenities` 필드에 "tv"가 포함되어 `rooms/baseRate` 있고 100 미만인 문서 일치:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>제한 사항

람다 식의 본문 내에서 필터 식의 모든 기능을 사용할 수 있는 것은 아닙니다. 제한은 필터링하려는 컬렉션 필드의 데이터 형식에 따라 다릅니다. 다음 표에는 제한 사항이 요약됩니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

이러한 제한 사항및 예제에 대한 자세한 내용은 [Azure Cognitive Search의 컬렉션 문제 해결 필터를](search-query-troubleshoot-collection-filters.md)참조하십시오. 이러한 제한 사항이 존재하는 이유에 대한 자세한 내용은 [Azure Cognitive Search의 컬렉션 필터 이해를](search-query-understand-collection-filters.md)참조하십시오.

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
