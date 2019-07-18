---
title: OData 컬렉션 필터-Azure Search 이해
description: Azure Search 쿼리에 OData 컬렉션 필터의 작동 방식을 이해 합니다.
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079601"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Azure Search의 OData 컬렉션 필터 이해

[필터](query-odata-filter-orderby-syntax.md) Azure Search의 컬렉션 필드를 사용할 수 있습니다 합니다 [ `any` 하 고 `all` 연산자](search-query-odata-collection-operators.md) 와 함께 **람다 식**. 람다 식을 참조 하는 부울 식을는 **범위 변수**합니다. 합니다 `any` 및 `all` 연산자는 비슷합니다는 `for` 루프 변수 및 루프의 본문으로 람다 식의 역할 범위 변수를 사용 하 여 대부분의 프로그래밍 언어에서 루프입니다. 범위 변수는 루프의 반복 중에 컬렉션의 "현재" 값에 적용 됩니다.

최소는 개념적으로 작동 하는 방법에 있습니다. 실제로 Azure Search에 매우 다른 방식으로 필터를 구현 `for` 작업을 반복 합니다. 이상적으로이 차이 표시 수는 있지만 특정 상황에서는 아닙니다. 최종 결과 람다 식을 작성 하는 경우 수행 해야 하는 규칙이 있습니다.

이 문서는 Azure Search에서 이러한 필터를 실행 하는 방법을 탐색 하 여 컬렉션 필터에 대 한 규칙 존재 하는 이유를 설명 합니다. 복잡 한 람다 식 사용 하 여 고급 필터를 작성 하는 경우 유용할 수 있습니다이 문서에서는 필터에 가능한 것에 대 한 이해를 작성 및 이유입니다.

표시 되는 내용 규칙에 대 한 컬렉션 필터, 예제를 포함 하는 방법은 [Azure Search의 문제 해결 OData 컬렉션 필터](search-query-troubleshoot-collection-filters.md)합니다.

## <a name="why-collection-filters-are-limited"></a>컬렉션 필터를 제한 하는 이유

세 가지가 있습니다 기본 컬렉션의 모든 형식에 대 한 일부 필터 기능은 지원 하지 않는 이유:

1. 특정 데이터 형식에 대 한 특정 연산자만 지원 됩니다. 예를 들어 부울 값을 비교 하는 것이 되어도 `true` 하 고 `false` 사용 하 여 `lt`, `gt`등입니다.
1. Azure Search를 지원 하지 않습니다 **상관 관계가 지정 된 검색** 형식의 필드에 `Collection(Edm.ComplexType)`입니다.
1. Azure Search에서는 모든 유형의 컬렉션을 포함 한 데이터를 통해 필터를 실행 하는 인덱스를 반전 합니다.

첫 번째 이유는 바로 OData 언어 및 EDM 형식 시스템 어떻게 정의 됩니다. 마지막 두가이 문서의 나머지 부분에서 자세히 설명 합니다.

## <a name="correlated-versus-uncorrelated-search"></a>상관 관계가 없는 검색 및 상관 관계가 지정 된

조건이 복잡 한 개체의 컬렉션에 대해 여러 필터 조건에 적용 하면 **상관 관계가 지정 된** 에 적용 되므로 *컬렉션의 각 개체*합니다. 예를 들어, 다음 필터는 100 보다 작은 비율을 사용 하 여 하나 이상의 deluxe 확보 하는 호텔을 반환 합니다.

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

필터링 되었으면 *상관 관계가 없는*, 위의 필터를 한곳에 디럭스 있고 다른 룸 기본 호텔 100 보다 작은 비율을 반환할 수 있습니다. 이치에 맞지 않습니다, 즉 람다 식의 두 절의 조건을 모두 동일한 범위 변수에 적용 되므로 `room`합니다. 이 때문에 이러한 필터는 상호 관련 됩니다.

