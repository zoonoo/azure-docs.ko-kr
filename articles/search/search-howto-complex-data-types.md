---
title: 복합 데이터 형식을 모델링하는 방법 - Azure Search
description: ComplexType 및 컬렉션 데이터 형식을 사용 하 여 Azure Search 인덱스에 중첩 되거나 계층적인 데이터 구조를 모델링할 수 있습니다.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621373"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Azure Search에서 복합 데이터 형식을 모델링하는 방법

Azure Search 인덱스를 채우는 데 사용 하는 외부 데이터 집합은 여러 도형에서 가져올 수 있습니다. 경우에 따라 계층적 또는 중첩 된 콘텐츠의 포함 됩니다. 예제는 단일 고객, 여러 색 및 크기에 대 한 여러 권의 책, 저자, 단일 SKU에 대 한 여러 주소를 포함 하 고 등 수 있습니다. 모델링 용어에서는 이러한 구조 라고 표시 될 수 있습니다 *복합*를 *복합*를 *복합*, 또는 *집계* 데이터 형식입니다. Azure Search에서는이 개념에 대 한 사용 기간은 **복합 형식**합니다. Azure search에서 복합 형식을 사용 하 여 모델링 됩니다 **복잡 한 필드**합니다. 복잡 한 필드에 다른 복잡 한 형식을 비롯 한 모든 데이터 형식일 수 있습니다 (하위 필드) 자식을 포함 하는 필드가입니다. 이 프로그래밍 언어의 구조화 된 데이터 형식과 비슷한 방식으로 작동합니다.

복잡 한 필드를 문서에 단일 개체 또는 데이터 형식에 따라 개체의 배열을 나타냅니다. 형식의 필드 `Edm.ComplexType` 형식의 필드 하는 동안 단일 개체를 나타내는 `Collection(Edm.ComplexType)` 개체의 배열을 나타냅니다.

Azure Search는 복합 형식 및 컬렉션에 고유 하 게 지원합니다. 이러한 종류를 사용 하면 Azure Search 인덱스에 거의 모든 JSON 구조를 모델링할 수 있습니다. Azure Search Api의 이전 버전에서는 행 집합을 가져올 수 없습니다만 결합 합니다. 최신 버전을 인덱스 더욱 긴밀 하 게 원본 데이터에 해당 이제 수 있습니다. 즉, 원본 데이터에 복합 형식이 있는 경우 인덱스가 있습니다 복합 형식은 또한.

시작 하려면 것이 좋습니다는 [호텔 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)에서 로드할 수 있는 합니다 **데이터 가져오기** Azure portal에서 마법사. 마법사 소스의 복합 유형을 검색 하 고 검색 된 구조에 따라 인덱스 스키마를 제안 합니다.

> [!Note]
> 복합 형식에 대 한 지원은에서 일반 공급 `api-version=2019-05-06`합니다. 
>
> 검색 솔루션을 기반으로 컬렉션에서 평면화 된 데이터 집합의 이전 해결 하는 경우 최신 API 버전에서 지원 되는 복잡 한 형식을 포함 하기 위해 인덱스를 변경 해야 합니다. API 버전으로 업그레이드 하는 방법에 대 한 자세한 내용은 참조 하세요. [최신 REST API 버전으로 업그레이드](search-api-migration.md) 하거나 [최신.NET SDK 버전으로 업그레이드](search-dotnet-sdk-migration-version-9.md)합니다.

## <a name="example-of-a-complex-structure"></a>복잡 한 구조의 예

다음 JSON 문서는 간단한 필드 및 복잡 한 필드의 구성 됩니다. 와 같은 복잡 한 필드 `Address` 및 `Rooms`, 하위 필드가 있습니다. `Address` 문서에 단일 개체 이므로 이러한 하위 필드에 대 한 값의 단일 집합을 있습니다. 반면, `Rooms` 하위 필드에 대 한 값의 여러 집합, 각 개체에 대 한 컬렉션에 있습니다.

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

## <a name="creating-complex-fields"></a>복잡 한 필드 만들기

모든 인덱스 정의 사용 하 여 포털을 사용할 수 있습니다 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), 또는 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 복합 형식을 포함 하는 스키마를 만듭니다. 

다음 예제에서는 간단한 필드, 컬렉션 및 복합 형식을 사용 하 여 JSON 인덱스 스키마를 보여 줍니다. 복합 형식 내에서 각 하위 필드에 형식이 있고 특성이 있을 수 있습니다, 수행 하는 것으로 최상위 필드를 확인 합니다. 위의 예제 데이터에 해당 하는 스키마입니다. `Address` (호텔 주소가 하나) 컬렉션에 없는 복잡 한 필드가입니다. `Rooms` 복잡 한 컬렉션 필드 (호텔 많은 대화방을 있음)입니다.

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

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

