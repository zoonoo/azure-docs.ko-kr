---
title: 인덱스 만들기
titleSuffix: Azure Cognitive Search
description: 스키마 정의 및 물리적 데이터 구조를 포함 하 여 Azure Cognitive Search의 인덱싱 개념 및 도구를 소개 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 96594d573c308727217f537e5421dcb79f02c2ff
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433797"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 인덱스 만들기

Cognitive Search은 *검색 인덱스* 에서 전체 텍스트 및 필터링 된 쿼리에 사용 되는 검색 가능한 콘텐츠를 저장 합니다. 인덱스는 스키마에 의해 정의 되 고 서비스에 저장 되며, 데이터 가져오기는 두 번째 단계로 수행 됩니다. 

인덱스는 *검색 문서* 를 포함 합니다. 개념상, 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 소매점은 각 제품에 대 한 문서를 포함할 수 있으며, 뉴스 조직에는 각 문서에 대 한 문서가 포함 될 수 있습니다. 이러한 개념을 보다 친숙 한 데이터베이스에 매핑: *검색 인덱스* 는 *테이블과* 같으며 *문서* 는 테이블의 *행* 과 거의 동일 합니다.

## <a name="whats-an-index-schema"></a>인덱스 스키마 란?

인덱스의 물리적 구조는 스키마에 의해 결정 됩니다. ' Fields ' 컬렉션은 일반적으로 각 필드의 이름을 지정 하 고, [데이터 형식을](/rest/api/searchservice/Supported-data-types)할당 하 고, 사용 방법을 결정 하는 허용 가능한 동작으로 특성을 지정 하는 인덱스의 가장 큰 부분입니다.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

