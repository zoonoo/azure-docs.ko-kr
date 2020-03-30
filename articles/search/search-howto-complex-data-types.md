---
title: 복잡한 데이터 유형을 모델링하는 방법
titleSuffix: Azure Cognitive Search
description: 중첩 또는 계층 데이터 구조는 ComplexType 및 컬렉션 데이터 형식을 사용하여 Azure 인지 검색 인덱스에서 모델링할 수 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283057"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure 인지 검색에서 복잡한 데이터 형식을 모델링하는 방법

Azure 인지 검색 인덱스를 채우는 데 사용되는 외부 데이터 집합은 여러 모양으로 제공될 수 있습니다. 때로는 계층적 또는 중첩된 하부 구조를 포함하기도 합니다. 예를 들어 단일 고객에 대한 여러 주소, 단일 SKU에 대한 여러 색상 및 크기, 단일 책의 여러 작성자 등이 포함될 수 있습니다. 모델링 용어에서 이러한 구조를 *복합,* *복합,* *복합*또는 *집계* 데이터 유형이라고 할 수 있습니다. 이 개념에 대해 Azure Cognitive Search가 사용하는 용어는 **복잡한 형식입니다.** Azure 인지 검색에서 복잡한 형식은 **복잡한 필드를**사용하여 모델링됩니다. 복합 필드는 다른 복잡한 형식을 포함하여 모든 데이터 형식이 될 수 있는 자식(하위 필드)을 포함하는 필드입니다. 이는 프로그래밍 언어의 구조화 된 데이터 유형과 유사한 방식으로 작동합니다.

복잡한 필드는 데이터 형식에 따라 문서의 단일 개체 또는 개체 배열을 나타냅니다. 형식 `Edm.ComplexType` 필드는 단일 개체를 나타내고 `Collection(Edm.ComplexType)` 형식 필드는 개체 배열을 나타냅니다.

Azure Cognitive Search는 기본적으로 복잡한 형식 및 컬렉션을 지원합니다. 이러한 형식을 사용하면 Azure 인지 검색 인덱스에서 거의 모든 JSON 구조를 모델링할 수 있습니다. 이전 버전의 Azure 인지 검색 API에서는 병합된 행 집합만 가져올 수 있습니다. 최신 버전에서는 인덱스가 원본 데이터에 더 밀접하게 대응할 수 있습니다. 즉, 원본 데이터에 복잡한 형식이 있는 경우 인덱스에 복잡한 형식도 있을 수 있습니다.

시작하려면 Azure 포털의 **데이터 가져오기** 마법사에서 로드할 수 있는 호텔 [데이터 집합을](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)권장합니다. 마법사는 원본에서 복잡한 형식을 검색하고 검색된 구조를 기반으로 인덱스 스키마를 제안합니다.

> [!Note]
> 복잡한 형식에 대한 지원은 일반적으로 에서 `api-version=2019-05-06`사용할 수 있습니다. 
>
> 검색 솔루션이 컬렉션에서 병합된 데이터 집합의 이전 해결 방법을 기반으로 하는 경우 인덱스를 변경하여 최신 API 버전에서 지원되는 복잡한 형식을 포함해야 합니다. API 버전 업그레이드에 대한 자세한 내용은 [최신 REST API 버전으로 업그레이드하거나](search-api-migration.md) 최신 [.NET SDK 버전으로 업그레이드를](search-dotnet-sdk-migration-version-9.md)참조하십시오.

## <a name="example-of-a-complex-structure"></a>복잡한 구조의 예

다음 JSON 문서는 간단한 필드와 복잡한 필드로 구성됩니다. 및 및 와 `Address` `Rooms`같은 복잡한 필드에는 하위 필드가 있습니다. `Address`문서의 단일 개체이기 때문에 이러한 하위 필드에 대한 단일 값 집합이 있습니다. 반대로 `Rooms` 컬렉션의 각 개체에 대해 하위 필드에 대해 여러 값 집합이 있습니다.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>복잡한 필드 만들기