## <a name="updating-complex-fields"></a>복잡 한 필드를 업데이트 하는 중

모든 합니다 [규칙을 다시 만드는](search-howto-reindex.md) 에 적용 되는 필드는 일반적 여전히 복잡 한 필드에 적용 합니다. 몇 재작성 여기에서 기본 규칙에 대 한 필드 추가 인덱스를 다시 작성 하는 필요 하지 않지만 일반적으로 수정.

### <a name="structural-updates-to-the-definition"></a>정의에 대 한 구조적 업데이트

인덱스 다시 작성 하지 않고도 언제 든 복잡 한 필드에 새 하위 필드를 추가할 수 있습니다. 예를 들어, "우편 번호"를 추가 `Address` 또는 "시설"를 `Rooms` 허용 되는 인덱스에 최상위 필드를 추가 하는 것과 같습니다. 기존 문서는 명시적으로 데이터를 업데이트 하 여 해당 필드를 채울 때까지 새 필드에 null 값을 포함 합니다.

복합 형식 내에서 각 하위 필드에 형식이 있고 특성이 있을 수 있습니다, 수행 하는 것으로 최상위 필드를 확인 합니다.

### <a name="data-updates"></a>데이터 업데이트

사용 하 여 인덱스의 기존 문서 업데이트는 `upload` 복잡 하 고 간단한 필드에 대해 동일한 방식으로 작동 하는 작업-모든 필드가 바뀝니다. 그러나 `merge` (또는 `mergeOrUpload` 기존 문서에 적용 된 경우) 모든 필드에서 동일 하 게 작동 하지 않습니다. 특히 `merge` 컬렉션 내에서 병합 요소를 지원 하지 않습니다. 이 제한은 기본 형식의 컬렉션 및 복잡 한 컬렉션에 대해 존재합니다. 컬렉션을 업데이트 하려면에서는 필요한 전체 컬렉션 값을 검색할 변경, 하 고 인덱스 API 요청에서 새 컬렉션을 포함 합니다.

## <a name="searching-complex-fields"></a>복잡 한 필드를 검색합니다.

자유 형식 검색 식에는 복합 형식의 예상 대로 작동 합니다. 모든 검색 가능한 필드 또는 문서의 아무 곳 이나 하위 필드에 일치 하는 경우 문서 자체는 일치 합니다.

쿼리 더욱 많은 경우 여러 항 및 연산자와 몇 가지 용어에 사용 하 여 가능한 필드 이름을 지정 하 고, 게 미묘한 합니다 [Lucene 구문](query-lucene-syntax.md)합니다. 예를 들어이 쿼리 일치 시 키 려 두 용어를 "포틀랜드" 및 "OR" 주소 필드의 두 하위 필드에 대해:

    search=Address/City:Portland AND Address/State:OR

이 같은 쿼리 *상관 관계가 없는* 필터와 달리 전체 텍스트 검색에 대 한 합니다. 필터에서 복잡 한 컬렉션의 하위 필드에 대 한 쿼리는 상관 관계가 지정 된 범위 변수를 사용 하 여 [ `any` 하거나 `all` ](search-query-odata-collection-operators.md)합니다. 위의 Lucene 쿼리 오리곤 주에 있는 다른 도시와 함께 "포틀랜드, Maine" 및 "오리건 포틀랜드"를 포함 하는 문서를 반환 합니다. 각 절이 전체 문서에서 해당 필드의 모든 값에 적용 되므로 "현재 하위 document" 라는 개념이 없기 때문에 발생 합니다. 이 대 한 자세한 내용은 참조 하세요. [Azure Search의 이해 OData 컬렉션 필터](search-query-understand-collection-filters.md)합니다.

## <a name="selecting-complex-fields"></a>복잡 한 필드를 선택합니다.

