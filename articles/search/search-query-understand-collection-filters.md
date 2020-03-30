---
title: OData 수집 필터 이해
titleSuffix: Azure Cognitive Search
description: 컬렉션고유의 제한 사항 및 동작을 포함하여 Azure Cognitive Search 쿼리에서 OData 컬렉션 필터가 작동하는 방식에 대해 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113061"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure 인지 검색에서 OData 컬렉션 필터 이해

Azure Cognitive Search의 컬렉션 필드를 [필터링하려면](query-odata-filter-orderby-syntax.md) **람다** [ `any` `all` 식과 함께 및 연산자](search-query-odata-collection-operators.md) 를 사용할 수 있습니다. Lambda 식은 **range 변수를**참조하는 부울 식입니다. 및 연산자는 대부분의 프로그래밍 `for` 언어의 루프와 유사하며, range 변수는 루프 변수의 역할을 맡고 람다 식은 루프의 본문으로 사용됩니다. `all` `any` range 변수는 루프를 반복하는 동안 컬렉션의 "현재" 값을 취합니다.

적어도 그것이 개념적으로 작동하는 방법입니다. 실제로 Azure Cognitive Search는 루프작동 방식과 `for` 매우 다른 방식으로 필터를 구현합니다. 이상적으로, 이 차이는 당신에게 보이지 않을 것입니다, 그러나 특정 상황에서는 그렇지 않습니다. 최종 결과는 람다 식을 작성할 때 따라야 할 규칙이 있다는 것입니다.

이 문서에서는 Azure Cognitive Search에서 이러한 필터를 실행하는 방법을 탐색하여 컬렉션 필터에 대한 규칙이 존재하는 이유를 설명합니다. 복잡한 람다 식을 사용하는 고급 필터를 작성하는 경우 이 문서에서필터에서 가능한 내용과 그 이유에 대한 이해를 높이는 데 도움이 될 수 있습니다.

예제를 포함하여 컬렉션 필터에 대한 규칙에 대한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 문제를 해결합니다.](search-query-troubleshoot-collection-filters.md)

## <a name="why-collection-filters-are-limited"></a>컬렉션 필터가 제한된 이유

모든 유형의 컬렉션에 대해 모든 필터 기능이 지원되지 않는 데는 세 가지 근본적인 이유가 있습니다.

1. 특정 데이터 형식에는 특정 연산자만 지원됩니다. 예를 `true` 들어 부울 값을 비교하고 `false` 을 `lt` `gt`사용하는 것은 의미가 없습니다.
1. Azure Cognitive Search 형식의 필드에서 상관 관계가 `Collection(Edm.ComplexType)`있는 **검색을** 지원 하지 않습니다.
1. Azure Cognitive Search는 반전된 인덱스를 사용하여 컬렉션을 포함한 모든 유형의 데이터에 대한 필터를 실행합니다.

첫 번째 이유는 OData 언어 및 EDM 형식 시스템이 정의되는 방식의 결과입니다. 마지막 두 가지는 이 문서의 나머지 부분에서 자세히 설명합니다.

## <a name="correlated-versus-uncorrelated-search"></a>상관 관계 검색과 상관 관계가 없는 검색

복잡한 개체 컬렉션에 여러 필터 조건을 적용할 때 *조건은 컬렉션의 각 개체에*적용되므로 **상관 관계가** 있습니다. 예를 들어 다음 필터는 요금이 100미만인 디럭스룸이 하나 이상 있는 호텔을 반환합니다.

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

필터링이 *상관관계가 없는*경우 위의 필터는 한 방이 디럭스이고 다른 객실의 기본 요금이 100 미만인 호텔을 반환할 수 있습니다. 람다 식의 두 절이 동일한 범위 변수, 즉 `room`동일한 범위 변수에 적용되므로 의미가 없습니다. 이러한 필터가 상관 관계가 있는 이유입니다.

그러나 전체 텍스트 검색의 경우 특정 범위 변수를 참조할 수 있는 방법은 없습니다. 필드 검색을 사용하여 다음과 같은 [전체 Lucene 쿼리를](query-lucene-syntax.md) 발행하는 경우:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

한 객실이 디럭스인 호텔을 다시 얻을 수 있으며, 다른 객실에서 설명에 "도시 전망"을 언급할 수 있습니다. 예를 들어 아래 `Id` `1` 문서는 쿼리와 일치합니다.

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

그 이유는 `Rooms/Type` 아래 표와 같이 전체 `Rooms/Type` 문서에서 필드의 모든 분석된 `Rooms/Description`용어를 참조하고 유사하게 에 대해 참조하기 때문입니다.

전체 `Rooms/Type` 텍스트 검색을 위해 저장되는 방법:

| 용어`Rooms/Type` | 문서 아이디 |
| --- | --- |
| 디럭스 | 1, 2 |
| 표준 | 1 |

전체 `Rooms/Description` 텍스트 검색을 위해 저장되는 방법:

| 용어`Rooms/Description` | 문서 아이디 |
| --- | --- |
| 코트 야드 | 2 |
| city | 1 |
| 정원 | 1 |
| 큰 | 1 |
| 모텔 | 2 |
| 룸 | 1, 2 |
| 표준 | 1 |
| 스위트룸 | 1 |
| 뷰 | 1 |

따라서 기본적으로 "방이 '디럭스 룸'과 `Type` 같고 **같은 방이** 100 `BaseRate` 미만인 문서일치"라고 말하는 위의 필터와 달리 `Rooms/Type` 검색 쿼리는 ""디럭스"라는 `Rooms/Description` 용어가 있고 "도시 보기"라는 문구가 있는 문서 일치"라고 말합니다. 후자의 경우 필드가 상호 연관될 수 있는 개별 객실의 개념은 없습니다.

