<properties 
	pageTitle="Azure 검색 서비스 REST API: 2014-10-20-Preview" 
	description="Azure 검색 서비스 REST API: 2014-10-20-Preview" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="06/24/2015" 
	ms.author="heidist"/>

# Azure 검색 서비스 REST API 버전 2014-10-20-Preview #

이 문서에서는 **2014-10-20-Preview** 버전의 Azure 검색 서비스 REST API를 설명합니다. 이 버전은 이제 사용되지 않습니다. 프로덕션 코드에서 이 API를 사용하고 있는 경우 일반적으로 사용 가능한 버전으로 즉시 마이그레이션합니다. 지침은 [Azure 검색의 미리보기 API 전환](search-transition-from-preview.md)을 참조하세요.

이 버전과 관련된 다른 API 콘텐츠는 다음과 같습니다.

- [점수 매기기 프로필(Azure 검색 서비스 REST API: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md)

Azure 검색 REST API의 릴리스 버전에 대한 설명서는 MSDN에서 확인할 수 있습니다. 자세한 내용은 [Azure 검색 서비스 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)를 참조하세요.

##서비스 REST API 정보##

Azure 검색은 사용자 지정 검색 응용 프로그램을 빌드하는 데 사용할 수 있는 클라우드 기반 서비스입니다. Azure 검색에서는 *검색 서비스*와 *인덱스*의 개념이 사용됩니다. 검색 서비스는 인덱스를 하나 이상 포함합니다. 검색 서비스는 정규화된 도메인 이름(예: `mysearchservice.search.windows.net`)을 통해 고유하게 식별됩니다. 서비스를 프로비전하면 생성되는 api-key를 사용하여 Azure 검색 서비스에 대한 요청을 인증합니다.

Azure 검색 서비스에 대해 실행할 수 있는 작업에는 다음 두 가지 유형이 있습니다.

- **인덱스 관리**: 여기에는 검색 서비스 또는 검색 인덱스에 대해 실행되는 관리 작업이 포함됩니다. 

- **문서 작업**: 지정된 인덱스 모음을 쿼리하고 관리합니다.

이 섹션에서 설명하는 API를 사용하면 인덱스 작성/채우기, 문서 업로드, 쿼리 등의 검색 데이터에 대한 작업을 수행할 수 있습니다. API를 호출할 때는 다음 사항을 고려하세요.

- 모든 API는 OData JSON 형식의 HTTP 요청 및 응답으로 정의됩니다.

- 모든 API 사용 시에는 아래 참고에 설명된 대로 쿼리 문자열이나 헤더에 `api-key`를 포함해야 합니다.

- 모든 API는 기본 포트 443에서 HTTPS를 통해 실행해야 합니다.

- 모든 API 요청은 `api-version` 쿼리 문자열 매개 변수를 포함해야 합니다. 해당 값은 다음 예제와 같이 현재 서비스 릴리스 버전으로 설정해야 합니다.

    GET /indexes?api-version=2014-10-20-Preview

- 모든 API 요청에서는 필요에 따라 `Accept` HTTP 헤더를 설정할 수 있습니다. 이 헤더를 설정하지 않으면 기본적으로 `application/json`이 사용됩니다.

서비스 관리용으로 별도의 API가 제공됩니다. 서비스 관리 작업의 예로는 서비스 프로비전, 용량 변경 등이 있습니다. 이 API에 대한 자세한 내용은 Azure 검색 관리 REST API를 참조하세요.

### 끝점 ###

서비스 작업의 끝점은 프로비전한 Azure 검색 서비스의 URL인 https://*your-service-name*.search.windows.net입니다.


### 버전 ###

Azure 검색 API에는 여러 버전이 있습니다. 프로덕션 응용 프로그램에서 사용할 Azure 검색을 평가하는 경우 `api-version=2014-07-31-Preview`를 사용하는 것이 좋습니다. 현재 잠긴 버전은 이 버전뿐입니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.


<a name="Authentication"></a>
### 인증 및 액세스 제어###

Azure 검색 서비스에 대한 인증은 검색 서비스 URL와 `api-key`의 두 가지 정보가 필요합니다. `api-keys`는 서비스가 만들어질 때 생성되며, 서비스가 프로비전된 후 요청 시 다시 생성할 수 있습니다. `api-key`는 모든 작업에 대한 액세스 권한을 부여하는 관리 키 또는 쿼리 요청만 인증하는 쿼리 키입니다. 서비스당 최대 2개의 관리 키와 50개의 쿼리 키를 사용할 수 있습니다.

액세스 제어는 Azure Preview 포털에서 제공되는 RBAC(역할 기반 액세스 제어)를 통한 서비스 관리로 제한됩니다. 역할을 사용하여 서비스 관리를 위한 액세스 수준을 설정합니다. 예를 들어 관리 키는 소유자 및 참가자 역할만 볼 수 있는 반면 서비스 상태는 역할에 관계없이 모든 구성원이 볼 수 있습니다.

인덱스 관리, 인덱스 채우기 및 쿼리를 비롯하여 검색 서비스 끝점에 대해 수행되는 데이터 작업에 액세스할 때는 `api-keys`만 사용해야 합니다. 인덱스 또는 문서 관련 작업에는 RBAC가 적용되지 않습니다. Azure 검색의 `api-keys` 또는 RBAC에 대한 자세한 내용은 [Microsoft Azure에서 검색 서비스 관리](search-manage.md)를 참조하세요.

**참고**: `api-key` 등의 중요한 데이터를 요청 URI에서 전달하는 것은 보안상 좋지 않습니다. 따라서 Azure 검색에서는 쿼리 문자열의 `api-key`로 쿼리 키만 허용하며, 인덱스 내용을 공개해야 하는 경우가 아니면 중요한 데이터를 요청 URI에서 전달해서는 안 됩니다. 대신 `api-key`를 요청 헤더로 전달하는 것이 좋습니다.

###API 요약###

Azure 검색 서비스 API는 엔터티 조회에 대한 두 가지 구문, 즉 간단한 OData 구문과 대체 OData 구문을 지원합니다(자세한 내용은 [OData 지원(Azure 검색 API)](http://msdn.microsoft.com/library/azure/dn798932.aspx)을 참조하세요. 다음 목록에서는 간단한 구문을 보여 줍니다.

[인덱스 만들기](#CreateIndex)

    POST /indexes?api-version=2014-10-20-Preview

[인덱스 업데이트](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-10-20-Preview

[인덱스 가져오기](#GetIndex)

    GET /indexes/[index name]?api-version=2014-10-20-Preview

[인덱스 나열](#ListIndexes)

    GET /indexes?api-version=2014-10-20-Preview

[인덱스 통계 가져오기](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-10-20-Preview

[인덱스 삭제](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-10-20-Preview

[인덱스 내에서 데이터 추가, 삭제 및 업데이트](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-10-20-Preview 

[문서 검색](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[문서 조회](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[문서 수 계산](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-10-20-Preview

[제안](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________
<a name="IndexOps"></a>
## 인덱스 작업

Azure 검색 서비스에서 간단한 HTTP 요청(POST, GET, PUT, DELETE)을 통해 지정된 인덱스 리소스에 대해 인덱스를 만들고 관리할 수 있습니다. 인덱스를 만들려면 먼저 인덱스 스키마를 설명하는 JSON 문서에 대해 POST를 수행합니다. 스키마는 인덱스의 필드, 필드의 데이터 형식 및 필드 사용 방법(예: 전체 텍스트 검색, 필터, 정렬, 패싯 또는 제안)을 정의합니다. 또한 인덱스의 동작을 구성하는 기타 특성과 점수 매기기 프로필 및 확인기도 정의합니다.

다음 예제에서는 Description 필드가 두 언어로 정의된 호텔 정보를 검색하는 데 사용되는 스키마를 보여 줍니다. 특성이 필드 사용 방식을 제어하는 방법을 살펴보세요. 예를 들어 `hotelId`는 문서 키(`"key": true`)로 사용되며 전체 텍스트 검색에서 제외됩니다(`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer: "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ] 
    }
 
인덱스를 만든 후에는 인덱스를 채울 문서를 업로드합니다. 이 작업을 수행하는 다음 단계는 [문서 추가 또는 업데이트](#AddOrUpdateDocuments)를 참조하세요.

Azure 검색의 인덱싱을 소개하는 비디오는 [Azure 검색에 대한 Channel 9의 Cloud Cover 에피소드](http://go.microsoft.com/fwlink/p/?LinkId=511509)를 참조하세요.


<a name="CreateIndex"></a>
## 인덱스 만들기 ##

HTTP POST 요청을 사용하여 Azure 검색 서비스 내에서 새 인덱스를 만들 수 있습니다. 요청 본문은 인덱스 이름, 필드, 쿼리 동작을 제어하는 특성, 결과 점수 매기기, 확인기 및 CORS 옵션을 지정하는 JSON 문서입니다.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 인덱스 이름을 지정할 수 있습니다. 인덱스가 없으면 인덱스가 만들어집니다.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**참고**: 허용되는 최대 인덱스 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱스를 3개까지 사용할 수 있으며 표준 서비스에서는 검색 서비스당 인덱스 50개를 만들 수 있습니다. 자세한 내용은 [한도 및 제약 조건](http://msdn.microsoft.com/library/azure/dn798934.aspx)을 참조하세요.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. POST 또는 PUT 메서드를 사용하여 **인덱스 만들기** 요청을 생성할 수 있습니다. POST를 사용할 때는 인덱스 스키마 정의와 함께 요청 본문에 인덱스 이름을 제공해야 합니다. PUT을 사용하는 경우 인덱스 이름이 URL에 포함됩니다. 인덱스가 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

인덱스 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 인덱스 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.

`api-version`은 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. API 버전에 대한 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요. 언어 분석기에 대한 자세한 내용은 [언어 지원](#LanguageSupport)을 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 
- 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 만들기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다. 
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

<a name="RequestData"></a> **요청 본문 구문**

요청 본문에는 이 인덱스에 공급할 문서 내의 데이터 필드 목록, 데이터 형식, 특성 및 쿼리 시 일치하는 문서의 점수를 매기는 데 사용되는 선택적 점수 매기기 프로필 목록이 들어 있는 스키마 정의가 포함됩니다.

POST 요청의 경우에는 요청 본문에 인덱스 이름을 지정해야 합니다.

인덱스에는 key 필드를 하나만 포함할 수 있습니다. 이 필드는 문자열 필드여야 하며, 인덱스와 함께 저장되는 각 문서의 고유 식별자를 나타냅니다.

요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

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
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

참고: `Edm.Int64` 데이터 형식은 API 버전 2014-10-20-Preview부터 지원됩니다.
    
**인덱스 특성**

인덱스를 만들 때 설정할 수 있는 특성은 다음과 같습니다. 점수 매기기 및 점수 매기기 프로필에 대한 자세한 내용은 [점수 매기기 프로필(Azure 검색 서비스 REST API: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md)을 참조하세요.

`name` - 필드의 이름을 설정합니다.

`type` - 필드의 데이터 형식을 설정합니다. 지원되는 형식 목록은 [지원되는 데이터 형식](#DataTypes)을 참조하세요.

`searchable` - 필드를 전체 텍스트 검색 가능 필드로 표시합니다. 이렇게 표시된 필드의 경우 인덱싱 중에 단어 분리 등의 분석이 수행됩니다. `searchable` 필드를 "sunny day" 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 "sunny"와 "day"로 분할됩니다. 따라서 이러한 용어에 대한 전체 텍스트 검색을 수행할 수 있습니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필드는 기본적으로 `searchable`로 설정됩니다. 다른 형식의 필드는 `searchable`로 설정할 수 없습니다.

  - **참고**: Azure 검색에서는 전체 텍스트 검색을 위한 필드 값의 토큰화된 버전을 추가로 저장하므로 `searchable` 필드는 인덱스에서 추가적인 공간을 사용합니다. 검색에 필드를 포함할 필요가 없으며 인덱스의 공간을 저장하려는 경우에는 `searchable`을 `false`로 설정합니다.

`filterable` - `$filter` 쿼리에서 필드를 참조할 수 있습니다. `filterable`은 문자열 처리 방법에서 `searchable`과 차이가 있습니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`이며 `filterable`인 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 `f`를 "sunny day"로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. 기본적으로 모든 필드는 `filterable`로 설정됩니다.

`sortable` - 시스템에서는 기본적으로 점수를 기준으로 결과를 정렬하지만 사용자는 대부분의 경우 문서의 필드를 기준으로 결과를 정렬합니다. 형식이 `Collection(Edm.String)`인 필드는 `sortable`로 설정할 수 없습니다. 기타 모든 필드는 기본적으로 `sortable`로 설정됩니다.

`facetable` - 일반적으로 범주별 적중 횟수를 포함하는 검색 결과 표시에 사용됩니다. 디지털 카메라를 검색한 다음 브랜드, 메가픽셀, 가격 등을 기준으로 적중 항목을 표시하는 경우를 예로 들 수 있습니다. 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. 기타 모든 필드는 기본적으로 `facetable`로 설정됩니다.

  - **참고**: 형식이 `Edm.String`이며 `filterable`, `sortable` 또는 `facetable`로 설정된 필드의 최대 길이는 32KB입니다. 이러한 필드는 단일 검색 용어로 처리되는데 Azure 검색에서 용어의 최대 길이는 32KB이기 때문입니다. 단일 문자열 필드에 이보다 많은 텍스트를 저장해야 하는 경우에는 인덱스 정의에서 `filterable`, `sortable` 및 `facetable`을 명시적으로 `false`로 설정해야 합니다.

`suggestions` - 필드를 형식에 대한 사전 자동 완성에 사용할 수 있는지 여부를 설정합니다. `Edm.String` 또는 `Collection(Edm.String)` 형식의 필드에 대해 설정할 수만 있습니다. `suggestions`은 인덱스에 추가 공간이 필요하므로 기본적으로 `false`로 설정됩니다. **참고**: 이 옵션 대신 2014-10-20-Preview 버전에 도입된 `suggesters` 속성을 제안에 사용하는 것이 좋습니다. 이후 버전에서는 `suggestions` 속성이 더 이상 사용되지 않으며, 대신 별도의 `suggesters` 사양이 사용됩니다.

  - **참고**: 위의 모든 특성(`searchable`, `filterable`, `sortable`, `facetable`, `suggestions`)이 `true`로 설정되어 있지 않은 필드는 반전된 인덱스에서 제외됩니다. 쿼리에서 사용되지는 않지만 검색 결과에는 필요한 필드의 경우 이 옵션을 사용하면 유용합니다. 이러한 필드를 인덱스에서 제외하면 성능이 향상됩니다.

`key` - 필드가 인덱스 내의 문서에 대한 고유 식별자를 포함하는 것으로 표시합니다. 정확히 하나의 필드를 `key` 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다. 키 필드를 사용하면 [문서 조회](#LookupAPI)를 통해 문서를 직접 조회할 수 있습니다.

`retrievable` - 검색 결과에서 필드를 반환할 수 있는지 여부를 설정합니다. 이익 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `key` 필드의 경우 이 특성을 `true`로 설정해야 합니다.

`scoringProfiles` - 검색 결과에서 더 위쪽에 표시할 항목을 제어할 수 있는 사용자 지정 점수 매기기 동작을 정의합니다. 점수 매기기 프로필은 가중치가 적용된 필드와 함수로 구성됩니다. 점수 매기기 프로필에서 사용되는 특성에 대한 자세한 내용은 [점수 매기기 프로필(Azure 검색 서비스 REST API: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md)을 참조하세요.

`analyzer` - 필드에 사용할 텍스트 분석기의 이름을 설정합니다. 허용되는 값 집합은 [언어 지원](#LanguageSupport)을 참조하세요. 이 옵션은 `searchable` 필드에만 사용할 수 있습니다. 필드에 대해 분석기를 선택한 후에는 변경할 수 없습니다.


<a name="LanguageSupport"></a> **언어 지원**

검색 가능 필드에서는 대개 단어 분리, 텍스트 정규화, 용어 필터링 등을 포함하는 분석이 수행됩니다. 기본적으로 Azure 검색에서는 [Apache Lucene 표준 분석기](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)를 사용하여 검색 가능 필드를 분석합니다. 이 분석기는 ["유니코드 텍스트 구분"](http://unicode.org/reports/tr29/) 규칙에 따라 텍스트를 요소로 분리합니다. 또한 표준 분석기에서는 모든 문자를 소문자 형식으로 변환합니다. 인덱싱 및 쿼리 처리 중에는 인덱싱된 문서와 검색 용어 둘 다에 대해 분석을 수행합니다.

Azure 검색에서는 다양한 언어로 필드를 인덱싱할 수 있습니다. 이러한 각 언어를 사용하려면 지정된 언어의 특성을 고려할 수 있는 비표준 텍스트 분석기가 필요합니다. 예를 들어 프랑스어 분석기에서는 [Light French Stemmer](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html)를 적용하여 단어를 [단어 형태소](http://en.wikipedia.org/wiki/Stemming)로 줄입니다. 또한 분석된 텍스트에 [발음 생략](http://en.wikipedia.org/wiki/Elision) 및 프랑스어 중지 단어를 제거합니다. 영어 분석기를 사용하면 표준 분석기의 기능을 확장할 수 있습니다. 이 분석기는 단어에서 소유격(후행 's)을 제거하고, [Porter 형태소 분석 알고리즘](http://tartarus.org/~martin/PorterStemmer/)에 따라 형태소 분석을 적용하며, 영어의 [중지 단어](http://en.wikipedia.org/wiki/Stop_words)를 제거합니다.
 
`analyzer` 속성을 설정하면 인덱스 정의에서 각 필드에 대해 독립적으로 분석기를 구성할 수 있습니다. 예를 들어 영어, 프랑스어, 스페인어 호텔 설명을 표시하는 개별 필드를 같은 인덱스에서 나란히 표시할 수 있습니다. 쿼리가 검색 쿼리에서 반환할 언어별 필드를 지정합니다.

아래 목록에는 지원되는 분석기와 해당 기능에 대한 간단한 설명이 나와 있습니다.

<table style="font-size:12">
    <tr>
		<th>언어</th>
		<th>분석기 이름</th>
		<th>설명</th>
	</tr>
    <tr>
		<td>아랍어</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>아랍어 철자 정규화를 구현합니다.</li>
			<li>간단한 알고리즘 방식 형태소 분석을 적용합니다.</li>
			<li>아랍어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>브라질어</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>브라질어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>중국어 간체</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>개연적 지식 모델을 사용하여 최적의 단어 구분을 찾습니다.</li>
			<li>중국어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>중국어 번체</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>인접하는 두 중국어 문자의 겹치는 그룹인 바이그램을 인덱싱합니다.</li>
			<li>문자 너비 차이를 정규화합니다.</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>체코어</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>체코어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>덴마크어</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>덴마크어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>네덜란드어</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>네덜란드어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>독일어</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>독일어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>그리스어</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>그리스어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>영어</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>영어 중지 단어를 필터링합니다.</li>
			<li>소유격을 제거합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>핀란드어</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>핀란드어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>프랑스어</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>프랑스어 중지 단어를 필터링합니다.</li>
			<li>발음 생략을 제거합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>힌디어</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>힌디어 중지 단어를 필터링합니다.</li>
			<li>일부 철자 변형 차이를 제거합니다.</li>
			<li>인도어 텍스트의 유니코드 표현을 정규화합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>헝가리어</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>헝가리어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>인도네시아어(공용어)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>인도네시아어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>이탈리아어</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>이탈리아어 중지 단어를 필터링합니다.</li>
			<li>발음 생략을 제거합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>일본어</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>형태학적 분석을 사용합니다.</li>
			<li>일반적인 가타카나 철자 변형을 정규화합니다.</li>
			<li>간단한 중지 단어/중지 태그 제거를 수행합니다.</li>
			<li>문자 너비를 정규화합니다.</li>
			<li>분류 정리를 통해 어형이 변화되는 형용사와 동사를 기본 형태로 축약합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>한국어</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>인접하는 두 중국어 문자의 겹치는 그룹인 바이그램을 인덱싱합니다.</li>
			<li>문자 너비 차이를 정규화합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>라트비아어</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>라트비아어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>노르웨이어</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>노르웨이어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>폴란드어</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>알고리즘 방식 형태소 분석(Stempel)을 적용합니다.</li>
			<li>폴란드어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>포르투갈어</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>포르투갈어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>루마니아어</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>루마니아어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>러시아어</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>러시아어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>스페인어</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>스페인어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>스웨덴어</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>스웨덴어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>터키어</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>아포스트로피와 그 뒤의 모든 문자를 제거합니다.</li>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>터키어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>태국어</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>간단한 형태소 분석을 적용합니다.</li>
			<li>태국어 중지 단어를 필터링합니다.</li>
		</ul>
		</td>
	</tr>
	<td colspan="3">Azure 검색에서는 추가적으로 언어에 관계없는 분석기 구성을 제공합니다.</td> 
    <tr>
		<td>표준 ASCII 접기</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>유니코드 텍스트 구분(표준 토크나이저)입니다.</li>
			<li>ASCII 접기 필터 - 처음 127개 ASCII 문자 집합에 속하지 않는 유니코드 문자를 해당 ASCII 문자로 변환합니다. 분음 부호(악센트)를 제거하는 데 유용합니다.</li>
		</ul>
		</td>
	</tr>
</table>

이름에 <i>lucene</i> 주석이 포함된 모든 분석기는 [Apache Lucene 언어 분석기](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)를 통해 구동됩니다. ASCII 접기 필터에 대한 자세한 내용은 [여기](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)에서 확인할 수 있습니다.

**CORS 옵션**

브라우저에서는 모든 교차 원본 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 교차 원본 쿼리를 허용하려면 `corsOptions` 특성을 설정하여 CORS(교차 원본 자원 공유)를 사용하도록 설정합니다. 보안상 쿼리 API에서만 CORS가 지원됩니다. CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

- `allowedOrigins`(필수): 인덱스에 대한 액세스 권한을 부여할 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://fully-qualified-domain-name:port` 형식이지만 포트는 대개 생략됩니다. 자세한 내용은 [이 문서](http://go.microsoft.com/fwlink/?LinkId=330822)를 참조하세요.
 - 모든 원본에 대한 액세스를 허용하려면 `allowedOrigins` 배열에서 `*`를 단일 항목으로 포함합니다. **프로덕션 검색 서비스에서는 이러한 방식을 사용하지 않는 것이 좋습니다**. 개발 또는 디버깅용으로는 이 기능이 유용할 수 있습니다.
- `maxAgeInSeconds`(선택): 브라우저는 이 값을 사용하여 CORS 실행 전 응답을 캐시할 기간(초)을 결정합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

<a name="Suggesters"></a> **확인기**

suggester를 사용하면 검색을 자동 완성할 수 있습니다. 일반적으로는 사용자가 입력하는 동안 부분 검색 문자열이 제안 API로 전송되며 API는 제안 구 집합을 반환합니다.

Azure 검색에서 새 suggesters API로 전환하고 있습니다. 버전 2014-07-31-Preview에는 필드를 `"suggestions": true`로 표시한 다음 짧은 문자열(3~25자)에 대한 접두사 제안을 수행할 수 있는 좁은 범위의 제안 API가 있습니다. 버전 2014-10-20-Preview부터 Azure 검색에서는 이 섹션에 설명된 대로 "suggesters"를 기반으로 보다 강력한 버전의 제안을 제공합니다. 이 새 구현은 접두사 및 중위 일치를 수행할 수 있으며, 검색 문자열 오류에 대한 더 나은 허용 오차를 제공합니다. 버전 2014-10-20-Preview부터 새 suggesters API를 사용하는 것이 좋습니다.

현재 확인기 지원은 광범위한 용어/구보다는 특정 문서를 제안하는 데 사용하는 경우 가장 효율적입니다. 확인기를 사용하기에 적합한 원본 필드는 제목, 이름 및 항목을 식별할 수 있는 비교적 짧은 기타 구입니다. 반면 범주/태그 등의 반복 필드나 설명/주석 등의 매우 긴 필드는 확인기를 사용하기에 효과적이지 않습니다.

인덱스 정의의 일부로 단일 suggester를 `suggesters` 컬렉션에 추가할 수 있습니다. suggester를 정의하는 속성은 다음과 같습니다.

- `name`: suggester의 이름입니다. `suggest` API를 호출할 때 suggester의 이름을 사용합니다.
- `searchMode`: 제안 가능한 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.
- `sourceFields`: 제안 내용의 원본인 필드 하나 이상이 포함된 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 현재 미리 보기의 suggesters에서는 사용자 지정 분석기가 설정되지 않은 필드만 사용할 수 있습니다.

현재 API 버전에서는 suggesters 컬렉션에 suggester를 하나만 포함할 수 있습니다.

<a name="CreateUpdateIndexExample"></a> **요청 본문 예제**
 
    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ] 
    }

**응답**

요청이 성공적으로 실행되면 “201 생성됨”이 반환됩니다.

기본적으로 응답 본문에는 생성된 인덱스 정의에 대 한 JSON 포함됩니다. 그러나 `Prefer` 요청 헤더를 `return=minimal`로 설정하면 응답 본문은 비어 있게 되며 성공 상태 코드는 "201 생성됨"이 아닌 "204 콘텐츠 없음"으로 표시됩니다. 인덱스를 만드는 데 사용한 방법(PUT 또는 POST)에 상관없이 결과는 동일합니다.

<a name="UpdateIndex"></a>
## 인덱스 업데이트 ##

HTTP PUT 요청을 사용하여 Azure 검색 내에서 기존 인덱스를 업데이트할 수 있습니다. 공개 미리 보기에서 수행 가능한 업데이트 작업으로는 기존 스키마에 새 필드 추가, CORS 옵션 수정, 점수 매기기 프로필 수정 등이 있습니다([점수 매기기 프로필(Azure 검색 서비스 REST API: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md) 참조). 요청 URI에서 업데이트할 인덱스의 이름을 지정합니다.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**중요:** 인덱스 스키마 업데이트에 대한 지원은 검색 인덱스를 다시 작성하지 않아도 되는 작업으로 제한됩니다. 필드 형식을 변경하는 등 인덱싱을 다시 수행해야 하는 모든 스키마 업데이트는 현재 지원되지 않습니다. 기존 필드를 변경하거나 삭제할 수는 없지만 언제든지 새 필드를 추가할 수는 있습니다. suggesters도 마찬가지입니다. 필드를 추가할 때 suggester에 새 필드를 추가할 수 있지만 suggesters에서 필드를 제거하거나 suggesters에 기존 필드를 추가할 수는 없습니다.

새 필드를 인덱스에 추가하면 인덱스의 모든 기존 문서에서 해당 필드에 null 값이 자동으로 포함됩니다. 새 문서를 인덱스에 추가할 때까지는 저장소 공간이 추가로 사용되지 않습니다.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 업데이트** 요청은 HTTP PUT을 사용하여 생성됩니다. PUT을 사용하는 경우 인덱스 이름이 URL에 포함됩니다. 인덱스가 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

인덱스 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 인덱스 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 업데이트** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문 구문**

기존 인덱스를 업데이트할 때 본문은 원래 스키마 정의와 새로 추가하는 필드뿐만 아니라 수정된 점수 매기기 프로필, suggesters 및 CORS 옵션(있는 경우)도 포함해야 합니다. 점수 매기기 프로필과 CORS 옵션을 수정하지 않는 경우에는 인덱스를 만들 때의 원래 값을 포함해야 합니다. 일반적으로 업데이트에 사용할 수 있는 가장 효율적인 패턴은 GET을 사용하여 인덱스 정의를 검색하여 수정한 다음 PUT을 사용하여 정의를 업데이트하는 것입니다.

여기서는 편의상 인덱스를 만드는 데 사용되는 스키마 구문을 재현합니다. 자세한 내용은 [인덱스 만들기](#CreateIndex)를 참조하세요.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

참고: `Edm.Int64` 데이터 형식은 API 버전 2014-10-20-Preview부터 지원됩니다.

**응답**

요청이 성공적으로 실행되면 “204 콘텐츠 없음”이 반환됩니다.

기본적으로 응답 본문은 비어 있습니다. 그러나 `Prefer` 요청 헤더가 `return=representation`으로 설정되어 있으면 응답 본문에는 업데이트된 인덱스 정의의 JSON이 포함됩니다. 이 경우의 성공 상태 코드는 "200 OK"입니다.

<a name="ListIndexes"></a>
## 인덱스 나열 ##

**인덱스 나열** 작업은 현재 Azure 검색 서비스에 있는 인덱스의 목록을 반환합니다.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 나열** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.
 
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 나열** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 인덱스 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /indexes?api-version=2014-10-20-Preview&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

이 기술은 검색 서비스에 많은 인덱스가 있는 경우 대역폭을 절감하는 데 유용합니다.

<a name="GetIndex"></a>
## 인덱스 가져오기 ##

**인덱스 가져오기** 작업은 Azure 검색에서 인덱스 정의를 가져옵니다.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 가져오기** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.
 
요청 URI의 [index name]은 인덱스 컬렉션에서 반환할 인덱스를 지정합니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.
 
- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 가져오기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 페이로드의 예제는 [인덱스 만들기 및 업데이트](#CreateUpdateIndexExample)에서 예제 JSON을 참조하세요.

<a name="DeleteIndex"></a>
## 인덱스 삭제 ##

**인덱스 삭제** 작업은 Azure 검색 서비스에서 인덱스 및 관련 문서를 제거합니다. Azure Preview 포털의 서비스 대시보드나 API에서 인덱스 이름을 가져올 수 있습니다. 자세한 내용은 [인덱스 나열](#ListIndexes)을 참조하세요.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]
    
**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 삭제** 요청은 DELETE 메서드를 사용하여 생성할 수 있습니다.
 
요청 URI의 [index name]은 인덱스 컬렉션에서 삭제할 인덱스를 지정합니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.
 
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **인덱스 삭제** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [Azure 검색 시작](search-get-started.md)을 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="GetIndexStats"></a>
## 인덱스 통계 가져오기 ##

**인덱스 통계 가져오기** 작업은 현재 인덱스의 문서 수와 저장소 사용량을 Azure 검색에서 반환합니다.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 통계 가져오기** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 지정한 인덱스의 인덱스 통계를 반환하도록 서비스에 지시합니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.
 
- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 통계 가져오기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문의 형식은 다음과 같습니다.

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## 문서 작업

Azure 검색에서는 서비스에 업로드하는 JSON 문서를 사용하여 인덱스를 채웁니다. 업로드하는 모든 문서는 검색 데이터 모음으로 구성됩니다. 문서에는 필드가 포함되며, 이러한 필드 중 일부는 문서 업로드 시 검색 용어로 토큰화됩니다. Azure 검색 API의 `/docs` URL 세그먼트는 인덱스의 문서 컬렉션을 나타냅니다. 문서 업로드/병합/삭제/쿼리 등 컬렉션에 대해 수행하는 모든 작업은 단일 인덱스의 컨텍스트에서 수행되므로 이러한 작업의 URL은 항상 지정된 인덱스 이름에 대해 `/indexes/[index name]/docs`로 시작됩니다.

응용 프로그램 코드는 관계형 데이터베이스 또는 기타 구조화된 데이터 원본의 결과 집합을 사용하여 Azure 검색에 업로드할 JSON 문서를 생성할 수 있습니다. CodePlex의 Azure 검색 Adventure Works 데모 샘플 응용 프로그램에는 Adventure Works 샘플 데이터베이스의 결과 집합을 사용하여 JSON 문서를 작성하는 코드가 포함되어있습니다. 샘플 응용 프로그램에 대한 자세한 내용은 [여기](search-create-first-solution.md)를 참조하세요.

대부분의 응용 프로그램 개발 시나리오에서 검색 데이터는 응용 프로그램 데이터 계층과 별도이며 해당 계층 외부에 있습니다. 예를 들어 응용 프로그램에서 온-프레미스 데이터베이스를 사용하여 재고 상태를 추적하는 경우 Azure 검색에 보관된 문서는 제품 이름, 가격, 사용 가능 여부 측면에서 비슷하거나 같은 데이터 값을 포함하지만 검색을 최적화하기 위해 역방향 인덱스에 저장됩니다.

검색하려는 항목당 문서 하나를 포함하도록 계획해야 합니다. 예를 들어 영화 대여 응용 프로그램은 영화당 문서 하나를, 상점 응용 프로그램은 SKU당 문서 하나를, 온라인 교육 과정 응용 프로그램은 과정당 문서 하나를, 리서치 업체는 리포지토리의 학술 문서당 문서 하나를 포함할 수 있습니다.

문서는 하나 이상의 필드로 구성됩니다. 필드는 검색 용어로 토큰화되는 텍스트를 포함할 수 있으며 필터 또는 점수 매기기 프로필에서 사용할 수 있는 토큰화되지 않는 값 또는 텍스트가 아닌 값도 포함할 수 있습니다. 각 필드에 대해 지원되는 이름, 데이터 형식, 검색 기능은 인덱스 스키마에 의해 결정됩니다. 각 인덱스 스키마의 필드 중 하나는 ID로 지정해야 하며 각 문서는 인덱스에서 해당 문서를 고유하게 식별하는 ID 필드 값을 포함해야 합니다. 다른 모든 문서 필드는 선택 사항이며 지정하지 않으면 null 값으로 기본 설정됩니다. null 값은 역방향 인덱스에서 공간을 차지하지 않습니다.

문서를 업로드하려면 먼저 서비스에 인덱스를 만들어야 합니다. 이 첫 단계에 대한 자세한 내용은 [인덱스 만들기](#CreateIndex)를 참조하세요.

**참고**: Azure 검색 공개 미리 보기에서는 전체 테스트 검색에서 영어만 지원됩니다.

<a name="AddOrUpdateDocuments"></a>
## 문서 추가, 업데이트 또는 삭제 ##

HTTP POST를 사용하여 지정한 인덱스에서 문서를 업로드, 병합, 병합하거나 업로드 또는 삭제할 수 있습니다. 업데이트할 문서 수가 많으면 문서 일괄 처리(일괄 처리당 문서 최대 1,000개 또는 일괄 처리당 약 16MB)를 수행하는 것이 좋습니다.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    
**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. HTTP POST를 사용하여 지정한 인덱스에서 문서를 업로드, 병합, 병합하거나 업로드 또는 삭제할 수 있습니다.

요청 URI에는 문서를 게시할 인덱스를 지정하는 [index name]이 포함됩니다. 문서는 한 번에 하나의 인덱스에만 게시할 수 있습니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **문서 추가** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

요청 본문에는 인덱싱할 하나 이상의 문서가 포함됩니다. 고유 키로 문서를 식별합니다. 각 문서는 upload, merge, mergeOrupload 또는 delete 작업과 연결됩니다. 업로드 요청은 키/값 쌍 집합으로 문서 데이터를 포함해야 합니다.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }
    
**문서 작업**

- `upload`: 업로드 작업은 새 문서는 삽입하고 기존 문서는 업데이트/교체하는 "upsert"와 비슷합니다. 업데이트 시에는 모든 필드가 바뀝니다.
- `merge`: 병합에서는 기존 문서를 지정한 필드로 업데이트합니다. 문서가 없으면 병합하지 못합니다. 문서의 기존 필드는 병합에서 지정하는 필드로 바뀝니다. 여기에는 `Collection(Edm.String)` 형식 필드가 포함됩니다. 예를 들어 값이 `["budget"]`인 "tags" 필드가 포함되어 있는 문서에서 "tags"에 대해 `["economy", "pool"]` 값과의 병합을 실행하면 "tags" 필드의 최종 값은 `["economy", "pool"]`이 됩니다. `["budget", "economy", "pool"]`이 **아닙니다**.
- `mergeOrUpload`: 이 작업은 지정된 키를 포함하는 문서가 인덱스에 이미 있는 경우 `merge`와 동일하게 동작합니다. 문서가 없는 경우에는 새 문서에 대해 `upload`와 같이 동작합니다.
- `delete`: 삭제 시에는 지정된 문서를 인덱스에서 제거합니다. `delete` 작업에서는 key 필드 값만 지정합니다. name 필드 등의 다른 필드를 지정하려고 하면 HTTP 400 오류가 발생합니다. 개별 필드를 문서에서 제거하려는 경우에는 대신 `merge`를 사용하고 필드를 명시적으로 `null`로 설정합니다. 

**응답**

상태 코드: 작업 성공 시의 응답에는 ‘200 OK’가 반환됩니다. 이 응답은 모든 항목이 올바르게 인덱싱되었음을 의미합니다(모든 항목의 'status' 필드가 true로 설정됨).

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

상태 코드: 하나 이상의 항목이 올바르게 인덱싱되지 않은 경우에는 207이 반환됩니다(인덱싱되지 않은 항목의 'status' 필드가 false로 설정됨).
 
    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

`errorMessage` 속성은 가능한 경우 인덱싱 오류의 원인을 표시합니다.

**참고**: 클라이언트 코드에서 207 응답이 자주 반환된다면 시스템 부하가 높기 때문일 수 있습니다. `errorMessage` 속성을 통해 시스템 부하가 원인인지 확인할 수 있습니다. 시스템 부하가 원인이라면 ***인덱싱 요청을 제한***하는 것이 좋습니다. 인덱싱 트래픽이 감소하지 않는 경우에는 시스템에서 모든 요청을 거부하며 503 오류가 발생할 수 있습니다.

상태 코드: 429는 인덱스당 문서 수의 할당량이 초과되었음을 나타냅니다. 이 경우에는 새 인덱스를 만들거나 업그레이드를 통해 용량 제한을 높여야 합니다.

**예제:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville", 
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## 문서 검색 ##

**검색** 작업은 GET 요청으로 실행되며, 일치하는 문서의 선택 조건을 제공하는 쿼리 매개 변수를 지정합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **검색** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI는 쿼리 매개 변수와 일치하는 모든 문서에 대해 쿼리할 인덱스를 지정합니다.

**쿼리 매개 변수**

`search=[string]`(선택) - 검색할 텍스트입니다. `searchFields`를 지정하지 않으면 기본적으로 모든 `searchable` 필드가 검색됩니다. `searchable` 필드를 검색할 때는 검색 텍스트 자체가 토큰화되므로 여러 용어를 공백으로 구분할 수 있습니다(예: `search=hello world`). 모든 용어를 일치시키려면 `*`를 사용합니다(부울 필터 쿼리에 유용할 수 있음). 이 매개 변수를 생략하는 것은 `*`로 설정하는 것과 같습니다. 검색 구문에 대한 세부 사항은 아래의 "단순 쿼리 구문"을 참조하세요.

  - **참고**: `searchable` 필드를 쿼리할 때 놀라운 결과가 나타나는 경우가 있습니다. 토크나이저에는 아포스트로피, 숫자 안의 쉼표 등과 같은 영어 텍스트에 일반적인 사례를 처리하는 논리가 포함되어 있습니다. 예를 들어 `search=123,456`은 개별 용어인 123과 456이 아니라 단일 용어인 123,456과 일치합니다. 여기서 쉼표는 영어에서 큰 숫자에 사용되는 1000 단위 구분 기호이기 때문입니다. 따라서 `search` 매개 변수에서 용어를 구분할 때는 마침표 대신 공백을 사용하는 것이 좋습니다.

`searchMode=any|all`(선택, 기본적으로 `any`) - 문서를 일치 항목으로 계산하기 이해 검색 용어의 일부를 일치시켜야 하는지 또는 전부를 일치시켜야 하는지 지정합니다.

`searchFields=[string]`(선택) - 지정한 텍스트를 검색할 쉼표로 구분된 필드 이름의 목록입니다. 대상 필드는 `searchable`로 표시되어야 합니다.

`$skip=#`(선택) - 건너뛸 검색 결과 수입니다. 100,000보다 클 수 없습니다. 문서를 순차적으로 검색하려는 경우 이 제한으로 인해 `$skip`을 사용할 수 없다면 대신 전체 정렬된 키에서 `$orderby`를 사용하고 범위 쿼리에 `$filter`를 사용하는 것이 좋습니다.

`$top=#`(선택) - 검색할 검색 결과 수입니다. 기본값은 50입니다. 1000보다 큰 값을 지정한 경우 1000개가 넘는 결과가 있으면 결과의 다음 페이지에 대한 링크와 함께 처음 1000개의 결과만 반환됩니다([아래 예제](#SearchResponse)의 `@odata.nextLink` 참조).

`$count=true|false`(선택, 기본적으로 `false`) - 총 결과 수를 가져올지 여부를 지정합니다. 이 값을 `true`로 설정하면 성능이 저하될 수 있습니다. 반환되는 개수는 근사값입니다.

`$orderby=[string]`(선택) - 결과 정렬 기준으로 사용할 쉼표로 구분된 식 목록입니다. 각 식은 필드 이름이거나 `geo.distance()` 함수 호출일 수 있습니다. 각 식 뒤에는 오름차순을 나타내는 `asc` 또는 내림차순을 나타내는 `desc`가 올 수 있습니다. 기본값은 오름차순입니다. 동률은 문서의 일치 점수로 구분됩니다. `$orderby`를 지정하지 않은 경우 기본 정렬 순서는 문서 일치 점수별 내림차순입니다. `$orderby` 절은 32개로 제한됩니다.

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않으면 스키마에서 검색 가능으로 표시된 모든 필드가 포함됩니다. 이 매개 변수를 `*`로 설정하여 모든 필드를 명시적으로 요청할 수도 있습니다.

`facet=[string]`(0 이상) - 패싯할 필드입니다. 필요에 따라 문자열은 쉼표로 구분된 `name:value` 쌍으로 표현된 패싯을 사용자 지정하는 매개 변수를 포함할 수 있습니다. 유효한 매개 변수는 다음과 같습니다.

- `count`(패싯 용어의 최대 개수, 기본값은 10). 최대값은 없지만 값이 크면 성능이 저하되며, 특히 패싯 필드에 포함된 고유한 항목 수가 많을수록 더욱 그렇습니다.
  - 예: `facet=category,count:5`는 패싯 결과에서 상위 5개의 범주를 가져옵니다.  
  - **참고**: `count` 매개 변수가 고유한 항목 수보다 작으면 결과가 정확하지 않을 수 있습니다. 이는 패싯 쿼리가 분할된 데이터베이스에서 분산된 방식 때문입니다. `count`를 늘리면 일반적으로 용어 수의 정확도가 높아지지만 성능이 저하됩니다. 
- `sort`(개수별로 *내림차순* 정렬하는 `count`, 개수별로 *오름차순* 정렬하는 `-count`, 값별로 *오름차순* 정렬하는 `value`, 값별로 *내림차순* 정렬하는 `-value` 중 하나)
  - 예: `facet=category,count:3,sort:count`는 각 도시 이름이 있는 문서 수를 기준으로 내림차순으로 정렬된 패싯 결과에서 상위 3개의 범주를 가져옵니다. 예를 들어 상위 3개의 범주가 Budget, Motel, Luxury인 경우 적중 항목 수가 Budget은 5개, Motel은 6개, Luxury는 4개이면 버킷이 Motel, Budget, Luxury 순으로 정렬됩니다.
  - 예: `facet=rating,sort:-value`는 값을 기준으로 내림차순으로 정렬된 가능한 모든 등급의 버킷을 생성합니다. 예를 들어 등급이 1~5인 경우 각 등급과 일치하는 문서 수에 상관없이 버킷이 5, 4, 3, 2, 1 순으로 정렬됩니다.
- `values`(파이프로 구분된 숫자 또는 패싯 항목 값의 동적 집합을 지정하는 `Edm.DateTimeOffset` 값)
  - 예: `facet=baseRate,values:10|20`은 각각 기본 요금이 1~9, 10~19 및 20 이상인 버킷 3개를 생성합니다.
  - 예: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z`는 각각 2010년 2월 이전에 리노베이션된 호텔과 2010년 2월 1일 이후에 리노베이션된 호텔에 대한 버킷 2개를 생성합니다.
- `interval`(숫자의 경우 0보다 큰 정수 간격 또는 날짜/시간 값의 경우 `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year`)
  - 예: `facet=baseRate,interval:100`은 기본 요금 범위 100을 기준으로 버킷을 생성합니다. 예를 들어 기본 요금이 모두 $60에서 $600 사이에 속한 경우 0-100, 100-200, 200-300, 300-400, 400-500 및 500-600에 대한 버킷이 생성됩니다.
  - 예: `facet=lastRenovationDate,interval:year`는 호텔이 리노베이션된 각 연도에 대해 하나의 버킷을 생성합니다.
- **참고**: `count`와 `sort`는 동일한 패싯 사양에서 함께 사용할 수 있지만 `interval` 또는 `values` 및 `interval`과 `values`는 함께 사용할 수 없습니다.

`$filter=[string]`(선택) - 표준 OData 구문의 구조화된 검색 식입니다. Azure 검색에서 지원하는 OData 식 문법의 하위 집합에 대한 자세한 내용은 [OData 식 구문](#ODataExpressionSyntax)을 참조하세요.

`highlight=[string]`(선택) - 적중 항목을 강조 표시하는 데 사용되는 쉼표로 구분된 필드 이름 집합입니다. `searchable` 필드만 적중 항목 강조 표시에 사용할 수 있습니다.

  `highlightPreTag=[string]`(선택) - 적중 항목 강조 표시 앞에 추가되는 문자열 태그입니다. `highlightPostTag`로 설정해야 합니다. URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

  `highlightPostTag=[string]`(선택) - 적중 항목 강조 표시에 추가되는 문자열 태그입니다. `highlightPreTag`로 설정해야 합니다. URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`scoringProfile=[string]`(선택) - 결과를 정렬하기 위해 일치하는 문서의 일치 점수를 평가할 점수 매기기 프로필의 이름입니다.

`scoringParameter=[string]`(0 이상) - 이름: 값 형식을 사용하여 점수 매기기 함수에 정의된 각 매개 변수(예: `referencePointParameter`)의 값을 나타냅니다. 예를 들어 점수 매기기 프로필이 "mylocation"라는 매개 변수를 사용하여 함수를 정의하는 경우 쿼리 문자열 옵션은 &scoringParameter=mylocation:-122.2,44.8입니다.

`api-version=[string]`(필수). 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업에는 `api-version`은 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **검색 ** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

<a name="SearchResponse"></a> **응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }
    
**예제:**

추가 예제는 [Azure 검색에 대한 OData 식 구문](https://msdn.microsoft.com/library/azure/dn798921.aspx) 페이지에서 확인할 수 있습니다.

1) 날짜를 기준으로 내림차순으로 정렬된 인덱스를 검색합니다.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-10-20-Preview

참고: DateTime 필드의 정밀도는 밀리초로 제한됩니다. 값을 더 작게 지정하는 타임스탬프(예: 타임스탬프 10:30:09.7552052의 초 부분 참조)를 푸시하면 반환 값이 반올림됩니다(또는 예제에 따라 10:30:09.7550000이 됨).

2) 패싯 검색에서 인덱스를 검색하고 범주, 등급, 태그 및 특정 범위의 baseRate이 있는 항목에 대한 패싯을 검색합니다.

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-10-20-Preview

3) 필터를 사용하여 사용자가 등급 3 및 범주 "Motel"을 클릭한 후 이전 패싯 쿼리 결과의 범위를 좁힙니다.

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-10-20-Preview

4) 패싯 검색에서 쿼리에 반환되는 고유 항목의 상한을 설정합니다. 기본값은 10이지만 `facet` 특성의 `count` 매개 변수를 사용하여 이 값을 늘리거나 줄일 수 있습니다.

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-10-20-Preview

5) 특정 필드(예: 언어 관련 필드) 내에서 인덱스를 검색합니다.

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-10-20-Preview

6) 여러 필드에서 인덱스를 검색합니다. 예를 들어 모두 동일한 인덱스 내에서 여러 언어로 된 검색 가능 필드를 저장하고 쿼리할 수 있습니다. 같은 문서에 영어 설명과 프랑스어 설명이 둘 다 있는 경우 쿼리 결과에서 둘 중 하나 또는 둘 다를 반환할 수 있습니다.

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-10-20-Preview
	
한 번에 하나의 인덱스만 쿼리할 수 있습니다. 한번에 하나씩 쿼리하려는 경우가 아니면 각 언어에 대한 여러 인덱스를 만들지 마세요.

7) 페이징 - 항목의 첫 번째 페이지(페이지 크기는 10)를 가져옵니다.

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-10-20-Preview

8) 페이징 - 항목의 두 번째 페이지(페이지 크기는 10)를 가져옵니다.

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-10-20-Preview

9) 특정 필드 집합을 검색합니다.

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-10-20-Preview

10) 특정 쿼리 식과 일치하는 문서를 검색합니다.

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-10-20-Preview

11) 인덱스를 검색하여 적중 항목이 강조 표시된 조각을 반환합니다.

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-10-20-Preview
    
12) 인덱스를 검색하여 참조 위치에서 가까이 있는 순으로 정렬된 문서를 반환합니다.

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-10-20-Preview

13) 두 개의 거리 점수 매기기 함수를 사용하는 "geo"라는 점수 매기기 프로필이 있는 것으로 가정하여 인덱스를 검색합니다. 두 함수는 각각 "currentLocation"이라는 매개 변수와 "lastLocation"이라는 매개 변수를 정의합니다.

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-10-20-Preview


<a name="LookupAPI"></a>
##문서 조회##

**문서 조회** 작업은 Azure 검색에서 문서를 검색합니다. 사용자가 특정 검색 결과를 클릭할 때 해당 문서에 대한 특정 세부 정보를 조회하려는 경우 이 작업을 사용하면 유용합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters] 
    api-key: [admin key]
    
**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. 다음과 같이 **문서 조회** 요청을 생성할 수 있습니다.

    GET /indexes/[index name]/docs/key?[query parameters] 

기존 OData 구문을 키 조회에 사용할 수도 있습니다.

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

요청 URI에는 검색할 문서와 문서를 검색할 인덱스를 지정하는 [index name] 및 [key]가 포함됩니다. 한 번에 하나의 문서만 가져올 수 있습니다. 단일 요청에서 여러 문서를 가져오려면 **Search**를 사용합니다.

**쿼리 매개 변수**

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않거나 `*`로 지정하면 스키마에서 검색 가능으로 표시된 모든 필드가 프로젝션에 포함됩니다.

`api-version=[string]`(필수). 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업에는 `api-version`은 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **문서 조회 ** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      field_name: field_value (fields matching the default or specified projection)
    }
    
**예제**

'2' 키가 있는 문서 조회

    GET /indexes/hotels/docs/2?api-version=2014-10-20-Preview

OData 구문을 사용하여 '3' 키가 있는 문서 조회

    GET /indexes('hotels')/docs('3')?api-version=2014-10-20-Preview

<a name="CountDocs"></a>
##문서 수 계산##

**문서 수 계산** 작업은 검색 인덱스의 문서 수를 검색합니다. `$count` 구문은 OData 프로토콜의 일부입니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]
    
**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **문서 수 계산** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 지정한 인덱스의 문서 컬렉션에 포함된 모든 항목의 수를 반환하도록 서비스에 지시합니다.

`api-version` 매개 변수는 필수 사항입니다. 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Accept`: 이 값은 `text/plain`으로 설정해야 합니다.
- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **문서 수 계산 ** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문에는 일반 텍스트로 서식이 지정된 정수로 개수 값이 포함됩니다.

<a name="Suggestions"></a>
##제안##

**제안** 작업에서는 부분 검색 입력을 기준으로 제안을 검색합니다. 이 작업은 일반적으로 사용자가 검색 용어를 입력할 때 미리 입력된 제안을 제공하기 위해 검색 상자에서 사용됩니다.

제안 요청은 대상 문서를 제공하기 위한 것이므로 같은 검색 입력과 일치하는 후보 문서가 여러 개이면 제안되는 텍스트가 반복될 수 있습니다. `$select`를 사용하면 각 제안의 원본인 문서를 확인할 수 있도록 문서 키를 비롯한 다른 문서 필드를 검색할 수 있습니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]


**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **제안** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI는 쿼리할 인덱스의 이름을 지정합니다. 또한 쿼리 문자열에 부분적으로 입력된 검색 용어도 포함합니다.

**쿼리 매개 변수**

`search=[string]` - 쿼리를 제안하는 데 사용할 검색 텍스트입니다. 설명은 3~25자여야 합니다.

`highlightPreTag=[string]`(선택, 기본값 = '`) - a string tag that prepends to search hits. Must be set with `highlightPostTag'). URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`highlightPostTag=[string]`(선택, 기본값 = '`) - a string tag that appends to search hits. Must be set with `highlightPreTag'). URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`suggesterName=[string]`(선택) - 인덱스 정의의 일부인 `suggesters` 컬렉션에 지정된 확인기의 이름입니다. 이 옵션을 사용하지 않으면 `"suggestions": true`로 표시된 필드에서 작동하고 접두사 일치만 지원하는 이전 버전의 구현을 기반으로 제안이 수행됩니다.

`fuzzy=[boolean]`(선택, 기본값=false) - true로 설정하면 검색 텍스트에 대체되었거나 누락된 문자가 있더라도 이 API는 제안을 찾습니다. 이 경우 일부 시나리오에서는 검색 환경이 개선되지만, 유사 제안 검색 속도가 느려지며 리소스가 더 많이 사용되므로 성능은 저하됩니다.

`searchFields=[string]`(선택) - 지정된 검색 텍스트를 검색하기 위한 쉼표로 구분된 필드 이름 목록입니다. 대상 필드가 제안을 사용하도록 설정되어 있어야 합니다.

`$top=#`(선택, 기본값=5) - 검색할 제안의 수입니다. 값은 1~100 사이의 숫자여야 합니다.

`$filter=[string]`(선택) - 제안을 검색할 때 고려할 문자를 필터링하는 식입니다.

`$orderby=[string]`(선택) - 결과 정렬 기준으로 사용할 쉼표로 구분된 식 목록입니다. 각 식은 필드 이름이거나 `geo.distance()` 함수 호출일 수 있습니다. 각 식 뒤에는 오름차순을 나타내는 `asc` 또는 내림차순을 나타내는 `desc`가 올 수 있습니다. 기본값은 오름차순입니다. `$orderby` 절은 32개로 제한됩니다.

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않으면 문서 키와 제안 텍스트만 반환됩니다.

`api-version=[string]`(필수). 유효한 값은 `2014-07-31-Preview` 또는 `2014-10-20-Preview`입니다. 각 요청에서 버전 관련 동작을 가져오는 데 사용할 값을 지정할 수 있지만 코드 전체에서 동일한 버전을 사용하는 것이 가장 좋습니다. 권장되는 버전은 일반적으로 `2014-07-31-Preview`입니다. 또는 `2014-10-20-Preview`를 사용하여 실험적 기능을 평가할 수 있습니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업에는 `api-version`은 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **제안 ** 요청은 관리 키 또는 쿼리 키를 `api-key`로 지정할 수 있습니다.

  요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure Preview 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

프로젝션 옵션을 사용하여 필드를 검색하는 경우에는 배열의 각 요소에 필드가 포함됩니다.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **예제**

부분 검색 입력이 'lux'인 제안 5개 검색

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2014-10-20-Preview




 

<!---HONumber=July15_HO4-->