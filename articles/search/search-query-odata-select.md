---
title: OData 선택 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 검색 결과에서 반환할 필드의 명시적 선택을 위한 구문 및 언어 참조입니다.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113108"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure 인지 검색에서 OData $select 구문

 [OData **$select** 매개 변수를](query-odata-filter-orderby-syntax.md) 사용하여 Azure 인지 검색의 검색 결과에 포함할 필드를 선택할 수 있습니다. 이 문서에서는 **$select** 구문을 자세히 설명합니다. 검색 결과를 표시할 때 **$select** 사용하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에서 검색 결과로 작업하는 방법을](search-pagination-page-layout.md)참조하세요.

## <a name="syntax"></a>구문

**$select** 매개 변수는 쿼리 결과 집합에서 반환되는 각 문서의 필드를 결정합니다. 다음 EBNF(확장[백투스-Naur Form)는](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) **$select** 매개 변수에 대한 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 인지 검색에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 전체 EBNF에 대한 Azure 인지 검색에 대한 [OData 식 구문 참조를](search-query-odata-syntax-reference.md) 참조하십시오.

**$select** 매개 변수는 두 가지 형태로 제공됩니다.

1. 단일 별()`*`) 은 모든 검색 가능한 필드를 반환해야 한다는 것을 나타내거나
1. 반환할 필드를 식별하는 쉼표로 구분된 필드 경로 목록입니다.

두 번째 양식을 사용하는 경우 목록에서 검색 가능한 필드만 지정할 수 있습니다.

하위 필드를 명시적으로 지정하지 않고 복잡한 필드를 나열하면 검색 가능한 모든 하위 필드가 쿼리 결과 집합에 포함됩니다. 예를 들어 `Address` 인덱스에 `Street`모두 검색 `City`가능한 `Country` . `Address` **$select**지정하면 쿼리 결과에 세 개의 하위 필드가 모두 포함됩니다.

## <a name="examples"></a>예

결과에 `HotelId`의 `HotelName` `Rating` 하위 필드뿐만 아니라 의 `City` 하위 필드를 `Address`포함합니다.

    $select=HotelId, HotelName, Rating, Address/City

예제 결과는 다음과 같을 수 있습니다.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

결과에 `HotelName` 최상위 필드와 `Address` `Rooms` 컬렉션의 각 개체의 모든 하위 필드 `Type` 및 `BaseRate` 하위 필드를 포함합니다.

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

예제 결과는 다음과 같을 수 있습니다.

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색에서 검색 결과 작업 하는 방법](search-pagination-page-layout.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
