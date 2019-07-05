---
title: 인덱스 정의 및 개념 만들기 - Azure Search
description: 구성 요소 파트 및 물리적 구조를 포함하여 Azure Search의 인덱스 용어 및 개념을 소개합니다.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a6a5b0e3957141b9ea17a378a7cbeff33a0124e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485194"
---
# <a name="create-a-basic-index-in-azure-search"></a>Azure Search에서 기본 인덱스 만들기

Azure Search에서 *인덱스*는 Azure Search 서비스의 필터링된 검색 및 전체 텍스트 검색에서 사용되는 *문서* 및 기타 구조의 지속형 저장소입니다. 개념상, 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 예를 들어 전자 상거래 소매점은 판매하는 각 항목에 대한 문서를 포함할 수 있으며 뉴스 조직은 각 기사에 대한 문서를 포함할 수 있습니다. 이러한 개념을 익숙한 데이터베이스와 동일하게 매핑: *인덱스*는 *테이블*과 개념적으로 유사하며 *문서*는 테이블의 *행*과 거의 비슷합니다.

인덱스를 추가하거나 업로드하면 Azure Search는 제공된 스키마를 기반으로 해서 물리적 구조를 만듭니다. 예를 들어 인덱스의 필드가 검색 가능으로 표시된 경우 해당 필드에 대해 반전된 인덱스가 생성됩니다. 나중에 문서를 추가 또는 업로드하거나 Azure Search에 검색 쿼리를 제출하는 경우 검색 서비스의 특정 인덱스에 요청을 전송하게 됩니다. 문서 값이 포함된 필드 로드를 ‘인덱싱’ 또는 데이터 수집이라고 합니다. 

포털, [REST API](search-create-index-rest-api.md) 또는 [.NET SDK](search-create-index-dotnet.md)에서 인덱스를 만들 수 있습니다.

## <a name="recommended-workflow"></a>권장 워크플로

올바른 인덱스 디자인은 일반적으로 수많은 반복을 거쳐 완성됩니다. 도구와 API를 조합하여 사용하면 설계를 보다 신속하게 완료할 수 있습니다.