그러나 전체 텍스트 검색 방법이 특정 범위 변수를 참조할 수 없습니다. 필드 지정된 검색을 사용 하 여 발급 하는 경우는 [전체 Lucene 쿼리](query-lucene-syntax.md) 이 이와 같은:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

설명에 호텔 다시 위치 한곳에 deluxe, 이며 다른 룸 언급 "city 보기"를 얻을 수 있습니다. 예를 들어, 아래 인 문서 `Id` 의 `1` 쿼리와 일치 합니다.

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

이유는 `Rooms/Type` 의 모든 분석 된 용어를 참조 합니다 `Rooms/Type` 전체 문서와 마찬가지로 필드 `Rooms/Description`아래 표에 나와 있는 것 처럼 합니다.

어떻게 `Rooms/Type` 전체 텍스트 검색을 위해 저장 됩니다.

| 용어 `Rooms/Type` | 문서 Id |
| --- | --- |
| 디럭스 | 1, 2 |
| 표준 | 1 |

어떻게 `Rooms/Description` 전체 텍스트 검색을 위해 저장 됩니다.

| 용어 `Rooms/Description` | 문서 Id |
| --- | --- |
| 안마당 | 2 |
| city | 1 |
| 가든 | 1 |
| 큰 | 1 |
| 모텔 | 2 |
| 대화방 | 1, 2 |
| 표준 | 1 |
| 도구 모음 | 1 |
| 보기 | 1 |

따라서 위의 필터와는 달리 기본적으로 줬 "대화방에 있는 문서를 찾습니다. `Type` 'Deluxe 공간' 같음 및 **같은 해당 공간** 에 `BaseRate` 100 대 미만의", 검색 쿼리 "일치 문서 위치 `Rooms/Type`기간은 "디럭스" 및 `Rooms/Description` "city 보기" 라는 문구에 합니다. 후자의 경우 상관 관계가 지정 된 필드가 포함 된 개별 방 개념이 있습니다.

> [!NOTE]
> 확인 하려는 경우 Azure Search에 추가 하는 상관 관계가 지정 된 검색에 대 한 지원에 대해 투표 하세요 [이 사용자 의견 항목](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)합니다.

## <a name="inverted-indexes-and-collections"></a>반전 된 인덱스 및 컬렉션

알 수 있습니다 간단한 컬렉션 같은 보다 복잡 한 컬렉션에 비해 람다 식에 대 한 훨씬 적습니다 제한이 `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`등입니다. Azure Search 간단한 컬렉션에서 모든 컬렉션으로 저장 되지 않습니다 하는 동안 하위 문서의 실제 컬렉션으로 복잡 한 컬렉션을 저장 하는 때문입니다.

예를 들어 필터링 가능한 문자열 컬렉션 필드 처럼 `seasons` 온라인 상점에 대 한 인덱스에서. 이 인덱스에 업로드 하는 일부 문서는 다음과 같습니다.

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

값을 `seasons` 필드 라고 하는 구조에 저장 됩니다는 **인덱스를 반전**, 다음과 같은 모양의:

| 용어 | 문서 Id |
| --- | --- |
| Spring | 1, 2 |
| 여름 | 1 |
| 대체 | 1, 2 |
| 겨울 | 2, 3 |

이 데이터 구조는 매우 빨리를 사용 하 여 한 질문에 대답 하도록 설계 되었습니다. 문서에서 지정된 용어 나타납니까? 이 질문에 답하려면 생김 더 루프 보다 일반 같음 검사를 컬렉션에 대 한 합니다. 사실,이 문자열 컬렉션에 있는 이유는, Azure Search만 허용 `eq` 에 대 한 람다 식 내의 비교 연산자로 `any`합니다.