다른 요소는 간결 하 게 축소 되지만 다음 링크를 통해 세부 정보를 제공할 수 있습니다. [확인 기](index-add-suggesters.md), [점수 매기기 프로필](index-add-scoring-profiles.md), 분석기에서 지 원하는 언어 규칙 또는 기타 특성에 따라 토큰에 문자열을 처리 하는 데 사용 되는 [분석기](search-analyzers.md) 및 [CORS (크로스-원본 원격 스크립팅)](#corsoptions) 설정입니다.

## <a name="choose-a-client"></a>클라이언트 선택

여러 가지 방법으로 검색 인덱스를 만들 수 있습니다. 초기 개발 및 개념 증명 테스트를 위해 Azure Portal 또는 Sdk를 권장 합니다.

개발 하는 동안 자주 다시 작성 하는 계획을 세워야 합니다. 물리적 구조는 서비스에서 만들어지므로 기존 필드 정의를 대부분 수정 하려면 [인덱스를 삭제 하 고 다시 만드는](search-howto-reindex.md) 것이 필요 합니다. 보다 빠르게 다시 작성할 수 있도록 데이터 하위 집합을 사용하는 방안을 고려해 볼 수 있습니다.

### <a name="permissions"></a>사용 권한

GET 요청 정의를 포함 하 여 검색 인덱스와 관련 된 모든 작업에는 요청에 대 한 [관리 api 키](search-security-api-keys.md) 가 필요 합니다.

### <a name="limits"></a>제한

모든 [서비스 계층](search-limits-quotas-capacity.md#index-limits) 은 만들 수 있는 개체의 수를 제한 합니다. 무료 계층을 시험 하는 경우 지정 된 시간에 3 개의 인덱스만 가질 수 있습니다.

### <a name="use-azure-portal-to-create-a-search-index"></a>Azure Portal를 사용 하 여 검색 인덱스 만들기

포털은 검색 인덱스를 만드는 두 가지 옵션, 즉 [**데이터 가져오기 마법사**](search-import-data-portal.md) 와 인덱스 스키마를 지정 하기 위한 필드를 제공 하는 **인덱스 추가** 를 제공 합니다. 마법사는 인덱서, 데이터 원본 및 데이터 로드를 만들어 추가 작업을 압축 합니다. 원하는 것 보다 많은 경우에는 **인덱스 추가** 또는 다른 방법만 사용 하면 됩니다.

다음 스크린샷은 포털에서 **추가 인덱스** 를 찾을 수 있는 위치를 보여 줍니다. **데이터 가져오기** 는 바로 다음 도어입니다.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="인덱스 추가 명령" border="true":::

> [!Tip]
> 포털을 통한 인덱스 디자인은 숫자 필드에서 전체 텍스트 검색 기능을 허용 하지 않는 등의 특정 데이터 형식에 대 한 요구 사항 및 스키마 규칙을 적용 합니다. 인덱스를 사용할 수 있으면 포털에서 JSON을 복사 하 여 솔루션에 추가할 수 있습니다.

### <a name="use-a-rest-client"></a>REST 클라이언트 사용

Postman과 Visual Studio Code (Azure Cognitive Search 용 확장 포함) 모두 검색 인덱스 클라이언트로 작동할 수 있습니다. 이러한 도구 중 하나를 사용 하 여 검색 서비스에 연결 하 고 [Create Index (REST)](/rest/api/searchservice/create-index) 요청을 보낼 수 있습니다. 개체를 만들기 위한 REST 클라이언트를 보여 주는 다양 한 자습서와 예제가 있습니다. 

각 클라이언트에 대해 알아보려면 다음 문서 중 하나를 시작 합니다.

+ [REST 및 Postman을 사용 하 여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

인덱스 요청을 작성 하는 데 도움이 필요한 경우 [인덱스 작업 (REST)](/rest/api/searchservice/index-operations) 을 참조 하세요.

### <a name="use-an-sdk"></a>SDK 사용

Cognitive Search의 경우 Azure Sdk는 일반적으로 사용 가능한 기능을 구현 합니다. 따라서 Sdk를 사용 하 여 검색 인덱스를 만들 수 있습니다. 이러한 모든 항목은 인덱스를 만들고 업데이트 하는 메서드가 포함 된 **Searchindexclient** 를 제공 합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-검색-dotnet-샘플/빠른 시작/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [인덱스](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>필드 정의

검색 문서는 컬렉션에 의해 정의 됩니다 `fields` . 쿼리 및 키에 대 한 필드가 필요 합니다. 또한 필터, 패싯 및 정렬을 지원 하기 위한 필드도 필요 합니다. 사용자에 게 표시 되지 않는 데이터에 대 한 필드도 필요할 수도 있습니다. 예를 들어, 검색 순위를 수정 하는 데 사용할 수 있는 수익 여백 또는 마케팅 판촉에 대 한 필드를 원할 수 있습니다.

Edm. String 형식의 필드 하나는 문서 키로 지정 해야 합니다. 각 검색 문서를 고유 하 게 식별 하는 데 사용 되며 대/소문자를 구분 합니다. 키를 기준으로 문서를 검색 하 여 세부 정보 페이지를 채울 수 있습니다.

들어오는 데이터가 계층적 이면 중첩 된 구조를 나타내는 [복합 유형](search-howto-complex-data-types.md) 데이터 형식을 할당 합니다. 기본 제공 샘플 데이터 집합 호텔은 각 호텔에 대해 일 대 일 관계를 갖는 주소 (여러 개의 하위 필드 포함)를 사용 하는 복합 형식을 보여 주며, 대화방 복합 컬렉션은 여러 개의 대화방에 연결 되어 있습니다. 

인덱스를 만들기 전에 문자열 필드에 분석기를 할당 합니다. 특정 필드에서 자동 완성을 사용 하려면 확인 기에 대해 동일한 작업을 수행 합니다.

<a name="index-attributes"></a>

### <a name="attributes"></a>특성

필드 특성에 따라 필드가 사용되는 방식(즉, 전체 텍스트 검색, 패싯 탐색, 정렬 작업 등)이 결정됩니다. 

문자열 필드는 "검색 가능" 및 "검색 가능"으로 표시 되는 경우가 많습니다. 검색 결과의 범위를 좁히는 데 사용 되는 필드에는 "정렬 가능한", "필터링 가능" 및 "패싯 가능"가 포함 됩니다.

|attribute|설명|  
|---------------|-----------------|  
|가능한 |전체 텍스트 검색 가능하며, 인덱싱 중에 단어 분리 등의 어휘 분석이 적용됩니다. 검색 가능 필드를 “sunny day” 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 “sunny”와 “day”로 분할됩니다. 자세한 내용은 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)을 참조하세요.|  
|가능 |$filter 쿼리에서 참조됩니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필터링 가능 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 f를 “sunny day”로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. |  
|8601 |기본적으로 시스템은 점수에 따라 결과를 정렬하지만 문서에 있는 필드를 기반으로 정렬하도록 구성할 수 있습니다. 형식의 필드는 `Collection(Edm.String)` "정렬 가능"으로 지정할 수 없습니다. |  
|패싯 가능 |일반적으로 카테고리별 적중 횟수가 포함된 검색 결과를 표시하는 데 사용됩니다(예: 특정 도시의 호텔). 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. `Edm.String`필터링 가능, "정렬 가능" 또는 "패싯 가능" 형식의 필드는 최대 32 킬로바이트 길이입니다. 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 참조하세요.|  
|키인지 |인덱스 내의 문서에 대한 고유 식별자입니다. 정확히 하나의 필드를 키 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다.|  
|조회 가능 |검색 결과에서 필드를 반환할 수 있는지 여부를 결정합니다. *이익률* 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `true` for `key` 로 설정해야 합니다.|  

언제든지 새 필드를 추가할 수 있지만 기존 필드 정의는 인덱스 수명 동안 잠겨 있습니다. 이러한 이유로 개발자는 일반적으로 포털을 사용하여 간단한 인덱스를 만들거나 아이디어를 테스트하거나 포털 페이지를 사용하여 설정을 조회합니다. 인덱스를 쉽게 다시 작성할 수 있도록 코드 기반 접근 방식을 따르는 경우 인덱스 대한 빈번한 반복 디자인이 보다 효율적입니다.

> [!NOTE]
> 인덱스를 작성 하는 데 사용 하는 Api는 다양 한 기본 동작을 포함 합니다. [REST api](/rest/api/searchservice/Create-Index)의 경우 대부분의 특성은 기본적으로 사용 하도록 설정 됩니다. 예를 들어 "검색 가능" 및 "검색 가능"은 문자열 필드에 대해 true 이며,이를 해제 하려는 경우에만 설정 해야 하는 경우가 많습니다. .NET SDK의 경우 반대의 경우도 마찬가지입니다. 명시적으로 설정 하지 않은 모든 속성에서 기본값은 명시적으로 사용 하도록 설정 하지 않은 경우 해당 검색 동작을 사용 하지 않도록 설정 하는 것입니다.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>특성 및 인덱스 크기 (저장소 영향)

인덱스 크기는 업로드 하는 문서의 크기와 확인 기 포함 여부, 개별 필드에 대 한 특성 설정 방법 등의 인덱스 구성에 따라 결정 됩니다. 

다음 스크린샷은 다양한 특성을 조합한 인덱스 스토리지 패턴을 보여줍니다. 인덱스는 데이터 가져오기 마법사를 사용 하 여 쉽게 만들 수 있는 부동산 **샘플 인덱스** 를 기반으로 합니다. 인덱스 스키마는 표시되지 않지만, 인덱스 이름을 보고 특성을 유추할 수 있습니다. 예를 들어 *검색 가능한* 인덱스에는 "검색 가능" 특성이 선택 되어 있고 다른 모든 항목이 없는 경우, realestate-us-sample 검색 가능한 인덱스에는 "검색 *가능* " 특성을 선택 하 고 다른 것은 realestate-us-sample 하지 않습니다.

![특성 선택에 따른 인덱스 크기](./media/search-what-is-an-index/realestate-index-size.png "특성 선택에 따른 인덱스 크기")

이러한 인덱스 변형은 인위적이지만, 특성이 스토리지에 미치는 영향을 광범위하게 비교하는 데 참조할 수 있습니다. "검색 가능"을 설정 하면 인덱스 크기가 증가 하나요? 아니요. **확인 기** 에 필드를 추가 하면 인덱스 크기가 증가 하나요? 예. 

필터링 된 필드와 정렬 된 필드는 토큰화 되지 않으므로 문자 시퀀스를 축 자와 일치 시킬 수 있기 때문에 필드를 필터링 하거나 정렬할 수 있도록 하면 저장소 소비에도 추가 됩니다.

또한 [분석기](search-analyzers.md)의 영향은 위의 표에 반영 되지 않습니다. EdgeNgram 토크를 사용 하 여 축 자 시퀀스 (a, ab, abc, abcd)를 저장 하는 경우 표준 분석기를 사용 하는 경우 인덱스 크기가 보다 커집니다.

> [!Note]
> 저장소 아키텍처는 Azure Cognitive Search의 구현 세부 정보로 간주 되며, 예 고 없이 변경 될 수 있습니다. 현재 동작이 나중에도 유지된다는 보장은 없습니다.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>`corsOptions` 정보

인덱스 스키마에는 설정에 대 한 섹션이 포함 `corsOptions` 됩니다. 브라우저에서는 모든 원본 간 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 원본 간 쿼리를 허용하려면 **corsOptions** 특성을 설정하여 CORS(원본 간 리소스 공유)를 사용하도록 설정합니다. 보안상의 이유로 쿼리 API에서만 CORS가 지원됩니다. 

CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

+ **allowedorigins** (필수): 인덱스에 대 한 액세스 권한이 부여 되는 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://<fully-qualified-domain-name>:<port>` 형식이지만 `<port>`는 대개 생략됩니다. 자세한 내용은 [원본 간 리소스 공유(위키백과)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 참조하세요.

  모든 원본에 대한 액세스를 허용하려면 **allowedOrigins** 배열에서 `*`를 단일 항목으로 포함합니다. *프로덕션 검색 서비스에는 권장되지 않지만* 개발 및 디버깅에 유용한 경우가 많습니다.

+ **maxAgeInSeconds** (선택 사항): 브라우저는이 값을 사용 하 여 CORS 실행 전 응답을 캐시할 기간 (초)을 결정 합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

## <a name="next-steps"></a>다음 단계

Cognitive Search에 대 한 거의 모든 샘플 또는 연습을 사용 하 여 인덱스를 만드는 실습 경험을 얻을 수 있습니다. 먼저 목차에서 퀵 스타트 중 하나를 선택할 수 있습니다.

그러나 데이터를 사용 하 여 인덱스를 로드 하는 방법에 대해서도 잘 알고 싶을 것입니다. 인덱스 정의와 데이터 가져오기 전략은 함께 정의 됩니다. 다음 문서에서는 인덱스를 로드 하는 방법에 대 한 자세한 정보를 제공 합니다.

+ [데이터 가져오기 개요](search-what-is-data-import.md)

+ [문서 추가, 업데이트 또는 삭제 (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 