1. [인덱서](search-indexer-overview.md#supported-data-sources)를 사용할 수 있는지 확인하세요. 외부 데이터가 지원되는 데이터 원본 중 하나인 경우 [**데이터 가져오기**](search-import-data-portal.md) 마법사를 사용하여 인덱스의 프로토타입을 제작하고 로드할 수 있습니다.

2. **데이터 가져오기**를 사용할 수 없는 경우 **인덱스 추가** 페이지의 컨트롤을 사용하여 [포털에서 초기 인덱스를 만들고](search-create-index-portal.md), 필드 및 데이터 형식을 추가하고, 특성을 할당할 수 있습니다. 포털은 여러 데이터 형식에 사용할 수 있는 특성을 보여줍니다. 인덱스 설계를 처음 접하는 분들에게 유용합니다.

   ![데이터 형식별 특성을 보여주는 인덱스 페이지 추가](media/search-create-index-portal/field-attributes.png "데이터 형식별 특성을 보여주는 인덱스 페이지 추가")
  
   **만들기**를 클릭하면 인덱스를 지원하는 모든 물리적 구조체가 검색 서비스 내에 생성됩니다.

3. [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index)와 [Postman](search-get-started-postman.md) 같은 웹 테스트 도구를 사용하여 인덱스 스키마를 다운로드할 수 있습니다. 이제 포털에서 만든 인덱스의 JSON 표현이 생겼습니다. 

   지금부터는 코드 기반 접근 방식으로 전환하겠습니다. 포털은 이미 만들어진 인덱스를 편집할 수 없기 때문에 반복에 적합하지 않습니다. 하지만 나머지 작업에는 Postman 및 REST를 사용할 수 있습니다.

4. [데이터가 포함된 인덱스를 로드합니다](search-what-is-data-import.md). Azure Search는 JSON 문서를 허용합니다. 데이터를 프로그래밍 방식으로 로드하려면 요청 페이로드의 JSON 문서에 Postman을 사용하면 됩니다. 데이터를 JSON으로 표현하기가 쉽지 않은 경우 이 단계에 가장 많은 노력이 필요할 것입니다.

5. 예상하는 결과가 나타나기 시작할 때까지 인덱스를 쿼리하고, 결과를 검사하고, 인덱스 스키마를 계속 반복합니다. [**Search 탐색기**](search-explorer.md) 또는 Postman을 사용하여 인덱스를 쿼리할 수 있습니다.

6. 계속해서 코드를 사용하여 설계를 반복하세요.  

물리적 구조 서비스에서 만들어지므로 [삭제 하 고 인덱스를 다시 만드는](search-howto-reindex.md) 변화가 기존 필드 정의를 변경할 때마다 반드시 합니다. 즉, 개발하는 동안 다시 작성이 자주 이루어질 것을 예상해야 합니다. 보다 빠르게 다시 작성할 수 있도록 데이터 하위 집합을 사용하는 방안을 고려해 볼 수 있습니다. 

반복 설계에는 포털 방식보다 코드를 권장합니다. 인덱스 정의에 포털을 사용하는 경우 다시 작성할 때마다 인덱스 정의를 채워야 합니다. 대안으로 사용 가능한 [Postman 및 REST API](search-get-started-postman.md) 같은 도구는 개발 프로젝트의 초기 단계에서 개념 증명 테스트에 도움이 됩니다. 요청 본문에서 인덱스 정의를 증분식으로 변경한 다음, 서비스에 요청을 보내서 업데이트된 스키마를 사용하여 인덱스를 다시 만들 수 있습니다.

## <a name="components-of-an-index"></a>인덱스의 구성 요소

아래 그림과 같이 Azure Search 인덱스는 다음 요소로 구성됩니다. 

일반적으로 [‘필드 컬렉션’](#fields-collection)이 인덱스의 가장 큰 파트이고, 각 필드에 이름과 유형이 지정되며 사용 방법을 결정하는 허용 가능한 동작으로 특성이 지정됩니다.  기타 요소에 포함 [suggesters](#suggesters)를 [점수 매기기 프로필](#scoring-profiles)합니다 [분석기](#analyzers) 사용자 지정을 지원 하도록 구성 요소를 사용 하 여 [CORS](#cors) 및 [암호화 키](#encryption-key) 옵션입니다.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
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
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>필드 컬렉션 및 필드 특성

스키마를 정의할 때 인덱스에서 각 필드의 이름, 형식 및 특성을 지정해야 합니다. 필드 형식은 해당 필드에 저장된 데이터를 분류합니다. 특성은 개별 필드에 설정되어 필드를 사용하는 방법을 지정합니다. 다음 표에서는 지정할 수 있는 형식 및 특성을 열거합니다.

### <a name="data-types"></a>데이터 형식
| Type | 설명 |
| --- | --- |
| *Edm.String* |전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 텍스트입니다(단어 분리, 형태소 분석 등). |
| *Collection(Edm.String)* |전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 문자열 목록입니다. 컬렉션에 있는 항목 수에 이론적인 상한은 없지만 페이로드 크기의 16MB 상한이 컬렉션에 적용됩니다. |
| *Edm.Boolean* |true/false 값을 포함합니다. |
| *Edm.Int32* |32비트 정수 값입니다. |
| *Edm.Int64* |64비트 정수 값입니다. |
| *Edm.Double* |배정밀도 숫자 데이터입니다. |
| *Edm.DateTimeOffset* |날짜 시간 값을 OData V4 형식으로 표현합니다(예: `yyyy-MM-ddTHH:mm:ss.fffZ` 또는 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |전 세계의 지리적 위치를 나타내는 지점입니다. |

Azure Search의 [지원되는 데이터 형식에 대한 자세한 내용은 여기서](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) 확인할 수 있습니다.

### <a name="index-attributes"></a>인덱스 특성

인덱스에 정확히 하나의 필드가으로 지정 되어야 합니다는 **키** 각 문서를 고유 하 게 식별 하는 필드입니다.

다른 특성 필드는 응용 프로그램 사용 되는 방식을 결정 합니다. 예를 들어 합니다 **검색 가능한** 특성은 전체 텍스트 검색에 포함 되어야 하는 모든 필드에 할당 됩니다. 

인덱스를 작성 하는 데 사용할 Api는 다양 한 기본 동작이 있습니다. 에 대 한는 [REST Api](https://docs.microsoft.com/rest/api/searchservice/Create-Index), 대부분의 특성은 기본적으로 활성화 됩니다 (예를 들어 **검색 가능한** 및 **검색할 수** 문자열 필드에 적용 됩니다)만 종종 경우 설정 해야 하 고 설정을 해제 해야 합니다. .NET SDK에 대 한 반대 마찬가지입니다. 명시적으로 설정 하지 않으면 모든 속성을 기본값인 특히 활성화 하지 않으면 해당 검색 동작을 사용 하지 않도록 설정 됩니다.

| 특성 | 설명 |
| --- | --- |
| `key` |문서 조회에 사용하는 각 문서의 고유 ID를 제공하는 문자열입니다. 모든 인덱스에는 하나의 키가 있어야 합니다. 필드 한 개만 키가 될 수 있으며, 이 필드 형식을 Edm.String으로 설정해야 합니다. |
| `retrievable` |검색 결과에서 필드를 반환할 수 있는지 여부를 지정합니다. |
| `filterable` |필드를 필터 쿼리에 사용하도록 허용합니다. |
| `Sortable` |쿼리에서 이 필드를 사용하여 검색 결과를 정렬할 수 있습니다. |
| `facetable` |사용자 자기 주도형 필터링을 위해 필드를 [패싯 탐색](search-faceted-navigation.md) 구조에 사용할 수 있습니다. 일반적으로 여러 문서(예를 들어 단일 브랜드 또는 서비스 범주에 속하는 여러 문서)를 그룹화하는 데 사용할 수 있는 반복되는 값을 포함한 필드는 패싯으로 가장 적합합니다. |
| `searchable` |전체 텍스트 검색 가능 필드로 표시합니다. |


## <a name="storage-implications"></a>스토리지 영향

선택하는 특성은 스토리지에 영향을 줍니다. 다음 스크린샷은 다양한 특성을 조합한 인덱스 스토리지 패턴을 보여줍니다.

인덱스를 기반으로 합니다 [기본 제공 부동산 샘플](search-get-started-portal.md) 인덱싱할 수 있는 데이터 원본 및 포털에서 쿼리 합니다. 인덱스 스키마는 표시되지 않지만, 인덱스 이름을 보고 특성을 유추할 수 있습니다. 예를 들어 *realestate-searchable* 인덱스에서는 **searchable** 특성만 선택되었고 그 외에는 아무 것도 선택되지 않았으며, *realestate-retrievable* 인덱스에서는 **retrievable** 특성만 선택되었고 그 외에는 아무 것도 선택되지 않았습니다.

![특성 선택에 따른 인덱스 크기](./media/search-what-is-an-index/realestate-index-size.png "특성 선택에 따른 인덱스 크기")

이러한 인덱스 변형은 인위적이지만, 특성이 스토리지에 미치는 영향을 광범위하게 비교하는 데 참조할 수 있습니다. **retrievable**을 설정하면 인덱스 크기가 커지나요? 아니요. **Suggester**에 필드를 추가하면 인덱스 크기가 커지나요? 예.

필터 및 정렬을 지원하는 인덱스는 전체 텍스트 검색만 지원하는 인덱스보다 비례적으로 큽니다. 필터 및 정렬은 정확히 일치하는 항목을 쿼리하므로 문서가 그대로 저장됩니다. 반면 전체 텍스트 및 유사 항목 검색을 지원하는 검색 가능 필드는 반전 인덱스를 사용하며, 반전 인덱스는 전체 문서보다 공간을 적게 사용하는 토큰화된 용어로 채워집니다.

> [!Note]
> 스토리지 아키텍처는 Azure Search의 구현 세부 정보로 간주되며 예고 없이 변경될 수 있습니다. 현재 동작이 나중에도 유지된다는 보장은 없습니다.

## <a name="suggesters"></a>확인기
제안기는 검색에서 자동 완성 또는 자동 완성 쿼리를 지원하는 데 사용되는 인덱스의 필드를 정의하는 스키마 섹션입니다. 일반적으로 사용자가 검색 쿼리를 입력하는 동안 부분 검색 문자열이 [제안(REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)으로 전송되고, API는 제안 문구 세트를 반환합니다. 

제안기에 추가된 필드는 검색어 미리 입력 기능을 빌드하는 데 사용됩니다. 모든 검색어는 인덱싱 중에 생성되어 별도로 저장됩니다. 제안기 구조체를 만드는 방법에 대한 자세한 내용은 [제안기 추가](index-add-suggesters.md)를 참조하세요.

## <a name="scoring-profiles"></a>점수 매기기 프로필

[점수 매기기 프로필](index-add-scoring-profiles.md)은 검색 결과에서 더 위쪽에 표시할 항목을 제어할 수 있는 사용자 지정 채점 동작을 정의하는 스키마 섹션입니다. 점수 매기기 프로필은 필드 가중치와 함수로 구성됩니다. 사용하려면 쿼리 문자열에서 이름별로 프로필을 지정합니다.

기본 점수 매기기 프로필은 결과 집합에 있는 모든 항목에 대한 검색 점수를 계산하기 위해 백그라운드에서 작동합니다. 이름이 지정되지 않은 내부 점수 매기기 프로필을 사용할 수 있습니다. 또는 사용자 지정 프로필이 쿼리 문자열에 지정되지 않은 경우 항상 호출되는 기본값으로 사용자 지정 프로필을 사용하도록 **defaultScoringProfile**을 설정합니다.

## <a name="analyzers"></a>분석기

분석기 요소는 필드에 사용할 언어 분석기의 이름을 설정합니다. 분석기를 사용할 수 있는 범위에 대한 자세한 내용은 [Azure Search 인덱스에 분석기 추가](search-analyzers.md)를 참조하세요. 분석기는 검색 가능한 필드에만 사용할 수 있습니다. 분석기가 필드에 할당되면 인덱스를 다시 작성하기 전에는 분석기를 변경할 수 없습니다.

## <a name="cors"></a>CORS

브라우저에서는 모든 원본 간 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 원본 간 쿼리를 허용하려면 **corsOptions** 특성을 설정하여 CORS(원본 간 리소스 공유)를 사용하도록 설정합니다. 보안상의 이유로 쿼리 API에서만 CORS가 지원됩니다. 

CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

+ **allowedOrigins**(필수): 인덱스에 대한 액세스 권한을 부여할 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://<fully-qualified-domain-name>:<port>` 형식이지만 `<port>`는 대개 생략됩니다. 자세한 내용은 [원본 간 리소스 공유(위키백과)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 참조하세요.

  모든 원본에 대한 액세스를 허용하려면 **allowedOrigins** 배열에서 `*`를 단일 항목으로 포함합니다. 프로덕션 검색 서비스에는 권장되지 않지만 개발 및 디버깅에 유용한 경우가 많습니다. 

+ **maxAgeInSeconds**(선택 사항): 브라우저는 이 값을 사용하여 CORS 실행 전 응답을 캐시할 기간(초)을 결정합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

## <a name="encryption-key"></a>암호화 키

Microsoft 관리 키를 사용 하 여 기본적으로 모든 Azure search 인덱스를 암호화 하는 동안 인덱스를 사용 하 여 암호화를 구성할 수 있습니다 **고객 관리 키** 키 자격 증명 모음에 있습니다. 자세한 내용은 참조 하세요 [Azure Search에서 암호화 키를 관리](search-security-manage-encryption-keys.md)합니다.

## <a name="next-steps"></a>다음 단계

인덱스 작성을 파악했으면 포털에서 계속해서 첫 번째 인덱스를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [인덱스 추가(포털)](search-create-index-portal.md)
