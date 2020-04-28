---
title: 업그레이드 REST API 버전
titleSuffix: Azure Cognitive Search
description: API 버전의 차이점을 검토 하 고 기존 코드를 최신 Azure Cognitive Search service REST API 버전으로 마이그레이션하는 데 필요한 작업에 대해 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112159"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>최신 Azure Cognitive Search service REST API 버전으로 업그레이드

이전 버전의 [검색 REST API](https://docs.microsoft.com/rest/api/searchservice/)를 사용 하는 경우이 문서를 사용 하면 일반적으로 제공 되는 최신 API 버전인 2019-05-06을 사용 하도록 응용 프로그램을 업그레이드할 수 있습니다.

REST API 버전 2019-05-06에는 이전 버전에서 변경 된 내용이 포함 되어 있습니다. 이는 대부분 이전 버전과 호환되기 때문에 이전에 사용하던 버전에 따라 간단히 코드를 변경할 수 있습니다. [업그레이드 단계](#UpgradeSteps) 에서는 새 기능을 사용 하는 데 필요한 코드 변경 사항을 간략하게 설명 합니다.

> [!NOTE]
> Azure Cognitive Search 서비스 인스턴스는 이전 버전을 비롯 한 다양 한 REST API 버전을 지원 합니다. 이러한 API 버전을 계속 사용할 수 있지만 새 기능에 액세스할 수 있도록 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>버전 2019-05-06의 새로운 기능
버전 2019-05-06은 일반적으로 사용 가능한 최신 REST API 릴리스입니다. 이 API 버전에서 일반적으로 사용 가능한 상태로 전환 된 기능은 다음과 같습니다.

* [자동 완성](index-add-suggesters.md) 은 부분적으로 지정 된 용어 입력을 완료 하는 미리 입력 기능입니다.

* [복합 형식은](search-howto-complex-data-types.md) 검색 인덱스의 구조적 개체 데이터에 대 한 기본 지원을 제공 합니다.

* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드](search-howto-index-json-blobs.md)는 JSON 엔터티 마다 줄 바꿈으로 구분 된 하나의 검색 문서를 만듭니다.

* [Ai 보강](cognitive-search-concept-intro.md) 는 Cognitive Services ai 보강 엔진을 활용 하는 인덱싱을 제공 합니다.

몇 가지 미리 보기 기능 릴리스는 일반적으로 사용 가능한 업데이트와 일치 합니다. 새 미리 보기 기능 목록을 검토 하려면 [REST Api 검색-버전 2019-05-06-미리 보기](search-api-preview.md)를 참조 하세요.

## <a name="breaking-changes"></a>호환성이 손상되는 변경

다음 기능을 포함 하는 기존 코드는 api-version = 2019-05-06에서 중단 됩니다.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB-datasource의 인덱서는 이제 "type": "cosmosdb"입니다.

[Cosmos DB 인덱서](search-howto-index-cosmosdb.md )를 사용 하는 경우를로 `"type": "documentdb"` `"type": "cosmosdb"`변경 해야 합니다.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>인덱서 실행 결과 오류에 상태가 더 이상 없습니다.

이전에 인덱서 실행에 대 한 오류 구조 `status` 에 요소가 있습니다. 이 요소는 유용한 정보를 제공 하지 않기 때문에 제거 되었습니다.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>인덱서 데이터 원본 API는 더 이상 연결 문자열을 반환 하지 않습니다.

API 버전 2019-05-06 및 2019-05-06-미리 보기부터 데이터 원본 API는 더 이상 REST 작업에 대 한 응답으로 연결 문자열을 반환 하지 않습니다. 이전 API 버전에서 POST를 사용 하 여 만든 데이터 원본에 대해 Azure Cognitive Search는 **201** 을 반환한 다음 OData 응답을 반환 합니다. 여기에는 일반 텍스트로 연결 문자열이 포함 되어 있습니다.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>명명 된 엔터티 인식 인식 기술이 이제 중단 되었습니다.

코드에서 [이름 엔터티 인식](cognitive-search-skill-named-entity-recognition.md) 기술을 호출 하면 호출이 실패 합니다. 대체 기능은 [엔터티 인식](cognitive-search-skill-entity-recognition.md)입니다. 기술 참조를 다른 변경 내용 없이 바꿀 수 있습니다. API 서명은 두 버전에 대해 동일 합니다. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
이전 GA 버전 2017-11-11 또는 2016-09-01에서 업그레이드 하는 경우 버전 번호를 변경 하는 것 외에는 코드를 변경할 필요가 없습니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.

* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

이러한 경우에 해당하는 경우 코드를 적절하게 변경해야 합니다. 그렇지 않으면 버전 2019-05-06의 [새 기능](#WhatsNew) 을 사용 하 여 시작 하지 않는 한 변경 하지 않아도 됩니다.

Preview API 버전에서 업그레이드 하는 경우에도 위의 내용이 적용 되지만 버전 2019-05-06에서는 일부 미리 보기 기능을 사용할 수 없습니다.

* ["이보다 더 나은" 쿼리](search-more-like-this.md)
* [CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
* [Cosmos DB 인덱서에 대 한 MongoDB API 지원](search-howto-index-cosmosdb.md)

코드에서 이러한 기능을 사용 하는 경우 사용을 제거 하지 않고 API 버전 2019-05-06로 업그레이드할 수 없습니다.

> [!IMPORTANT]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않습니다.
> 

### <a name="upgrading-complex-types"></a>복합 형식 업그레이드

코드에서 이전 preview API 버전 2017-11-11-Preview 또는 2016-09-01-Preview와 함께 복합 형식을 사용 하는 경우, 버전 2019-05-06에 몇 가지 새로운 제한 사항이 있으며이에 대해 알고 있어야 합니다.

+ 하위 필드 수준에 대 한 제한과 인덱스 당 복합 컬렉션 수가 감소 했습니다. Preview api 버전을 사용 하 여 이러한 제한을 초과 하는 인덱스를 만든 경우 API 버전 2019-05-06을 사용 하 여 해당 인덱스를 업데이트 하거나 다시 만들려고 하면 실패 합니다. 이 경우 새 제한에 맞게 스키마를 다시 디자인 한 다음 인덱스를 다시 작성 해야 합니다.

+ Api 버전 2019-05-06에는 문서 당 복합 컬렉션의 요소 수에 대 한 새로운 제한이 있습니다. Preview api 버전을 사용 하 여 이러한 제한을 초과 하는 문서를 사용 하 여 인덱스를 만든 경우에는 api-version 2019-05-06를 사용 하 여 해당 데이터를 다시 인덱싱 하려고 하면 실패 합니다. 사용자에 게 적용 되는 경우 데이터를 인덱스를 만들기 전에 문서당 복합 컬렉션 요소 수를 줄여야 합니다.

자세한 내용은 [Azure Cognitive Search에 대 한 서비스 제한](search-limits-quotas-capacity.md)을 참조 하세요.

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>이전 복합 형식 구조를 업그레이드 하는 방법

코드에서 이전 preview API 버전 중 하나를 사용 하 여 복합 형식을 사용 하는 경우 다음과 같은 인덱스 정의 형식을 사용할 수 있습니다.

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

인덱스 필드를 정의 하는 최신 트리와 유사한 형식은 API 버전 2017-11-11-미리 보기에서 도입 되었습니다. 새 형식에서 각 복합 필드에는 하위 필드가 정의 된 fields 컬렉션이 있습니다. API 버전 2019-05-06에서는이 새 형식이 독점적으로 사용 되며 이전 형식을 사용 하 여 인덱스를 만들거나 업데이트 하는 데 실패 합니다. 이전 형식을 사용 하 여 인덱스를 만든 경우 api 버전 2017-11-11-Preview를 사용 하 여 API 버전 2019-05-06을 사용 하 여 관리 하려면 먼저 새 형식으로 업데이트 해야 합니다.

API 버전 2017-11-11-Preview를 사용 하 여 다음 단계를 수행 하 여 "플랫" 인덱스를 새 형식으로 업데이트할 수 있습니다.

1. GET 요청을 수행 하 여 인덱스를 검색 합니다. 이미 새 형식이 면 완료 됩니다.

2. 인덱스를 "flat" 형식에서 새 형식으로 변환 합니다. 이 문서를 작성할 때 사용할 수 있는 샘플 코드가 없기 때문에이에 대 한 코드를 작성 해야 합니다.

3. PUT 요청을 수행 하 여 인덱스를 새 형식으로 업데이트 합니다. 인덱스 업데이트 API에서 허용 되지 않기 때문에 필드의 높이려는/filterability 같은 인덱스의 다른 세부 정보는 변경 하지 않도록 해야 합니다.

> [!NOTE]
> Azure Portal에서 이전 "플랫" 형식을 사용 하 여 만든 인덱스는 관리할 수 없습니다. 가장 빠른 편의를 위해 "플랫" 표현에서 "트리" 표현으로 인덱스를 업그레이드 하세요.

## <a name="next-steps"></a>다음 단계

검색 REST API 참조 설명서를 검토 합니다. 문제가 발생 하는 경우 [Stackoverflow](https://stackoverflow.com/) 또는 [지원 담당자에 게](https://azure.microsoft.com/support/community/?product=search)도움을 요청 합니다.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)

