---
title: OData 컬렉션 필터 이해
titleSuffix: Azure Cognitive Search
description: 컬렉션에 고유한 제한 사항 및 동작을 포함 하 여 Azure Cognitive Search 쿼리에서 OData 컬렉션 필터를 사용 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113061"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData 컬렉션 필터 이해

Azure Cognitive Search에서 컬렉션 필드를 [필터링](query-odata-filter-orderby-syntax.md) 하려면 [`any` 및 `all` 연산자](search-query-odata-collection-operators.md) 를 **람다 식**과 함께 사용할 수 있습니다. 람다 식은 **범위 변수**를 참조 하는 부울 식입니다. `any` 및 `all` 연산자는 루프 변수의 역할을 취하는 범위 변수와 루프 본문으로 사용 되는 람다 식을 사용 하는 대부분의 프로그래밍 언어의 `for` 루프와 유사 합니다. 범위 변수는 루프를 반복 하는 동안 컬렉션의 "current" 값을 사용 합니다.

개념적으로 작동 하는 방법은 이상입니다. 실제로 Azure Cognitive Search는 `for` 루프의 작동 방식에 대해 매우 다양 한 방법으로 필터를 구현 합니다. 이러한 차이는 사용자에 게 표시 되지 않지만 특정 상황에서는 표시 되지 않습니다. 최종 결과에는 람다 식을 작성할 때 따라야 하는 규칙이 있습니다.

이 문서에서는 Azure Cognitive Search에서 이러한 필터를 실행 하는 방법을 탐색 하 여 컬렉션 필터에 대 한 규칙이 있는 이유를 설명 합니다. 복잡 한 람다 식으로 고급 필터를 작성 하는 경우이 문서를 통해 필터 및 이유에 대 한 이해를 수립할 수 있습니다.

예제를 비롯 하 여 컬렉션 필터에 대 한 규칙에 대 한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조 하세요.

## <a name="why-collection-filters-are-limited"></a>컬렉션 필터를 제한 하는 이유

모든 유형의 컬렉션에 대해 모든 필터 기능이 지원 되는 이유는 다음과 같은 세 가지 기본적인 이유가 있습니다.

1. 특정 데이터 형식에 대해서는 특정 연산자만 지원 됩니다. 예를 들어 `lt`, `gt`등을 사용 하 여 `true` 하 고 `false` 부울 값을 비교 하는 것은 의미가 없습니다.
1. Azure Cognitive Search는 `Collection(Edm.ComplexType)`형식의 필드에 대 한 **상관 관계 검색** 을 지원 하지 않습니다.
1. Azure Cognitive Search는 반전 된 인덱스를 사용 하 여 컬렉션을 비롯 한 모든 데이터 형식에 대 한 필터를 실행 합니다.

첫 번째 이유는 OData 언어 및 EDM 형식 시스템을 정의 하는 것입니다. 마지막 두 항목은이 문서의 나머지 부분에서 자세히 설명 합니다.

## <a name="correlated-versus-uncorrelated-search"></a>상호 관련 검색 및 상관 관계가 없는 검색

복합 개체의 컬렉션에 여러 필터 조건을 적용 하는 경우이 기준은 *컬렉션의 각 개체*에 적용 되므로 **상관 관계가** 지정 됩니다. 예를 들어 다음 필터는 100 미만의 deluxe 공간이 하나 이상 있는 호텔을 반환 합니다.

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

필터링이 *상관 관계가 없는*경우 위의 필터는 한 방 deluxe 하 고 다른 방에는 100 미만의 기본 요금이 있는 호텔을 반환할 수 있습니다. 람다 식의 두 절은 같은 범위 변수에 적용 되므로이는 의미가 없습니다. 즉, `room`입니다. 이러한 필터가 상호 관련 되는 이유입니다.

그러나 전체 텍스트 검색에는 특정 범위 변수를 참조할 수 있는 방법이 없습니다. 필드 지정 search를 사용 하 여 다음과 같은 [전체 Lucene 쿼리](query-lucene-syntax.md) 를 실행 하는 경우

    Rooms/Type:deluxe AND Rooms/Description:"city view"

한 방에서 deluxe 하 고 다른 방에는 설명의 "도시 보기"를 언급 하는 호텔을 받을 수 있습니다. 예를 들어 `1` `Id` 있는 아래 문서가 쿼리와 일치 합니다.

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

그 이유는 `Rooms/Type`는 전체 문서에 있는 `Rooms/Type` 필드의 분석 된 모든 용어를 참조 하 고, 아래 표에 나와 있는 것 처럼 `Rooms/Description`에 대해 유사 합니다.

전체 텍스트 검색을 위해 `Rooms/Type`를 저장 하는 방법:

| `Rooms/Type` 용어 | 문서 Id |
| --- | --- |
| deluxe | 1, 2 |
| 표준 | 1 |

전체 텍스트 검색을 위해 `Rooms/Description`를 저장 하는 방법:

| `Rooms/Description` 용어 | 문서 Id |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| 가든 | 1 |
| 큰 | 1 |
| 모텔 | 2 |
| 대화방 | 1, 2 |
| 표준 | 1 |
| tcp/ip | 1 |
| view | 1 |

위의 필터와는 달리, 기본적으로 "대화방에 ' Deluxe ' `Type`가 있는 것과 같은 공간이 있고 **같은 방에** `BaseRate` 100 미만으로" 일치 하는 문서 "가 있는 경우 검색 쿼리는" Deluxe "라는 용어를 포함 하 고 `Rooms/Description`에" city 보기 "라는 구가 포함 된" 일치 `Rooms/Type` 문서 "를 의미 합니다. 후자의 경우에는 필드의 상관 관계를 지정할 수 있는 개별 대화방의 개념이 없습니다.

