---
title: 인덱서 만들기(Azure Search Service REST api-version=2017-11-11-Preview)
description: 미리 보기 API에서는 보강 출력을 Azure Search 인덱스의 필드에 매핑하는 데 사용되는 outputFieldMapping 매개 변수를 포함하도록 인덱서가 확장됩니다.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365188"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>인덱서 만들기(Azure Search Service REST api-version=2017-11-11-Preview)

이 API 참조는 [인식 검색](cognitive-search-concept-intro.md) 요소를 인덱서 만들기 API에 추가하는 미리 보기 관련 버전의 설명서입니다. [일반 공급](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 버전과 마찬가지로, HTTP POST 요청을 사용하여 Azure Search 서비스 내에서 새 인덱서를 만들 수 있습니다. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key**는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [Azure Search의 보안](search-security-overview.md)에서 인증 섹션을 참조하세요. [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version**이 필요합니다. 현재 GA(일반 공개) 버전은 `api-version=2017-11-11`이지만 인식 검색에 대한 미리 보기 버전(`api-version=2017-11-11-Preview`)이 필요합니다.  자세한 내용은 [Azure Search의 API 버전](search-api-versions.md)을 참조하세요.

인덱서를 만드는 방법에 대한 데이터 플랫폼 특정 지침을 보려면 [관련 문서](search-indexer-overview.md#next-steps)의 전체 목록을 포함하는 [인덱서 개요](search-indexer-overview.md)에서 시작하세요.

> [!NOTE]  
>  허용되는 최대 인덱서 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱서를 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다. 표준 고화질 서비스는 인덱서를 지원하지 않습니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.    

## <a name="request"></a>요청  

[데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [인덱스](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [기술 집합](ref-create-skillset.md)은 [인덱서](search-indexer-overview.md) 정의의 일부이지만 각각 다른 조합으로 사용될 수 있는 독립적인 구성 요소입니다. 예를 들어, 여러 인덱서에서 동일한 데이터 원본을 사용하거나, 여러 인덱서에서 동일한 인덱스를 사용하거나, 여러 인덱서를 단일 인덱스로 작성할 수 있습니다.

 요청 본문에는 다음 부분으로 구성된 인덱서 정의가 포함됩니다.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [매개 변수](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>요청 구문

요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 샘플 요청은 이 문서의 뒷부분에 나와 있습니다.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

[데이터 원본 정의](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에는 종종 인덱서에서 원본 플랫폼 특성을 악용하는 데 사용할 수 있는 속성이 포함됩니다. 이와 같이 인덱서로 전달하는 데이터 원본은 Azure blob의 콘텐츠 형식 필터링 또는 Azure SQL Database에 대한 쿼리 제한 시간과 같은 특정 속성 및 매개 변수의 가용성을 결정합니다. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[인덱스 스키마](https://docs.microsoft.com/rest/api/searchservice/create-index)는 검색 가능, 필터링 가능, 조회 가능 및 기타 필드 사용 방식을 결정하는 특성이 포함된 필드 컬렉션을 정의합니다. 인덱싱하는 동안 인덱서는 데이터 원본을 탐색하고, 필요에 따라 문서를 해독하고 정보를 추출하며, 결과를 JSON으로 serialize하고, 인덱스에 대해 정의된 스키마를 기반으로 페이로드를 인덱싱합니다.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[인식 검색(미리 보기)](cognitive-search-concept-intro.md)은 Azure Search의 자연어 및 이미지 처리 기능을 의미하며, 데이터를 수집하여 엔터티, 키 구, 언어, 이미지의 정보 등을 추출하는 동안 적용됩니다. 콘텐츠에 적용되는 변환은 인덱서당 하나씩 단일 [*기술 집합*](ref-create-skillset.md)으로 통합하는 *기술*을 통해 이루어집니다. 데이터 원본 및 인덱스와 마찬가지로 기술 집합은 인덱서에 연결하는 독립적인 구성 요소입니다. 다른 인덱서로 기술 집합의 용도를 변경할 수 있지만 각 인덱서는 한 번에 하나의 기술 집합만 사용할 수 있습니다.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
인덱서는 선택적으로 일정을 지정할 수 있습니다. 일정이 없으면 데이터 원본 연결, 탐색 및 인덱싱과 같은 요청을 보내는 즉시 인덱서가 실행됩니다. 장기 실행 인덱싱 작업을 포함하는 일부 시나리오의 경우 일정은 최대값인 24시간을 넘어 [처리 기간을 확장](search-howto-reindex.md#scale-out-indexing)하는 데 사용됩니다. 일정이 있을 경우, 인덱서가 일정에 따라 주기적으로 실행됩니다. 스케줄러는 기본 제공되며, 외부 스케줄러를 사용할 수 없습니다. **Schedule**에는 다음과 같은 특성이 있습니다. 

-   **간격**: 필수입니다. 인덱서 실행 간격 또는 기간을 지정하는 기간 값입니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 패턴: `"P[nD][T[nH][nM]]".` 예: `PT15M`(15분마다), `PT2H`(2시간마다)  

-   **StartTime** - 선택 사항입니다. 인덱서 실행을 시작해야 하는 UTC 날짜/시간입니다.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

필요한 경우 런타임 동작을 수정하는 구성 매개 변수를 인덱서에 적용할 수 있습니다. 구성 매개 변수는 인덱서 요청에서 쉼표로 구분됩니다. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>모든 인덱서에 대한 일반 매개 변수

| 매개 변수 | 형식 및 허용되는 값   | 사용 현황  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | 정수 <br/>기본값은 원본별로 결정됩니다(Azure SQL Database 및 Azure Cosmos DB의 경우 1000, Azure Blob Storage의 경우 10) | 성능을 향상시키기 위해 단일 배치로 인덱싱되고 데이터 원본에서 읽는 항목 수를 지정합니다. |
| `"maxFailedItems"` | 정수 <br/>기본값은 0입니다. | 인덱서 실행이 실패로 간주되기 전에 허용할 오류 횟수입니다. 모든 오류에 대해 인덱싱 프로세스를 중지하지 않으려면 -1로 설정합니다. [인덱서 상태 가져오기](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)를 사용하여 실패한 항목에 대한 정보를 가져올 수 있습니다.  |
| `"maxFailedItemsPerBatch"` | 정수 <br/>기본값은 0입니다. | 인덱서 실행이 실패로 간주되기 전에 각 일괄 처리에서 허용할 오류 횟수입니다. 모든 오류에 대해 인덱싱 프로세스를 중지하지 않으려면 -1로 설정합니다. |

#### <a name="blob-configuration-parameters"></a>Blob 구성 매개 변수

몇 가지 매개 변수는 [Azure blob 인덱싱](search-howto-indexing-azure-blob-storage.md)과 같은 특정 인덱서에만 사용됩니다.

| 매개 변수 | 형식 및 허용되는 값   | 사용 현황  |
|-----------|---------------------------|--------|
| `"parsingMode"` | 문자열<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 Blob Storage의 일반 텍스트 파일에 대한 인덱싱 성능을 향상시키기 위해 `text`로 설정합니다. <br/>[CSV blob](search-howto-index-csv-blobs.md)의 경우 blob이 일반 CSV 파일이면 `delimitedText`로 설정합니다. <br/>[JSON blob](search-howto-index-json-blobs.md)의 경우 `json`으로 설정하여 구조화된 콘텐츠를 추출하거나 `jsonArray`(미리 보기)로 설정하여 배열의 개별 요소를 Azure Search에서 별도의 문서로 추출합니다. |
| `"excludedFileNameExtensions"` | 문자열<br/>쉼표로 구분된 목록 | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 목록에 있는 모든 파일 형식을 무시합니다. 예를 들어, ".png, .png, .mp4"를 제외하여 인덱싱하는 동안 이러한 파일을 건너뛸 수 있습니다. | 
| `"indexedFileNameExtensions"` | 문자열<br/>쉼표로 구분된 목록 | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 파일 확장명이 목록에 있으면 blob을 선택합니다. 예를 들어, 특정 응용 프로그램 파일 ".docx, .pptx, .msg"의 인덱싱에 집중하여 이러한 파일 형식을 특별히 포함할 수 있습니다. | 
| `"failOnUnsupportedContentType"` | true <br/>false(기본값) | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 일부 콘텐츠 형식(파일 확장명)을 사전에 알지 못하고 지원되지 않는 콘텐츠 형식이 발견되었을 때 인덱싱을 계속하려면 `false`로 설정합니다. |
| `"failOnUnprocessableDocument"` | true <br/>false(기본값)| [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 문서 인덱싱에 실패했을 때 인덱싱을 계속하려면 `false`로 설정합니다. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true <br/>false(기본값)| [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 여전히 너무 커서 처리할 수 없는 blob 콘텐츠에 대한 저장소 메타데이터를 계속 인덱싱하려면 이 속성을 `true`로 설정합니다.  너무 큰 Blob은 기본적으로 오류로 처리됩니다. Blob 크기에 제한은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요. |
| `"delimitedTextHeaders"` | 문자열<br/>쉼표로 구분된 목록| [CSV blob(미리 보기)](search-howto-index-csv-blobs.md)의 경우 열 머리글의 쉼표로 구분된 목록을 지정합니다. 이는 인덱스에서 원본 필드를 대상 필드에 매핑하는 데 유용합니다. |
| `"delimitedTextDelimiter"` | 문자열<br/>사용자 정의 | [CSV blob(미리 보기)](search-howto-index-csv-blobs.md)의 경우 CSV 파일의 각 행이 새 문서를 시작하는 행 끝 구분 기호를 지정합니다(예: '"|"`).  |
| `"firstLineContainsHeaders"` | true(기본값) <br/>false | [CSV blob(미리 보기)](search-howto-index-csv-blobs.md)은 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.|
| `"documentRoot"`  | 문자열<br/>사용자 정의 | [JSON 배열(미리 보기)](search-howto-index-json-blobs.md#nested-json-arrays)의 경우 구조화되거나 반구조화된 문서가 있을 때 이 속성을 사용하여 배열 경로를 지정할 수 있습니다. |
| `"dataToExtract"` | 문자열<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"`(기본값) | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우:<br/>[표준 blob 속성 및 사용자 지정 메타데이터](../storage/blobs/storage-properties-metadata.md)만 인덱싱하려면 `"storageMetadata"`로 설정합니다. <br/>Azure Blob Storage 하위 시스템에서 제공하는 메타데이터를 추출하려면 `"allMetadata"`로 설정합니다. 그러면 [콘텐츠 형식별 메타데이터](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)(예: .png 파일에 고유한 메타데이터)가 인덱싱됩니다. <br/>각 blob에서 모든 메타데이터 및 텍스트 콘텐츠를 추출하려면 `"contentAndMetadata"`로 설정합니다. <br/><br/>[인식 검색(미리 보기)에서 이미지 분석](cognitive-search-concept-image-scenarios.md)의 경우 `"imageAction"`이 `"generateNormalizedImages"`로 설정되면 `"dataToExtract"` 설정은 인덱서에 이미지 콘텐츠에서 추출할 데이터를 알려 줍니다. Azure blob에서 .PDF 또는 다른 응용 프로그램이나 이미지 파일(예: .jpg 및 .png)의 포함된 이미지 콘텐츠에 적용됩니다.  |
| `"imageAction"` |  문자열<br/>`"none"`<br/>`"generateNormalizedImages"` | [Azure blob](search-howto-indexing-azure-blob-storage.md)의 경우 데이터 집합에서 포함된 이미지나 이미지 파일을 무시하려면 `"none"`으로 설정합니다. 이것이 기본값입니다. <br/><br/>[인식 검색에서 이미지 분석](cognitive-search-concept-image-scenarios.md)의 경우 이미지에서 텍스트(예: 정지 신호의 "정지"라는 단어)를 추출하여 콘텐츠 필드의 일부로 포함하려면 `"generateNormalizedImages"`로 설정합니다. 이미지를 분석하는 동안 인덱서는 문서 해독의 일환으로 정규화된 이미지의 배열을 만들고 생성된 정보를 콘텐츠 필드에 포함합니다. 이 작업을 수행하려면 `"dataToExtract"`를 `"contentAndMetadata"`로 설정해야 합니다. 정규화된 이미지는 시각적 검색 결과에 이미지를 포함할 때 일관된 렌더링을 위해 크기 및 회전이 지정되어 균일한 이미지를 출력하는 추가 처리를 의미합니다(예: [JFK 데모](https://github.com/Microsoft/AzureSearch_JFK_Files)에서 볼 수 있는 그래프 컨트롤의 동일 크기 사진). 이 정보는 이미지를 사용할 때 각 이미지에 대해 생성됩니다. |


#### <a name="other-configuration-parameters"></a>기타 구성 매개 변수

다음 매개 변수는 Azure SQL Database에만 적용됩니다.

| 매개 변수 | 형식 및 허용되는 값   | 사용 현황  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | 문자열<br/>"hh:mm:ss"<br/>"00:05:00"| [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)의 경우 기본값인 5분을 넘어 시간 제한을 늘리려면 이 매개 변수를 설정합니다.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

인덱서 정의에는 Azure Search 인덱스에서 원본 필드를 대상 필드에 매핑하기 위한 필드 연결이 포함됩니다. 콘텐츠 전송이 직접 경로 또는 보강된 경로를 따르는지에 따라 다음 두 가지 유형의 연결이 있습니다.

+ **fieldMappings**는 선택 사항이며, 소스-대상 필드 이름이 일치하지 않거나 함수를 지정하려는 경우에 적용됩니다.
+ **outputFieldMappings**은 [보강 파이프라인](cognitive-search-concept-intro.md)을 작성하는 경우에 필요합니다. 보강 파이프라인에서 출력 필드는 보강 프로세스 중에 정의된 구문입니다. 예를 들어, 출력 필드는 원본 문서에 있는 두 개의 개별 필드에서 보강 중에 작성된 복합 구조일 수 있습니다. 

다음 예제에서는 `_id` 필드가 있는 원본 테이블을 가정합니다. Azure Search에서는 밑줄로 시작하는 필드 이름을 사용할 수 없으므로 필드 이름을 바꿔야 합니다. 이렇게 하려면 다음과 같이 인덱서의 `fieldMappings` 속성을 사용하면 됩니다.

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

여러 필드 매핑을 지정할 수 있습니다.

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

원본 필드 이름과 대상 필드 이름 모두 대/소문자를 구분하지 않습니다.

필드 매핑이 유용한 시나리오에 대한 자세한 내용은 [Search 인덱서 필드 매핑](https://docs.microsoft.com/azure/search/search-indexer-field-mappings)을 참조하세요.

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

기능이 인덱서에 바인딩된 [인식 검색](cognitive-search-concept-intro.md) 시나리오에서는 `outputFieldMappings`를 추가하여 콘텐츠를 제공하는 보강 단계의 출력을 인덱스의 검색 가능한 필드에 연결해야 합니다.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>필드 매핑 함수

필드 매핑을 사용하는 경우 *필드 매핑 함수*를 통해 원본 필드 값을 변환할 수도 있습니다. 예를 들어, 임의 문자열 값이 base64로 인코드될 수 있으므로 문서 키 필드를 채우는 데 사용할 수 있습니다.

필드 매핑 함수를 사용하는 시기 및 방법에 대한 자세한 내용은 [필드 매핑 함수](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)를 참조하세요.

## <a name="request-examples"></a>요청 예제  
 첫 번째 예제에서는 `ordersds` 데이터 원본에서 참조하는 테이블의 데이터를 2015년 1월 1일 UTC에 시작하여 매 시간 실행되는 일정으로 `orders` 인덱스에 복사하는 인덱서를 만듭니다. 각 일괄 처리에서 인덱싱에 실패하는 항목이 5개 이하이며 인덱싱에 실패하는 총 항목 수가 10개 이하이면 각 인덱서 호출은 성공합니다.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

두 번째 예제에서는 기술 집합에 대한 참조 및 [outputFieldMappings](#output-fieldmappings)가 나타내는 인식 검색 작업을 보여 줍니다. [기술 집합](ref-create-skillset.md)은 별도로 정의되는 상위 수준의 리소스입니다. 이 예제는 [인식 검색 자습서](cognitive-search-tutorial-blob.md)에 있는 인덱서 정의를 축약한 것입니다.

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>response  
 요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.  

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [인식 검색 개요](cognitive-search-concept-intro.md)
+ [빠른 시작: 인식 검색 시도](cognitive-search-quickstart-blob.md)
+ [필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