`$select` 검색 결과에 반환 되는 필드 선택 매개 변수를 사용 합니다. 복잡 한 필드의 특정 하위 필드를 선택 하려면이 매개 변수를 사용 하려면 부모 필드 및 하위 필드 구분 하 여 슬래시를 포함 (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

검색 결과에서 원하는 경우 필드를 인덱스의 조회 가능으로 표시 되어야 합니다. 조회 가능으로 표시 된 필드에만에서 사용할 수는 `$select` 문입니다.

## <a name="filter-facet-and-sort-complex-fields"></a>필터, 패싯 및 복잡 한 필드 정렬

동일 [OData 경로 구문](query-odata-filter-orderby-syntax.md) 필터링에 사용 되 고 한 검색 패싯, 정렬 및 검색 요청에서 필드를 선택에 사용할 수 있습니다. 복합 형식에 하위 필드 정렬 가능으로 표시 될 수 있습니다 또는 패싯 가능을 제어 하는 규칙이 적용 됩니다. 이러한 규칙에 대 한 자세한 내용은 참조는 [인덱스 만들기 API 참조](https://docs.microsoft.com/rest/api/searchservice/create-index#request)합니다.

### <a name="faceting-sub-fields"></a>하위 필드 패싯

모든 하위 필드 형식이 아니면 패싯 가능으로 표시할 수 있습니다 `Edm.GeographyPoint` 또는 `Collection(Edm.GeographyPoint)`합니다.

하위 문서 (대화방) 복잡 한 컬렉션의 부모 문서 (호텔)에 대 한 패싯 결과에 반환 된 문서 수 계산 됩니다. 예를 들어 호텔에 "제품군" 형식의 20 방은 것으로 가정 합니다. 이 패싯 매개 변수를 지정 된 `facet=Rooms/Type`, 패싯 수는 호텔의 대화방에 대 한 20 하지에 하나가 됩니다.

### <a name="sorting-complex-fields"></a>복잡 한 필드를 정렬합니다.

정렬 작업 문서 (호텔) 및 하위 문서가 아닌 (대화방)에 적용 됩니다. 방 같은 복합 형식 컬렉션에 있는 경우에 대화방에 전혀 정렬할 수 없습니다는 점에 주의 해야 합니다. 사실 모든 컬렉션은 정렬할 수 없습니다.

정렬 작업 필드는 간단한 필드 또는 복합 형식의 하위 필드를 필드에는 각 문서에 단일 값을 포함 하는 경우 작동 합니다. 예를 들어 `Address/City` 호텔 당 하나의 주소를 따라서 있기 때문에 정렬 가능한 일 수 `$orderby=Address/City` 호텔 도시별으로 정렬 됩니다.

### <a name="filtering-on-complex-fields"></a>복잡 한 필드 필터링

필터 식에서 복잡 한 필드의 하위 필드를 참조할 수 있습니다. 동일한 삼아 [OData 경로 구문](query-odata-filter-orderby-syntax.md) 패싯, 정렬 및 필드를 선택에 사용 되는 합니다. 예를 들어, 다음 필터는 캐나다의 모든 호텔을 반환 합니다.

    $filter=Address/Country eq 'Canada'

복잡 한 컬렉션 필드를 필터링 하려면 사용할 수 있습니다는 **람다 식** 사용 하 여를 [ `any` 하 고 `all` 연산자](search-query-odata-collection-operators.md)합니다. 이런 경우는 **범위 변수** 람다 식은 하위 필드를 사용 하 여 개체입니다. 표준 OData 경로 구문 사용 하 여 해당 하위 필드에 참조할 수 있습니다. 예를 들어, 다음 필터는 deluxe 대화방을 하나 이상 있는 모든 호텔 및 모든 비-흡연 대화방을 반환 합니다.

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

최상위 간단한 필드를 사용 하 여 복잡 한 필드의 간단한 하위 필드 수만 포함 될 필터에 있는 경우 처럼 합니다 **필터링 가능** 특성이로 설정 `true` 인덱스 정의의 합니다. 자세한 내용은 참조는 [인덱스 만들기 API 참조](https://docs.microsoft.com/rest/api/searchservice/create-index#request)합니다.

## <a name="next-steps"></a>다음 단계

시도 합니다 [호텔 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) 에 **데이터를 가져올** 마법사. Cosmos DB 연결 정보 데이터에 액세스 하는 추가 정보에 제공 해야 합니다.

해당 정보를가지고 사용 하 여 마법사의 첫 번째 단계 새 Azure Cosmos DB 데이터 원본을 만드는 것입니다. 추가 마법사에서 대상 인덱스 페이지에 도달할 때 표시 됩니다 인덱스 복합 형식입니다. 이 인덱스에 로드 만들고 그런 다음 새 구조를 이해 하는 쿼리를 실행 합니다.

> [!div class="nextstepaction"]
> [가져오기, 인덱싱 및 쿼리 빠른 시작: 포털 마법사](search-get-started-portal.md)