> [!NOTE]
> Azure Cognitive Search에 추가 된 상관 관계 검색에 대 한 지원을 보려면 [이 사용자 음성 항목](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)에 대해 투표 하세요.

## <a name="inverted-indexes-and-collections"></a>반전 인덱스 및 컬렉션

`Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`등의 간단한 컬렉션 보다 복잡 한 컬렉션에 대 한 람다 식에 대 한 제한이 훨씬 적음을 알 수 있습니다. Azure Cognitive Search는 복합 컬렉션을 하위 문서의 실제 컬렉션으로 저장 하는 반면 간단한 컬렉션은 컬렉션으로 저장 되지 않기 때문입니다.

예를 들어 온라인 대리점의 인덱스에 `seasons`와 같은 필터링 가능한 문자열 컬렉션 필드가 있다고 가정 합니다. 이 인덱스에 업로드 된 일부 문서는 다음과 같습니다.

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

`seasons` 필드의 값은 다음과 같이 **반전 된 인덱스**라는 구조에 저장 됩니다.

| 용어 | 문서 Id |
| --- | --- |
| 스프링 | 1, 2 |
| 절약 | 1 |
| 있게 | 1, 2 |
| 동계 | 2, 3 |

이 데이터 구조는 지정 된 용어가 표시 되는 문서에 대해 좋은 속도로 한 질문에 대답 하도록 디자인 되었습니다. 이 질문에 대 한 대답은 컬렉션에 대 한 루프와는 다른 일반적인 같음 검사 처럼 작동 합니다. 실제로 문자열 컬렉션의 경우 Azure Cognitive Search는 `any`에 대 한 람다 식 내에서 비교 연산자로 `eq`만 허용 합니다.

동일 하 게 구축 하 여 동일한 범위 변수에 대 한 여러 개의 같음 검사를 `or`와 결합할 수 있는 방법을 살펴보겠습니다. [수량자의 대 수 및 분배 속성](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에 작동 합니다. 이 식:

    seasons/any(s: s eq 'winter' or s eq 'fall')

이는 다음과 동등합니다.

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

그리고 두 개의 `any` 하위 식은 반전 된 인덱스를 사용 하 여 효율적으로 실행할 수 있습니다. 또한 [수량자의 부정 법칙](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에 다음 식은

    seasons/all(s: s ne 'winter' and s ne 'fall')

이는 다음과 동등합니다.

    not seasons/any(s: s eq 'winter' or s eq 'fall')

따라서 `ne` 및 `and`에서 `all`를 사용할 수 있습니다.

> [!NOTE]
> 세부 정보는이 문서의 범위를 벗어나는 것 이지만 이러한 동일한 원칙이 [지역 공간 지점 컬렉션에 대 한 거리 및 교차 테스트](search-query-odata-geo-spatial-functions.md) 로도 확장 됩니다. 그 이유는 `any`:
>
> - `geo.intersects`는 부정할 수 없습니다.
> - `geo.distance` `lt` 또는 `le`를 사용 하 여 비교 해야 합니다.
> - 식을 `or`와 결합 해야 `and`
>
> `all`에 대해서는 반대 규칙이 적용 됩니다.

예를 들어 `Collection(Edm.Int32)`와 같이 `lt`, `gt`, `le`및 `ge` 연산자를 지 원하는 데이터 형식의 컬렉션을 필터링 하는 경우 더 광범위 한 식이 허용 됩니다. 특히 `and`를 사용 하 여 기본 비교 식이 **범위 비교** 에 결합 된 후 `or`를 사용 하 여 추가로 결합 되는 경우 `any`에는 `or` `and`를 사용할 수 있습니다. 이러한 부울 식의 구조는 [DNF (분리 Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)라고 하며, 그렇지 않은 경우에는 "ORs of ANDs" 이라고 합니다. 반대로 이러한 데이터 형식에 대 한 `all`에 대 한 람다 식은 [my.cnf (결합 Normal Form)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)에 있어야 합니다. 그렇지 않으면 "ANDs of ORs"로 알려져 있습니다. Azure Cognitive Search는 문자열에 대 한 빠른 용어 조회를 수행할 수 있는 것 처럼 반전 인덱스를 사용 하 여 효율적으로 실행할 수 있으므로 이러한 범위 비교를 허용 합니다.

요약 하자면, 람다 식에서 허용 되는 항목에 대 한 다음과 같은 규칙이 있습니다.

- `any`내에서 `lt` 또는 `le`과 비교 하 여 같음, 범위 비교, `geo.intersects`또는 `geo.distance`와 같은 *긍정 검사가* 항상 허용 됩니다 (거리를 확인 하는 데 사용 되는 것 처럼 "거리"는 같음).
- `any`내에서 `or`는 항상 허용 됩니다. `and`는 범위 검사를 표현할 수 있는 데이터 형식에 대해서만 사용할 수 있으며, DNF (ORs of ANDs)를 사용 하는 경우에만 사용할 수 있습니다.
- `all`내에서 규칙은 역방향으로 *표시 됩니다.* 즉, `and` 항상 사용할 수 있으며, ANDs ORS (my.cnf)로 표현 된 범위 검사에만 `or`를 사용할 수 있습니다.

실제로 이러한 필터 유형 중에서 가장 많이 사용 되는 필터 유형입니다. 그러나 가능한 작업의 경계를 이해 하는 것도 도움이 됩니다.

허용 되는 필터 종류와 그렇지 않은 필터 종류에 대 한 구체적인 예는 [올바른 컬렉션 필터를 작성 하는 방법](search-query-troubleshoot-collection-filters.md#bkmk_examples)을 참조 하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search에 대 한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 &#40;검색 Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
