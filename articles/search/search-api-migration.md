---
title: REST API 버전 업그레이드
titleSuffix: Azure Cognitive Search
description: API 버전의 차이점을 검토하고 기존 코드를 최신 Azure Cognitive Search 서비스 REST API 버전으로 마이그레이션하는 데 필요한 작업을 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112159"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>최신 Azure 인지 검색 서비스 REST API 버전으로 업그레이드

이전 버전의 [Search REST API를](https://docs.microsoft.com/rest/api/searchservice/)사용하는 경우 이 문서에서는 응용 프로그램을 업그레이드하여 일반적으로 사용할 수 있는 최신 API 버전인 2019-05-06을 사용하는 데 도움이 됩니다.

REST API의 버전 2019-05-06에는 이전 버전의 일부 변경 내용이 포함되어 있습니다. 이는 대부분 이전 버전과 호환되기 때문에 이전에 사용하던 버전에 따라 간단히 코드를 변경할 수 있습니다. [업그레이드 단계는](#UpgradeSteps) 새 기능을 사용하는 데 필요한 코드 변경 내용을 간략하게 설명합니다.

> [!NOTE]
> Azure 인지 검색 서비스 인스턴스는 이전 버전을 포함하여 다양한 REST API 버전을 지원합니다. 이러한 API 버전을 계속 사용할 수 있지만 새 기능에 액세스할 수 있도록 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>버전 2019-05-06의 새로운 내용
버전 2019-05-06REST API의 최신 일반적으로 사용할 수 있는 릴리스입니다. 이 API 버전에서 일반적으로 사용 가능한 상태로 전환된 기능은 다음과 같습니다.

* [자동 완성은](index-add-suggesters.md) 부분적으로 지정된 용어 입력을 완료하는 유형 선 기능입니다.

* [복합 형식은](search-howto-complex-data-types.md) 검색 인덱스의 구조화 된 개체 데이터에 대한 기본 지원을 제공합니다.

* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드는](search-howto-index-json-blobs.md)줄 바이라고 구분되는 JSON 엔터티당 하나의 검색 문서를 만듭니다.

* [AI 농축은](cognitive-search-concept-intro.md) 코그너티브 서비스의 AI 강화 엔진을 활용하는 인덱싱을 제공합니다.

몇 가지 미리 보기 기능 릴리스는 일반적으로 사용 가능한 업데이트와 일치합니다. 새 미리 보기 기능 목록을 검토하려면 [REST API 버전 검색 2019-05-06-미리 보기를](search-api-preview.md)참조하십시오.

## <a name="breaking-changes"></a>호환성이 손상되는 변경

다음 기능이 포함된 기존 코드는 api 버전=2019-05-06에서 중단됩니다.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure 코스모스 DB용 인덱서 - 데이터 원본은 이제 "유형"입니다: "cosmosdb"

[Cosmos DB 인덱서를](search-howto-index-cosmosdb.md )사용하는 경우 `"type": "documentdb"` 을 `"type": "cosmosdb"`변경해야 합니다.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>인덱서 실행 결과 오류에 더 이상 상태가 없습니다.

인덱서 실행에 대한 오류 `status` 구조에는 이전에 요소가 있었습니다. 이 요소는 유용한 정보를 제공하지 않았기 때문에 제거되었습니다.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>인덱서 데이터 소스 API가 더 이상 연결 문자열을 반환하지 않습니다.

API 버전 2019-05-06 및 2019-05-06-Preview 이후에서 데이터 원본 API는 REST 작업의 응답으로 더 이상 연결 문자열을 반환하지 않습니다. 이전 API 버전에서는 POST를 사용하여 만든 데이터 원본의 경우 Azure Cognitive Search가 **201을** 반환한 다음 일반 텍스트에 연결 문자열이 포함된 OData 응답이 뒤따랐습니다.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>명명된 엔티티 인식 인지 기술이 중단되었습니다.

코드에서 [이름 엔터티 인식](cognitive-search-skill-named-entity-recognition.md) 기술을 호출하면 호출이 실패합니다. 대체 기능은 [엔터티 인식입니다.](cognitive-search-skill-entity-recognition.md) 기술 참조를 다른 변경 내용없이 바꿀 수 있어야 합니다. API 서명은 두 버전 모두에 대해 동일합니다. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
이전 GA 버전인 2017-11-11 또는 2016-09-01에서 업그레이드하는 경우 버전 번호를 변경하는 것 외에는 코드를 변경할 필요가 없습니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.

* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

이러한 경우에 해당하는 경우 코드를 적절하게 변경해야 합니다. 그렇지 않으면 버전 2019-05-06의 [새로운 기능을](#WhatsNew) 사용하기 를 원하지 않는 한 변경할 필요가 없습니다.

미리 보기 API 버전에서 업그레이드하는 경우 위의 경우에도 적용되지만 일부 미리 보기 기능은 버전 2019-05-06에서 사용할 수 없다는 점에 유의해야 합니다.

* ["이보다 더 나은" 쿼리](search-more-like-this.md)
* [CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
* [코스모스 DB 인덱서에 대한 몽고DB API 지원](search-howto-index-cosmosdb.md)

코드에서 이러한 기능을 사용하는 경우 API 버전 2019-05-06으로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않습니다.
> 

### <a name="upgrading-complex-types"></a>복잡한 유형 업그레이드

코드가 이전 미리 보기 API 버전 2017-11-11-Preview 또는 2016-09-01-Preview에서 복잡한 형식을 사용하는 경우 버전 2019-05-06에서 몇 가지 새롭고 변경된 제한이 있습니다.

+ 하위 필드의 깊이에 대한 제한과 인덱스당 복잡한 컬렉션 수가 낮아졌습니다. 미리 보기 api 버전을 사용하여 이러한 제한을 초과하는 인덱스를 만든 경우 API 버전 2019-05-06을 사용하여 업데이트하거나 다시 만들려는 모든 시도가 실패합니다. 이 경우 새 제한에 맞게 스키마를 다시 디자인한 다음 인덱스를 다시 작성해야 합니다.

+ api 버전 2019-05-06에는 문서당 복잡한 컬렉션의 요소 수에 대한 새로운 제한이 있습니다. 미리 보기 api 버전을 사용하여 이러한 제한을 초과하는 문서로 인덱스를 만든 경우 api 버전 2019-05-06을 사용하여 해당 데이터를 다시 인덱싱하려는 시도가 실패합니다. 이 경우 데이터를 다시 인덱싱하기 전에 문서당 복잡한 컬렉션 요소 수를 줄여야 합니다.

자세한 내용은 [Azure 인지 검색에 대한 서비스 제한을](search-limits-quotas-capacity.md)참조하십시오.

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>이전 복잡한 유형 구조를 업그레이드하는 방법

코드가 이전 미리 보기 API 버전 중 하나를 사용하여 복잡한 형식을 사용하는 경우 다음과 같은 인덱스 정의 형식을 사용하고 있을 수 있습니다.

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

API 버전 2017-11-11-Preview에서는 인덱스 필드를 정의하기 위한 트리와 같은 최신 형식이 도입되었습니다. 새 형식의 각 복합 필드에는 하위 필드가 정의된 필드 컬렉션이 있습니다. API 버전 2019-05-06에서는 이 새 형식이 단독으로 사용되며 이전 형식을 사용하여 인덱스를 만들거나 업데이트하려고 하면 실패합니다. 이전 형식을 사용하여 만든 인덱스가 있는 경우 API 버전 2019-05-06을 사용하여 관리하려면 API 버전 2017-11-11-Preview를 사용하여 새 형식으로 업데이트해야 합니다.

API 버전 2017-11-11-미리 보기를 사용하여 다음 단계로 "플랫" 인덱스를 새 형식으로 업데이트할 수 있습니다.

1. GET 요청을 수행하여 인덱스를 검색합니다. 이미 새 형식이면 완료된 것입니다.

2. 인덱스를 "플랫" 형식에서 새 형식으로 변환합니다. 이 작성 당시에는 사용할 수 있는 샘플 코드가 없으므로 이에 대한 코드를 작성해야 합니다.

3. PUT 요청을 수행하여 인덱스를 새 형식으로 업데이트합니다. 업데이트 인덱스 API에서는 허용되지 않으므로 필드의 검색 가능성/필터링 가능성과 같은 인덱스의 다른 세부 정보를 변경하지 않도록 하십시오.

> [!NOTE]
> Azure 포털에서 이전 "플랫" 형식으로 만든 인덱스를 관리할 수 없습니다. 인덱스를 "플랫" 표현에서 "트리" 표현으로 업그레이드하십시오.

## <a name="next-steps"></a>다음 단계

REST API 참조 설명서를 검토합니다. 문제가 발생하면 [StackOverflow에](https://stackoverflow.com/) 대한 도움을 요청하거나 지원 팀에 [문의하십시오.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)