다른 인덱스 정의와 마찬가지로 포털, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)또는 [.NET SDK를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 사용하여 복잡한 형식을 포함하는 스키마를 만들 수 있습니다. 

다음 예제에서는 간단한 필드, 컬렉션 및 복잡한 형식이 있는 JSON 인덱스 스키마를 보여 주며 있습니다. 복잡한 형식 내에서 각 하위 필드에는 형식이 있으며 최상위 필드와 마찬가지로 특성이 있을 수 있습니다. 스키마는 위의 예제 데이터에 해당합니다. `Address`는 컬렉션이 아닌 복잡한 필드입니다(호텔에 주소가 하나 있음). `Rooms`는 복잡한 수집 필드입니다 (호텔에는 많은 객실이 있습니다).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>복잡한 필드 업데이트

일반적으로 필드에 적용되는 모든 [재인덱싱 규칙은](search-howto-reindex.md) 복잡한 필드에 계속 적용됩니다. 여기서 몇 가지 주요 규칙을 복원하고 필드를 추가하면 인덱스 다시 빌드가 필요하지 않지만 대부분의 수정 사항은 수행됩니다.

### <a name="structural-updates-to-the-definition"></a>정의에 대한 구조 업데이트

인덱스를 다시 작성할 필요 없이 언제든지 복잡한 필드에 새 하위 필드를 추가할 수 있습니다. 예를 들어 인덱스에 최상위 `Address` 필드를 추가하는 것과 `Rooms` 마찬가지로 "우편 번호"를 "우편 번호"에 추가하거나 "편의 시설"에 추가하는 것이 허용됩니다. 데이터를 업데이트하여 해당 필드를 명시적으로 채울 때까지 기존 문서에는 새 필드에 대한 null 값이 있습니다.

복잡한 형식 내에서 각 하위 필드에는 형식이 있으며 최상위 필드와 마찬가지로 특성이 있을 수 있습니다.

### <a name="data-updates"></a>데이터 업데이트

인덱스의 기존 문서를 작업으로 `upload` 업데이트하면 복잡하고 간단한 필드에 대해 동일한 방식으로 작동하며 모든 필드가 대체됩니다. `merge` 그러나(또는 `mergeOrUpload` 기존 문서에 적용된 경우) 모든 필드에서 동일하게 작동하지 는 않습니다. 특히 `merge` 컬렉션 내에서 요소를 병합하는 것은 지원되지 않습니다. 이 제한은 기본 형식 및 복잡한 컬렉션의 컬렉션에 대해 존재합니다. 컬렉션을 업데이트하려면 전체 컬렉션 값을 검색하고 변경한 다음 Index API 요청에 새 컬렉션을 포함해야 합니다.

## <a name="searching-complex-fields"></a>복잡한 필드 검색

자유 형식 검색 식은 복잡한 형식에서 예상대로 작동합니다. 문서의 어느 곳에서나 검색 가능한 필드 또는 하위 필드가 일치하는 경우 문서 자체가 일치합니다.

여러 용어와 연산자가 있을 때 쿼리의 미묘한 차이가 높아지고 일부 용어에는 [Lucene 구문에서](query-lucene-syntax.md)가능한 한 필드 이름이 지정되어 있습니다. 예를 들어 이 쿼리는 주소 필드의 두 하위 필드에 대해 "포틀랜드" 및 "OR"이라는 두 용어를 일치시키려고 시도합니다.

    search=Address/City:Portland AND Address/State:OR

이와 같은 쿼리는 필터와 달리 전체 텍스트 검색과 *상관 관계가 없습니다.* 필터에서 복합 컬렉션의 하위 필드에 대한 쿼리는 [ `any` 또는 에서 `all` ](search-query-odata-collection-operators.md)범위 변수를 사용하여 상관 관계가 있습니다. 위의 Lucene 쿼리는 오리건의 다른 도시와 함께 "포틀랜드, 메인" 및 "포틀랜드, 오리건"을 모두 포함하는 문서를 반환합니다. 이는 각 절이 전체 문서에서 해당 필드의 모든 값에 적용되므로 "현재 하위 문서"에 대한 개념이 없기 때문에 발생합니다. 이에 대한 자세한 내용은 [Azure 인지 검색에서 OData 컬렉션 필터 이해를](search-query-understand-collection-filters.md)참조하십시오.

## <a name="selecting-complex-fields"></a>복잡한 필드 선택

`$select` 매개 변수는 검색 결과에 반환되는 필드를 선택하는 데 사용됩니다. 이 매개변수를 사용하여 복합 필드의 특정 하위 필드를 선택하려면 슬래시()로`/`구분된 상위 필드와 하위 필드를 포함합니다.

    $select=HotelName, Address/City, Rooms/BaseRate

필드를 검색 결과에 원하는 경우 인덱스에서 검색 가능으로 표시해야 합니다. 검색 가능으로 표시된 필드만 명령문에서 `$select` 사용할 수 있습니다.

## <a name="filter-facet-and-sort-complex-fields"></a>복잡한 필드 필터링, 면 및 정렬

필터링 및 필드 검색에 사용되는 동일한 [OData 경로 구문을](query-odata-filter-orderby-syntax.md) 검색 요청에서 필드를 면면, 정렬 및 선택하는 데도 사용할 수 있습니다. 복잡한 형식의 경우 정렬 가능 또는 면 테이블로 표시할 수 있는 하위 필드를 제어하는 규칙이 적용됩니다. 이러한 규칙에 대한 자세한 내용은 [인덱스 API 만들기 참조를](/rest/api/searchservice/create-index)참조하십시오.

### <a name="faceting-sub-fields"></a>하위 필드 면

모든 하위 필드는 형식또는 `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)`가식이 아니면 면면 테이블로 표시할 수 있습니다.

패싯 결과에 반환된 문서 수는 복잡한 컬렉션(룸)의 하위 문서가 아닌 상위 문서(호텔)에 대해 계산됩니다. 예를 들어 호텔에 "스위트 룸"유형이 20개 있다고 가정합니다. 이 면 매개 `facet=Rooms/Type`변수를 감안할 때 면 수는 객실의 경우 20이 아니라 호텔에 대한 면 수가 됩니다.

### <a name="sorting-complex-fields"></a>복잡한 필드 정렬

정렬 작업은 하위 문서(룸)가 아닌 문서(호텔)에 적용됩니다. 룸과 같은 복잡한 형식 컬렉션이 있는 경우 룸에서 정렬할 수 없다는 것을 깨닫는 것이 중요합니다. 실제로 컬렉션은 정렬할 수 없습니다.

정렬 작업은 필드가 간단한 필드이든 복합 형식의 하위 필드이든 관계없이 문서에 단일 값이 있는 경우 작업정렬이 작동합니다. 예를 들어 `Address/City` 호텔당 주소가 하나만 있기 때문에 정렬이 `$orderby=Address/City` 가능하므로 도시별로 호텔을 정렬합니다.

### <a name="filtering-on-complex-fields"></a>복잡한 필드에 대한 필터링

필터 식에서 복잡한 필드의 하위 필드를 참조할 수 있습니다. 필드를 면처리, 정렬 및 선택하는 데 사용되는 것과 동일한 [OData 경로 구문을](query-odata-filter-orderby-syntax.md) 사용하기만 하면 됩니다. 예를 들어 다음 필터는 캐나다의 모든 호텔을 반환합니다.

    $filter=Address/Country eq 'Canada'

복잡한 컬렉션 필드를 필터링하려면 [ `any` 및 `all` 연산자와](search-query-odata-collection-operators.md)함께 **lambda 식을** 사용할 수 있습니다. 이 경우 lambda 식의 **범위 변수는** 하위 필드가 있는 개체입니다. 표준 OData 경로 구문이 있는 하위 필드를 참조할 수 있습니다. 예를 들어 다음 필터는 디럭스룸이 하나 이상 있고 모든 금연실이 있는 모든 호텔을 반환합니다.

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

최상위 단순 필드와 마찬가지로 복잡한 필드의 간단한 하위 필드는 인덱스 정의에 필터링 **가능한** 특성이 `true` 설정되어 있는 경우에만 필터에 포함될 수 있습니다. 자세한 내용은 인덱스 [API 만들기 참조를](/rest/api/searchservice/create-index)참조하십시오.

## <a name="next-steps"></a>다음 단계

가져오기 데이터 마법사에서 [호텔 데이터 집합을](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) 사용해 봅을 사용해 봅을 사용해 봅을 사용해 봅을 사용해 봅을 사용해 **봅을 사용해 봅** 데이터에 액세스하려면 readme에 제공된 Cosmos DB 연결 정보가 필요합니다.

이 정보를 사용하여 마법사의 첫 번째 단계는 새 Azure Cosmos DB 데이터 원본을 만드는 것입니다. 마법사에서 대상 인덱스 페이지로 이동하면 복잡한 형식의 인덱스가 표시됩니다. 이 인덱스를 만들고 로드한 다음 쿼리를 실행하여 새 구조를 이해합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 가져오기, 인덱싱 및 쿼리를 위한 포털 마법사](search-get-started-portal.md)