같음에서 구축, 다음에 살펴보겠습니다 사용 하 여 동일한 범위 변수에서 여러 같음 검사를 결합 하는 방법을 `or`합니다. 대 수 덕분 작동 하 고 [수량자의 분배 속성](https://en.wikipedia.org/wiki/Existential_quantification#Negation)합니다. 이 식:

    seasons/any(s: s eq 'winter' or s eq 'fall')

이는 다음과 동등합니다.

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

각 두 `any` 하위 식을 효율적으로 실행할 수 반전된 된 인덱스를 사용 하 여 합니다. 또한 덕분에 [수량자의 부정 사유가](https://en.wikipedia.org/wiki/Existential_quantification#Negation)을이 식:

    seasons/all(s: s ne 'winter' and s ne 'fall')

이는 다음과 동등합니다.

    not seasons/any(s: s eq 'winter' or s eq 'fall')

사용할 수 있기 때문입니다 `all` 사용 하 여 `ne` 고 `and`입니다.

> [!NOTE]
> 이러한 동일한 원칙이 확장 세부 정보는이 문서의 범위를 벗어나는, 있지만 [지리 공간 지점 컬렉션에 대 한 거리 및 교차 테스트](search-query-odata-geo-spatial-functions.md) 도 합니다. 바로 이것이 `any`:
>
> - `geo.intersects` 부정할 수 없습니다.
> - `geo.distance` 사용 하 여 비교 해야 `lt` 또는 `le`
> - 식을 사용 하 여 결합 해야 `or`아니라 `and`
>
> 반대로 규칙에 대 한 적용 `all`합니다.

다양 한 식 지 원하는 형식을 데이터 컬렉션에 대해 필터링 하는 경우 허용 됩니다 합니다 `lt`, `gt`를 `le`, 및 `ge` 연산자와 같은 `Collection(Edm.Int32)` 예를 들어 합니다. 특히, 사용할 수 있습니다 `and` 뿐만 `or` 에서 `any`로 기본 비교 식으로 결합 되어 **범위 비교** 사용 하 여 `and`, 추가 된 다음 사용 하 여 결합 `or`합니다. 이 구조의 부울 식 이라고 [Disjunctive Normal Form (로)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), "ANDs ORs" 라고도 합니다. 반대로, 람다 식에 대 한 `all` 이러한 데이터 형식에 있어야 [으로만 정규 양식 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), "ANDs의 ORs" 라고도 합니다. Azure Search에 실행할 수 있으므로 이러한 범위 비교를 사용 하면 사용 하 여 반전 된 인덱스를 효율적으로 가능한 것 처럼 문자열에 대 한 빠른 용어 조회 합니다.

요약 하자면, 람다 식에 허용 되는 사항에 대 한 규칙이 다음과 같습니다.

- 내 `any`, *양수 검사* 는 항상 같은 허용 같음, 범위 비교 `geo.intersects`, 또는 `geo.distance` 비교할 `lt` 또는 `le` (생각할 "근접성"와 같은 것으로 같음 거리를 확인 하는 데 있어)입니다.
- 내 `any`, `or` 항상 허용 됩니다. 사용할 수 있습니다 `and` ANDs ORs (로) 범위 검사 및 경우에만 표현할 수 있는 데이터 형식에 대해서만 사용 합니다.
- 내 `all`, 규칙 반대가 됩니다만- *검사 음수* 허용 되는 사용할 수 있습니다 `and` 항상 사용할 수 있습니다 `or` 범위 검사 표현으로 ANDs의 ORs (CNF)만 합니다.

실제로 이러한 유형은 필터를 사용 하 시겠습니까 가능성이 가장 합니다. 하지만 가능한 것의 경계를 파악 하는 데 여전히 유용 합니다.

어떤 종류의 필터를 허용 하 고는 없는의 특정 예제를 참조 하세요 [유효한 컬렉션 필터를 작성 하는 방법을](search-query-troubleshoot-collection-filters.md#bkmk_examples)합니다.

## <a name="next-steps"></a>다음 단계  

- [Azure Search의 OData 컬렉션 필터를 문제 해결](search-query-troubleshoot-collection-filters.md)
- [Azure Search의 필터](search-filters.md)
- [Azure Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Search 서비스 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
