---
title: Azure 검색 BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
description: Azure 검색 BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/26/2016
ms.author: eugenesh

---
# Azure 검색 BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
이 문서에서는 JSON이 포함된 blob에서 구조화된 콘텐츠를 추출하도록 Azure 검색 blob 인덱서를 구성하는 방법을 보여 줍니다.

## 시나리오
기본적으로 [Azure 검색 BLOB 인덱서](search-howto-indexing-azure-blob-storage.md)는 단일 텍스트 청크로 JSON BLOB을 구문 분석합니다. JSON 문서의 구조를 유지하려는 경우가 많습니다. 예를 들어 JSON 문서를 지정하고

    { 
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

"text", "datePublished" 및 "tags" 필드를 포함하는 Azure 검색 문서로 구문 분석하려고 할 수 있습니다.

또는 Blob에 **JSON 개체 배열**이 포함되면 각 배열 요소를 별도의 Azure 검색 문서로 나타내려고 할 수 있습니다. 예를 들어 이 JSON이 있는 blob가 있다고 가정할 경우

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

각각 "id" 및 "text" 필드가 있는 별도의 3개 문서로 Azure 검색 인덱스를 채울 수 있습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기 상태입니다. **2015-02-28-Preview** 버전을 사용하여 REST API로만 제공됩니다. 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요.
> 
> 

## JSON 인덱싱 설정
JSON blob을 인덱싱하려면 `parsingMode` 구성 매개 변수를 `json`(각 blob을 단일 문서로 인덱싱) 또는 `jsonArray`(blob에 JSON 배열이 포함된 경우)로 설정합니다.

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

필요한 경우 **필드 매핑**을 사용하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택합니다. 아래에서 자세히 설명합니다.

> [!IMPORTANT]
> `json` 또는 `jsonArray` 구문 분석 모드를 사용하는 경우 Azure 검색은 데이터 원본의 모든 BLOB을 JSON으로 가정합니다. 동일한 데이터 원본에서 JSON 및 비 JSON BLOB을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.
> 
> 

## 필드 매핑을 사용하여 검색 문서 빌드
현재 Azure 검색은 기본 데이터 형식, 문자열 배열 및 GeoJSON 포인트만 지원하므로 임의의 JSON 문서를 직접 인덱싱할 수 없습니다. 그러나 **필드 매핑**을 사용하여 JSON 문서의 부분을 선택하고 검색 문서의 최상위 필드로 이 부분을 "올릴" 수 있습니다. 필드 매핑의 기본에 대해 알아 보려면 [데이터 원본 및 검색 인덱스의 차이를 극복하는 Azure 검색 인덱서 필드 매핑](search-indexer-field-mappings.md)을 참조하십시오.

예제 JSON 문서를 다시 보겠습니다.

    { 
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Edm.String 형식의 `text`, Edm.DateTimeOffset 형식의 `date` 및 Collection(Edm.String) 형식의 `tags` 필드가 있는 검색 인덱스가 있다고 가정해 보겠습니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

매핑의 원본 필드 이름은 [JSON 포인터](http://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 상세히 검색합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 필드 매핑 경로의 원본 필드 이름이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 다른 스키마를 사용하여 문서를 지원할 수 있도록 이 작업을 수행합니다(일반적인 사용 사례). 유효성을 검사하지 않기 때문에 필드 매핑 사양에 오타가 발생하지 않도록 주의해야 합니다.
> 
> 

JSON 문서에 단순한 최상위 속성만 포함되는 경우 필드 매핑이 전혀 필요 없습니다. 예를 들어 JSON이 다음과 같은 경우 최상위 속성 "text", "datePublished" 및 "tags"가 검색 인덱스의 해당 필드에 직접 매핑됩니다.

    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
     }

## 중첩된 JSON 배열 인덱싱
JSON 개체의 배열을 인덱싱하려고 하지만 해당 배열이 문서 내에 중첩되어 있으면 어떻게 할까요? `documentRoot` 구성 속성을 사용하여 배열을 포함하는 속성을 선택할 수 있습니다. 예를 들어 blob은 다음과 같습니다.

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

다음 구성을 사용하여 "level2" 속성에 포함된 배열을 인덱싱합니다.

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## 요청 예제
다음은 이를 모두 포함한 전체 페이로드 예제입니다.

데이터 원본:

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

인덱서:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## Azure 검색 개선 지원
기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

<!---HONumber=AcomDC_0727_2016-->