> [!NOTE]
> Azure Cognitive Search에 추가된 상관 검색에 대한 지원을 확인하려면 [이 사용자 음성 항목에](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)투표하십시오.

## <a name="inverted-indexes-and-collections"></a>반전된 인덱스 및 컬렉션

와 `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)`같은 간단한 컬렉션보다 복잡한 컬렉션에 대한 람다 식에 대한 제한이 훨씬 적다는 것을 알았을 수 있습니다. Azure Cognitive Search는 복잡한 컬렉션을 하위 문서의 실제 컬렉션으로 저장하는 반면 단순 컬렉션은 컬렉션으로 전혀 저장되지 않기 때문입니다.

예를 들어 온라인 소매업체의 인덱스와 `seasons` 같이 필터링 가능한 문자열 컬렉션 필드를 고려합니다. 이 인덱스에 업로드된 일부 문서는 다음과 같습니다.

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

`seasons` 필드의 값은 **반전된 인덱스라는**구조에 저장되며 다음과 같습니다.

| 용어 | 문서 아이디 |
| --- | --- |
| 봄 | 1, 2 |
| 여름 | 1 |
| 가 | 1, 2 |
| 겨울 | 2, 3 |

이 데이터 구조는 하나의 질문에 매우 빠른 속도로 대답하도록 설계되었습니다. 이 질문에 대답하는 것은 컬렉션에 대한 루프보다 일반 같음 검사와 비슷합니다. 실제로 문자열 컬렉션의 경우 Azure Cognitive Search는 `eq` 에 대한 `any`lambda 식 내에서 비교 연산자로만 허용됩니다.

같음에서 구축, 다음으로 우리는 `or`동일한 범위 변수에 여러 같음 검사를 결합 할 수있는 방법을 살펴 보겠습니다. 대수와 [수량자의 분배 속성](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에 작동합니다. 이 표현식은 다음과 같은 것입니다.

    seasons/any(s: s eq 'winter' or s eq 'fall')

이는 다음과 동등합니다.

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

그리고 두 `any` 하위 표현식 각각은 반전된 인덱스를 사용하여 효율적으로 실행할 수 있습니다. 또한, [수량자의 부정 법](https://en.wikipedia.org/wiki/Existential_quantification#Negation)덕분에,이 표현 :

    seasons/all(s: s ne 'winter' and s ne 'fall')

이는 다음과 동등합니다.

    not seasons/any(s: s eq 'winter' or s eq 'fall')

그래서 함께 `all` `ne` 사용할 수 있습니다. `and`

> [!NOTE]
> 세부 사항은 이 문서의 범위를 벗어나지만 이러한 동일한 원칙은 [지리 공간 점의 컬렉션에 대한 거리 및 교차 테스트로확장됩니다.](search-query-odata-geo-spatial-functions.md) 이것이 다음과 같은 `any`이유입니다.
>
> - `geo.intersects`부정할 수 없음
> - `geo.distance``lt` 또는`le`
> - 식을 와 `or`결합해야 합니다.`and`
>
> 반대 규칙이 `all`적용됩니다.

`lt`예를 들어 , 및 `gt` `le` `ge` 연산자 및 연산자(예:)를 `Collection(Edm.Int32)` 지원하는 데이터 형식의 컬렉션을 필터링할 때 더 다양한 표현식이 허용됩니다. 특히 기본 비교 `and` 식이 `or` 를 `any` `and`사용하여 **범위 비교로** 결합된 다음 을 사용하여 `or`추가로 결합되는 한 에서뿐만 아니라 에서사용할 수 있습니다. 부울 식의 이 구조는 [DNF(DNF)라고](https://en.wikipedia.org/wiki/Disjunctive_normal_form)하며, 그렇지 않으면 "AND의 ORs"라고도 합니다. 반대로 이러한 데이터 형식에 대한 `all` 람다 식은 "ANDs of OR"이라고도 하는 [CNF(결말법 법수)에](https://en.wikipedia.org/wiki/Conjunctive_normal_form)있어야 합니다. Azure Cognitive Search는 문자열에 대한 빠른 용어 조회를 수행할 수 있는 것처럼 반전된 인덱스를 사용하여 효율적으로 실행할 수 있으므로 이러한 범위 비교를 허용합니다.

요약하자면 람다 식에서 허용되는 사항에 대한 엄지 손가락 규칙은 다음과 같습니다.

- `any`내부, *양수 검사는* 항상 같음, 범위 `geo.intersects`비교, `lt` 또는 `le` `geo.distance` 비교또는 (거리를 검사할 때 "친밀감"이 같음과 같다고 생각)과 같이 허용됩니다.
- `any`내부는 `or` 항상 허용됩니다. 범위 검사를 `and` 표현할 수 있는 데이터 형식에만 사용할 수 있으며 DNF(AND)의 O를 사용하는 경우에만 사용할 수 있습니다.
- 내부, `all`규칙은 반전됩니다 - *부정적인 검사만* 허용되고 항상 사용할 `and` `or` 수 있으며 CRS (CNF)의 AND로 표현 된 범위 검사에만 사용할 수 있습니다.

실제로 이러한 필터유형은 어쨌든 사용할 가능성이 가장 높은 필터 유형입니다. 하지만 가능한 것의 경계를 이해하는 것은 여전히 도움이됩니다.

허용되는 필터 종류와 허용되지 않는 필터의 구체적인 예는 [유효한 컬렉션 필터를 작성하는 방법을](search-query-troubleshoot-collection-filters.md#bkmk_examples)참조하십시오.

## <a name="next-steps"></a>다음 단계  

- [Azure 인지 검색에서 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)
- [Azure 인지 검색의 필터](search-filters.md)
- [Azure 인지 검색에 대한 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure 인지 검색에 대 한 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
