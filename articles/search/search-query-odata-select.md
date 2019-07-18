---
title: OData 선택 참조-Azure Search
description: OData 언어 참조에 대 한 Azure Search 쿼리에 구문을 선택 합니다.
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079679"
---
# <a name="odata-select-syntax-in-azure-search"></a>Azure Search의 OData $select 구문

 사용할 수는 [OData **$select** 매개 변수](query-odata-filter-orderby-syntax.md) Azure Search에서 검색 결과에 포함할 필드를 선택 합니다. 이 문서에서는 설명의 구문을 **$select** 자세히 설명에서 합니다. 사용 하는 방법에 대 한 보다 일반적인 정보에 대 한 **$select** 검색 결과 표시 하는 경우 참조 [Azure Search에서 검색을 사용 하는 방법을 결과](search-pagination-page-layout.md)합니다.

## <a name="syntax"></a>구문

합니다 **$select** 각 문서에는 필드가 쿼리 결과 집합 반환 되는 매개 변수를 결정 합니다. 다음 EBNF ([확장 된 Backus Naur 폼](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에 대 한 문법을 정의 된 **$select** 매개 변수:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

대화형 구문 다이어그램을 사용할 수 있는 이기도합니다.

> [!div class="nextstepaction"]
> [Azure Search에 대 한 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 참조 [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md) 전체 EBNF에 대 한 합니다.

합니다 **$select** 매개 변수는 두 가지 형태로 제공 됩니다.

1. 단일 별표 (`*`), 모든 검색 가능 필드를 반환할지를 나타내는 또는
1. 필드 경로 목록은 쉼표로 구분 된, 식별 필드를 반환 합니다.

두 번째 폼을 사용 하는 경우에 목록에서 검색 가능 필드를 지정할 수 있습니다.

하위 필드를 명시적으로 지정 하지 않고 복잡 한 필드를 나열 하는 경우 하위 검색 가능한 모든 필드가 쿼리 결과 집합에 포함 됩니다. 예를 들어, 인덱스에는 `Address` 필드와 `Street`를 `City`, 및 `Country` 모두 검색할 수 있는 하위 필드입니다. 지정 하는 경우 `Address` 에 **$select**, 쿼리 결과 세 하위 필드를 모두 포함 됩니다.

## <a name="examples"></a>예

포함 된 `HotelId`, `HotelName`, 및 `Rating` 결과에서 최상위 필드 뿐만 `City` 하위 필드 `Address`:

    $select=HotelId, HotelName, Rating, Address/City

결과 예는 다음과 같습니다.

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

포함 된 `HotelName` 최상위 필드의 모든 하위 필드를 비롯 하 여 결과 `Address`, 및 `Type` 및 `BaseRate` 하위 필드의 각 개체의는 `Rooms` 컬렉션:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

결과 예는 다음과 같습니다.

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

- [Azure Search에서 결과 검색을 사용 하는 방법](search-pagination-page-layout.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
