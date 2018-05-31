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
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786922"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>인덱서 만들기(Azure Search Service REST api-version=2017-11-11-Preview)

이 API 참조는 인식 검색의 인덱싱 기능 향상을 포함하는, 미리 보기 특정 버전의 설명서입니다.

[일반 공급](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 버전과 마찬가지로, HTTP POST 요청을 사용하여 Azure Search 서비스 내에서 새 인덱서를 만들 수 있습니다. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key**는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [Azure Search의 보안](search-security-overview.md)에서 인증 섹션을 참조하세요. [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version**이 필요합니다. 현재 버전은 `2016-09-01`입니다. 자세한 내용은 [Azure Search의 API 버전](search-api-versions.md)을 참조하세요.

인덱서를 만드는 방법에 대한 데이터 플랫폼 특정 지침을 보려면 [관련 문서](search-indexer-overview.md#next-steps)의 전체 목록을 포함하는 [인덱서 개요](search-indexer-overview.md)에서 시작하세요.

> [!NOTE]  
>  허용되는 최대 인덱서 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱서를 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.    

## <a name="request"></a>요청  
 요청 본문에는 인덱서 정의가 포함됩니다. 이 정의는 인덱싱의 데이터 원본 및 대상 인덱스와 선택적 인덱싱 일정 및 매개 변수를 지정합니다.  

 요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 샘플 요청은 이 항목의 뒷부분에 나와 있습니다.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>인덱서 일정  
인덱서는 선택적으로 일정을 지정할 수 있습니다. 일정이 있을 경우, 인덱서가 일정에 따라 주기적으로 실행됩니다. 스케줄러는 기본 제공되며, 외부 스케줄러를 사용할 수 없습니다. **일정**은 다음과 같은 특성을 갖습니다. 

-   **간격**: 필수입니다. 인덱서 실행 간격 또는 기간을 지정하는 기간 값입니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 패턴: `"P[nD][T[nH][nM]]".` 예: `PT15M`(15분마다), `PT2H`(2시간마다)  

-   **StartTime** - 선택 사항입니다. 인덱서 실행을 시작해야 하는 UTC 날짜/시간입니다.  

### <a name="indexer-parameters"></a>인덱서 매개 변수  
 인덱서는 필요에 따라 동작에 영향을 주는 여러 매개 변수를 지정할 수 있습니다. 아래 나열된 모든 매개 변수는 선택 사항입니다.  

-   **maxFailedItems**: 인덱서 실행이 실패한 것으로 간주되기 전에 인덱싱하지 못할 수도 있는 항목 수입니다. 기본값은 0입니다. 실패한 항목에 대한 정보는 [인덱서 상태 가져오기 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 작업에서 반환됩니다.  

-   **maxFailedItemsPerBatch**: 인덱서 실행이 실패한 것으로 간주되기 전에 각 배치당 인덱싱하지 못할 수도 있는 항목 수입니다. 기본값은 0입니다.  

-   **batchSize:** 성능을 향상하기 위해 단일 배치로 인덱싱되고 데이터 원본에서 읽는 항목 수를 지정합니다. 기본값은 데이터 원본 형식에 따라 달라집니다. Azure SQL 및 Azure Cosmos DB의 경우 1000, Azure Blob Storage의 경우 10입니다.

### <a name="field-mapping-parameters"></a>필드 매핑 매개 변수

인덱서 정의에는 Azure Search 인덱스에서 원본 필드를 대상 필드에 매핑하기 위한 필드 연결이 포함됩니다. 콘텐츠 전송이 직접 경로 또는 보강된 경로를 따르는지에 따라 다음 두 가지 유형의 연결이 있습니다.

+ **fieldMappings**는 선택 사항이며, 소스-대상 필드 이름이 일치하지 않거나 함수를 지정하려는 경우에 적용됩니다.
+ **outputFieldMappings**은 [보강 파이프라인](cognitive-search-concept-intro.md)을 작성하는 경우에 필요합니다. 보강 파이프라인에서 출력 필드는 보강 프로세스 중에 정의된 구문입니다. 예를 들어, 출력 필드는 원본 문서에 있는 두 개의 개별 필드에서 보강 중에 작성된 복합 구조일 수 있습니다. 

#### <a name="fieldmappings"></a>fieldMappings

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

#### <a name="outputfieldmappings"></a>outputFieldMappings

기능이 인덱서에 바인딩된 인식 검색 시나리오에서는 `outputFieldMappings`를 추가하여 콘텐츠를 제공하는 보강 단계의 출력을 인덱스의 검색 가능한 필드에 연결해야 합니다.

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

#### <a name="field-mapping-functions"></a>필드 매핑 함수

필드 매핑을 사용하는 경우 *필드 매핑 함수*를 통해 원본 필드 값을 변환할 수도 있습니다. 예를 들어, 임의 문자열 값이 base64로 인코드될 수 있으므로 문서 키 필드를 채우는 데 사용할 수 있습니다.

필드 매핑 함수를 사용하는 시기 및 방법에 대한 자세한 내용은 [필드 매핑 함수](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)를 참조하세요.

### <a name="request-body-examples"></a>요청 본문 예제  
 다음 예제에서는 `ordersds` 데이터 원본에서 참조하는 테이블의 데이터를 2015년 1월 1일 UTC에 시작하여 매 시간 실행되는 일정으로 `orders` 인덱스에 복사하는 인덱서를 만듭니다. 각 일괄 처리에서 인덱싱에 실패하는 항목이 5개 이하이며 인덱싱에 실패하는 총 항목 수가 10개 이하이면 각 인덱서 호출은 성공합니다.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>response  
 요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.  

## <a name="see-also"></a>참고 항목

+ [인식 검색 개요](cognitive-search-concept-intro.md)
+ [빠른 시작: 인식 검색 시도](cognitive-search-quickstart-blob.md)
+ [필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
