---
title: 최신 Azure Search Service REST API 버전으로 업그레이드 - Azure Search
description: API 버전의 차이를 검토하고 기존 코드를 최신 Azure Search Service REST API 버전으로 마이그레이션하는 데 필요한 작업을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 286d8bbc01b5916e842c196aed5a49ef1c76bc3c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025193"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>최신 Azure Search Service REST API 버전으로 업그레이드
이전 버전의를 사용 하는 경우는 [Azure Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/),이 문서를 2019-05-06 일반적으로 사용 가능한 최신 API 버전을 사용 하도록 응용 프로그램을 업그레이드할 수 있습니다.

버전 2019-05-06 REST API의 이전 버전에서 변경 된 일부를 포함합니다. 이는 대부분 이전 버전과 호환되기 때문에 이전에 사용하던 버전에 따라 간단히 코드를 변경할 수 있습니다. [업그레이드 하는 단계](#UpgradeSteps) 새 기능을 사용 하는 데 필요한 코드 변경 내용을 간략하게 설명 합니다.

> [!NOTE]
> Azure Search 서비스 인스턴스를 포함 하 여 이전 범위의 REST API 버전을 지원 합니다. 해당 API 버전을 사용 하 여 계속할 수 있지만 새 기능에 액세스할 수 있도록 최신 버전으로 코드를 마이그레이션하는 것이 좋습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>2019-05-06 버전의 새로운 기능
버전 2019-05-06 Azure Search 서비스 REST API의 최신 일반 공급 릴리스입니다. 이 API 버전에서 일반적으로 사용 가능한 상태로 전환 하는 기능은 다음과 같습니다.

* [자동 완성](index-add-suggesters.md) 미리 입력 기능은 부분적으로 지정 된 용어 입력을 완료 합니다.

* [복합 형식](search-howto-complex-data-types.md) 구조화 된 개체 데이터를 Azure Search 인덱스에 대 한 기본 지원을 제공 합니다.

* [구문 분석 모드 JsonLines](search-howto-index-json-blobs.md)일부인 인덱싱, Azure Blob의 줄 바꿈으로 구분 된 JSON 엔터티 당 검색 문서를 하나 만듭니다.

* [Cognitive Search](cognitive-search-concept-intro.md) 에서는 Cognitive Services의 인공 지능 보강 엔진을 활용 하는 인덱싱을 제공 합니다.

몇 가지 미리 보기 기능 릴리스에이 일반적으로 사용 가능한 업데이트와 일치합니다. 새 미리 보기 기능 목록을 참조 하세요 [검색 REST api-버전 2019-05-06-미리 보기](search-api-preview.md)합니다.

## <a name="breaking-changes"></a>주요 변경 내용

Api 버전에는 다음과 같은 기능을 포함 하는 기존 코드의 연결이 끊어집니다 2019-05-06 =.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB 용 인덱서 데이터 원본은 이제 "type": "cosmosdb"

사용 중인 경우는 [Cosmos DB 인덱서](search-howto-index-cosmosdb.md )를 변경 해야 `"type": "documentdb"` 를 `"type": "cosmosdb"`입니다.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>인덱서 실행 결과 오류 상태를 더 이상

: 인덱서 실행에 대 한 오류 구조를 이전에 `status` 요소입니다. 이 요소는 유용한 정보를 제공 하지 않는 했습니다 때문에 제거 되었습니다.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>인덱서 데이터 원본 API는 더 이상 연결 문자열을 반환

API에서 데이터 원본 API 2019-05-06 및 2019-05-06-미리 보기부터 버전 모든 REST 작업의 응답에 연결 문자열이 더 이상 반환 합니다. 이전 API 버전에서는 POST를 사용 하 여 만든 데이터 원본에 대 한 Azure Search 반환 **201** 뒤에 일반 텍스트로 연결 문자열을 포함 하는 OData 응답 합니다.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>라는 엔터티 인식 cognitive 기술은 이제 사용 되지 않습니다.

호출 하는 경우 [이름을 엔터티 인식](cognitive-search-skill-named-entity-recognition.md) 코드에서 이런 기술을 갖춘 경우 호출이 실패 합니다. 대체 기능은 [엔터티 인식](cognitive-search-skill-entity-recognition.md)합니다. 기술 참조 기타 변경 내용으로 바꾸려면 수 있어야 합니다. API 서명을 두 버전 모두 동일합니다. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
이전 GA 버전에서 업그레이드 하는 경우 2017 년 11 월 11 또는 2016-09-01, 아마도 필요가 없습니다 변경한 코드에 이외의 다른 버전 번호를 변경 합니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.

* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

이러한 경우에 해당하는 경우 코드를 적절하게 변경해야 합니다. 그렇지 않은 경우 변경 하지 않고 필요는 사용을 시작 하려는 경우가 아니면 합니다 [새로운 기능](#WhatsNew) 2019-05-06 버전입니다.

미리 보기 API 버전에서 업그레이드 하는 경우 위의 적용 되지만 몇 가지 미리 보기 기능 2019-05-06 버전에서 사용할 수 없는 알고 있어야 합니다.

* [쿼리 "처럼 이"](search-more-like-this.md) 미리 보기 전용 기능을 계속 합니다.

이러한 기능을 사용 하는 코드를 해당 사용을 제거 하지 않고 API 버전 2019-05-06로 업그레이드할 수 됩니다.

> [!IMPORTANT]
> 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않습니다.
> 

### <a name="upgrading-complex-types"></a>복합 형식 업그레이드

이전 미리 보기 API 버전 2017-11-11-미리 보기 또는 2016-09-01-미리 보기를 사용 하 여 복합 형식을 사용 하는 코드, 하는 경우 몇 가지 새로운 기능과 변경 된 제한이 있습니다 버전에서 2019-05-06 알아야 할 필요는:

+ 하위 필드 및 복잡 한 컬렉션 인덱스 당 수의 깊이 대 한 제한은 낮췄습니다. 업데이트 하거나 다시 보내려고 하면 미리 보기 api 버전을 사용 하 여 이러한 제한을 초과 하는 인덱스를 만든 경우 버전 2019-05-06 API를 사용 하 여 실패 합니다. 이 해당 하는 경우 새로운 제한 내에 다음 인덱스 다시 작성 하 여 스키마를 다시 디자인 해야 합니다.

+ Api-version 2019-05-06 문서당 복잡 한 컬렉션의 요소 수에 새 제한이 있습니다. 미리 보기 api 버전을 사용 하 여 이러한 제한을 초과 하는 문서를 사용 하 여 인덱스를 만든 경우 2019-05-06-api-version을 사용 하 여 해당 데이터를 인덱스 다시 작성 하려는 모든 시도가 실패 합니다. 이 해당 하는 경우 데이터를 다시 만드는 전에 문서당 복합 컬렉션 요소의 수를 줄이기 위해 해야 합니다.

자세한 내용은 [Azure Search의 서비스 제한](search-limits-quotas-capacity.md)합니다.

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>이전 복합 형식의 구조를 업그레이드 하는 방법

코드를 사용 중인 경우 복합 형식은 이전 미리 보기 API 버전 중 하나를 사용 하 여 다음과 같은 인덱스 정의 형식으로 사용할 수 있습니다.

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

인덱스 필드 정의 대 한 최신 트리 형식 API 버전 2017-11-11-미리 보기에서 도입 되었습니다. 새 형식으로 복잡 한 각 필드에 필드 컬렉션이 해당 하위 필드 정의 되어 있는 있습니다. Api 버전 2019-05-06에서이 새 형식은 단독으로 사용 됩니다 하 고 만들거나 이전 형식을 사용 하 여 인덱스를 업데이트 하려는 시도 실패 합니다. 이전 형식을 사용 하 여 생성 된 인덱스에 있는 경우에 API-VERSION 2019-05-06을 사용 하 여 관리할 수 전에 새 형식으로 업데이트 하려면 API 버전 2017-11-11-미리 보기를 사용 하는 것이 해야 합니다.

API 버전 2017-11-11-미리 보기를 사용 하 여 다음 단계를 사용 하 여 새 형식으로 "단순" 인덱스를 업데이트할 수 있습니다.

1. 인덱스를 검색 하는 GET 요청을 수행 합니다. 새 형식으로 이미 있으면 작업이 끝났습니다.

2. 새 형식으로 "기본" 형식에서 인덱스를 변환 합니다. 이 문서 작성 당시에 없는 샘플 코드는이 대 한 코드를 작성 해야 합니다.

3. 새 형식으로 인덱스를 업데이트 하는 PUT 요청을 수행 합니다. 이 업데이트 인덱스 API에 의해 허용 되지 않으므로 인덱스 필드의 검색 가능성/filterability 등의 기타 세부 정보를 변경할 필요가 있는지 확인 합니다.

> [!NOTE]
> Azure portal에서 이전 "평면" 형식을 사용 하 여 만든 인덱스를 관리 하는 것이 불가능 합니다. 가장 빠른 편의 "나무" 표현과 "평면" 표현에서 인덱스를 업그레이드 하세요.

## <a name="next-steps"></a>다음 단계

Azure Search 서비스 REST API 참조 설명서를 검토 합니다. 문제가 있는 경우 미국에서 지원 요청 [StackOverflow](https://stackoverflow.com/) 하거나 [지원에 문의](https://azure.microsoft.com/support/community/?product=search)합니다.

> [!div class="nextstepaction"]
> [Search 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)

