---
title: 전체 텍스트 검색을 위해 Azure Blob 인덱서에서 JSON Blob 인덱싱 - Azure Search
description: Azure Search Blob 인덱서를 사용하여 텍스트 콘텐츠에 대해 Azure JSON Blob을 크롤링합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753152"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB 인덱서를 사용하여 JSON BLOB 인덱싱
이 문서에서는 Azure Blob Storage의 JSON Blob에서 구조화된 콘텐츠를 추출하도록 Azure Search Blob 인덱서를 구성하는 방법을 보여줍니다.

[포털](#json-indexer-portal), [REST API](#json-indexer-rest) 또는 [.NET SDK](#json-indexer-dotnet)를 사용하여 JSON 콘텐츠를 인덱싱할 수 있습니다. 모든 방법에 공통되는 JSON 문서는 Azure Storage 계정의 blob 컨테이너에 있습니다. 다른 비 Azure 플랫폼에서 JSON 문서를 푸시하는 방법에 대한 지침은 [Azure Search에서 데이터 가져오기](search-what-is-data-import.md)를 참조하세요.

Azure Blob Storage에서 JSON Blob은 일반적으로 단일 JSON 문서 또는 JSON 배열입니다. Azure Search에서 Blob 인덱서는 요청에 대한 **parsingMode** 매개 변수를 설정하는 방법에 따라 생성을 구문 분석할 수 있습니다.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>포털 사용

JSON 문서를 인덱싱하는 가장 쉬운 방법은 [Azure Portal](https://portal.azure.com/)에서 마법사를 사용하는 것입니다. Azure Blob 컨테이너의 메타데이터를 구문 분석하여 [**데이터 가져오기**](search-import-data-portal.md) 마법사는 기본 인덱스를 만들고, 원본 필드를 대상 인덱스 필드에 매핑하고, 인덱스를 로드하는 과정을 단일 작업으로 진행할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

### <a name="1---prepare-source-data"></a>1 - 원본 데이터 준비

Azure Storage 계정과 Blob 스토리지 및 JSON 문서 컨테이너가 있어야 합니다. 이러한 작업을 잘 모를 경우 [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data)에서 "Azure Blob service 설정 및 샘플 데이터 로드" 필수 구성 요소를 검토합니다.

### <a name="2---start-import-data-wizard"></a>2 - 데이터 가져오기 마법사 시작

Azure Search 서비스 페이지의 명령 모음에서 또는 스토리지 계정의 왼쪽 탐색 창에 있는 **Blob service** 섹션에서 **Azure Search 추가**를 클릭하여 [마법사를 시작](search-import-data-portal.md)할 수 있습니다.

### <a name="3---set-the-data-source"></a>3 - 데이터 원본 설정

**데이터 원본** 페이지에서 원본은 다음 사양을 가진 **Azure Blob 스토리지**여야 합니다.

+ **추출할 데이터**는 *콘텐츠 및 메타데이터*여야 합니다. 이 옵션을 선택하면 마법사가 인덱스 스키마를 유추하고 가져올 필드를 매핑할 수 있습니다.
   
+ **구문 분석 모드**를 *JSON* 또는 *JSON 배열*로 설정해야 합니다. 

  *JSON*은 각 Blob을 단일 검색 문서로 구분하여 검색 결과에서 개별 항목으로 표시합니다. 

  *JSON 배열*은 여러 요소로 구성된 Blob용 배열로, 각 요소를 독립적인 검색 문서로 표시할 수 있습니다. Blob이 복잡하고 *JSON 배열*을 선택하지 않을 경우 전체 Blob이 단일 문서로 수집됩니다.
   
+ **스토리지 컨테이너**는 스토리지 계정 및 컨테이너 또는 컨테이너로 확인되는 연결 문자열을 지정해야 합니다. Blob service 포털 페이지에서 연결 문자열을 가져올 수 있습니다.

   ![Blob 데이터 원본 정의](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 마법사에서 "인식 검색 추가" 페이지 건너뛰기

JSON 문서 가져오기를 위해 반드시 인식 기술을 추가할 필요는 없습니다. 인덱싱 파이프라인에 [Cognitive Services API 및 변환을 포함](cognitive-search-concept-intro.md)할 구체적인 요구가 있지 않다면 이 단계는 건너뛰는 것이 좋습니다.

이 단계를 건너뛰려면 다음 페이지 **대상 인덱스 사용자 지정**을 클릭합니다.

### <a name="5---set-index-attributes"></a>5 - 인덱스 특성 설정

**인덱스** 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 메타데이터에 따라, 원본 데이터를 샘플링하여 기본 인덱스를 생성할 수 있습니다. 

기본값을 적용하면 각 문서를 고유하게 식별하기 위한 키 또는 문서 ID로 사용되는 필드(문자열로 캐스팅)를 선택할 뿐만 아니라 전체 텍스트 검색에 적합한 후보로서 결과 세트에서 검색 가능한 필드도 선택할 수 있습니다. Blob의 경우 `content` 필드는 검색 가능 콘텐츠의 가장 적합한 후보입니다.

기본값을 그대로 적용하거나 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) 및 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)에 대한 설명을 검토하여 초기 값을 재정의하거나 보완할 수 있습니다. 

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 이러한 필드를 편집할 수 없습니다.

   ![Blob 인덱스 정의](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 인덱서 만들기

완전히 지정된 마법사는 검색 서비스에서 세 개의 고유한 개체를 만듭니다. 데이터 원본 개체 및 인덱스 개체는 Azure Search 서비스에 명명된 리소스로 저장됩니다. 마지막 단계는 인덱서 개체를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

인덱서에 익숙하지 않은 경우 *인덱서*를 검색 가능 콘텐츠의 외부 데이터 원본을 탐색하는 Azure Search의 리소스로 간주할 수 있습니다. **데이터 가져오기** 마법사를 실행하면 JSON 데이터 원본을 탐색하고, 검색 가능한 콘텐츠를 추출하고, Azure Search의 인덱스로 가져오는 인덱서가 제공됩니다.

   ![Blob 인덱서 정의](media/search-howto-index-json/import-wizard-json-indexer.png)

**확인**을 클릭하여 마법사를 실행하고 모든 개체를 만듭니다. 인덱싱이 즉시 시작됩니다.

포털 페이지에서 데이터 가져오기를 모니터링할 수 있습니다. 진행률 알림은 인덱싱 상태 및 업로드된 문서 수를 나타냅니다. 인덱싱이 완료되면 [검색 탐색기](search-explorer.md)를 사용하여 인덱스를 쿼리할 수 있습니다.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API 사용

JSON Blob을 인덱싱하는 작업은 Azure Search에 있는 모든 인덱서에 공통되는 세 부분, 즉 데이터 원본 만들기, 인덱스 만들기, 인덱서 만들기로 구성된 워크플로의 일반 문서 추출 작업과 비슷합니다.

코드 기반 JSON 인덱싱의 경우 REST API와 함께 [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [인덱스](https://docs.microsoft.com/rest/api/searchservice/create-index)용 API를 사용할 수 있습니다. 포털 마법사와 달리, 코드 접근 방식에서는 **인덱서 만들기** 요청을 보낼 때 인덱스가 있고 JSON 문서를 수락할 준비가 되어 있어야 합니다.

Azure Blob Storage에서 JSON Blob은 일반적으로 단일 JSON 문서 또는 JSON 배열입니다. Azure Search에서 Blob 인덱서는 요청에 대한 **parsingMode** 매개 변수를 설정하는 방법에 따라 생성을 구문 분석할 수 있습니다.

| JSON 문서 | parsingMode | 설명 | 가용성 |
|--------------|-------------|--------------|--------------|
| Blob 당 하나 | `json` | JSON Blob을 텍스트의 단일 청크로 구문 분석합니다. 각 JSON Blob은 단일 Azure Search 문서가 됩니다. | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 및 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 모두에서 사용할 수 있습니다. |
| Blob 당 여러 개 | `jsonArray` | Blob에서 JSON 배열을 구문 분석합니다. 여기서 배열의 각 요소는 별도의 Azure Search 문서가 됩니다.  | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 및 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 모두에서 사용할 수 있습니다. |


### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

첫 번째 단계는 인덱서에서 사용하는 데이터 원본 연결 정보를 제공하는 것입니다. `azureblob`으로 지정된 데이터 원본 형식은 인덱서에 의해 호출되는 데이터 추출 동작을 결정합니다. JSON Blob 인덱싱의 경우 데이터 원본 정의는 JSON 문서 및 배열 모두에서 동일합니다. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>2단계: 대상 검색 인덱스 만들기 

인덱서는 인덱스 스키마과 쌍을 이룹니다. 포털이 아닌 API를 사용하는 경우 인덱서 작업에서 지정할 수 있도록 사전에 인덱스를 준비합니다.

인덱스는 Azure Search에 검색 가능한 콘텐츠를 저장합니다. 인덱스를 만들려면 문서의 필드, 특성 및 검색 환경을 형성하는 기타 항목을 지정하는 스키마를 제공합니다. 원본과 동일한 필드 이름 및 데이터 형식을 갖는 인덱스를 만드는 경우 인덱서는 사용자에게 명시적으로 필드를 매핑하도록 요구하지 않고, 원본 및 대상 필드를 일치시킵니다.

다음 예제는 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 요청을 보여 줍니다. 인덱스에는 blob에서 추출된 텍스트를 저장하기 위한 검색 가능한`content` 필드가 표시됩니다.   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>3단계: 인덱서 구성 및 실행

지금까지 데이터 원본 및 인덱스에 대한 정의는 parsingMode와 독립적이었습니다. 그러나 인덱서 구성 3단계의 경로는 Azure Search 인덱스에서 JSON Blob 콘텐츠를 구문 분석하고 구성하려는 방법에 따라 달라집니다. 옵션에는 `json` 또는 `jsonArray`가 포함됩니다.

+ 각 Blob을 단일 문서로 인덱싱하려면 **parsingMode**를 `json`으로 설정합니다.

+ Blob이 JSON 배열로 구성되어 있고 배열의 각 요소를 Azure Search에서 별도 문서로 나타내야 할 경우 **parsingMode**를 `jsonArray`로 설정합니다. 문서를 검색 결과의 단일 항목으로 생각할 수 있습니다. 배열의 각 요소를 검색 결과에 개별 항목으로 표시하려는 경우 `jsonArray` 옵션을 사용합니다.

인덱서 정의 내에서 **필드 매핑**을 사용하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택할 수도 있습니다. JSON 배열의 경우 하위 수준 속성인 배열이 있으면 Blob 내에서 배열의 위치를 나타내는 문서 루트를 설정할 수 있습니다.

> [!IMPORTANT]
> `json` 또는 `jsonArray` 구문 분석 모드를 사용하는 경우 Azure Search는 데이터 원본의 모든 blob이 JSON을 포함한다고 가정합니다. 동일한 데이터 원본에서 JSON 및 비 JSON BLOB을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.


### <a name="how-to-parse-single-json-blobs"></a>단일 JSON Blob을 구문 분석하는 방법

기본적으로 [Azure Search BLOB 인덱서](search-howto-indexing-azure-blob-storage.md) 는 단일 텍스트 청크로 JSON BLOB을 구문 분석합니다. JSON 문서의 구조를 유지하려는 경우가 많습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 인덱서는 JSON 문서를 단일 Azure Search 문서로 구문 분석합니다. 인덱서는 동일하게 명명되고 형식이 지정된 대상 인덱스 필드에 대해 원본의 "text", "datePublished" 및 "tags"를 일치시켜 인덱스를 로드합니다.

구성은 인덱서 작업의 본문에 제공됩니다. 이전에 정의된 데이터 원본 개체를 호출하고, 데이터 원본 형식과 연결 정보를 지정합니다. 서비스에서 빈 컨테이너인 대상 인덱스도 있어야 합니다. 일정 및 매개 변수는 선택 사항이지만 생략하는 경우 `json`을 구문 분석 모드로 사용하여 인덱서가 즉시 실행됩니다.

전체 지정된 요청은 다음과 같이 표시될 수 있습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

명시된 대로 필드 매핑이 필요하지 않습니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

### <a name="how-to-parse-json-arrays"></a>JSON 배열을 구문 분석하는 방법

또는 JSON 배열 기능을 선택할 수 있습니다. 이 기능은 Blob에 *JSON 개체 배열*이 포함되고 각 배열 요소를 별도의 Azure Search 문서로 나타내려고 하는 경우에 유용합니다. 예를 들어 다음 JSON Blob의 경우 각각 "id" 및 "text" 필드가 있는 별도의 3개 문서로 Azure Search 인덱스를 채울 수 있습니다.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다. parsingMode 매개 변수는 `jsonArray` 파서를 지정합니다. 올바른 파서를 지정하고 적절한 데이터를 입력하는 것이 JSON Blob 인덱싱을 위한 두 가지 배열 관련 요구 사항입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

다시 말하지만 필드 매핑은 생략할 수 있습니다. 동일하게 명명된 "id" 및 "text" 필드를 포함하는 인덱스가 있는 경우 Blob 인덱서는 명시적인 필드 매핑 목록 없이 올바른 매핑을 유추할 수 있습니다.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>중첩된 JSON 배열
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

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>필드 매핑을 사용하여 검색 문서 빌드

원본 및 대상 필드가 완벽하게 정렬되지 않은 경우 명시적 필드 간 연결을 위해 요청 본문에서 필드 매핑 섹션을 정의할 수 있습니다.

현재 Azure Search는 기본 데이터 형식, 문자열 배열 및 GeoJSON 포인트만 지원하므로 임의의 JSON 문서를 직접 인덱싱할 수 없습니다. 그러나 **필드 매핑** 을 사용하여 JSON 문서의 부분을 선택하고 검색 문서의 최상위 필드로 이 부분을 "올릴" 수 있습니다. 필드 매핑 기본 사항에 대한 자세한 내용은 [Azure Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

예제 JSON 문서 다시 방문:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

매핑의 원본 필드 이름은 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 필드 매핑 경로의 원본 필드 이름이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 다른 스키마를 사용하여 문서를 지원할 수 있도록 이 작업을 수행합니다(일반적인 사용 사례). 유효성을 검사하지 않기 때문에 필드 매핑 사양에 오타가 발생하지 않도록 주의해야 합니다.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST 예제: 필드 매핑을 포함한 인덱서 요청

필드 매핑을 비롯하여 완전히 지정된 인덱서 페이로드는 다음과 같습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK 사용

.NET SDK는 REST API와 완전히 동일한 기능을 제공합니다. 개념, 워크플로 및 요구 사항을 알아보려면 이전 REST API 섹션을 검토하는 것이 좋습니다. 그런 후, 다음 .NET API 참조 설명서를 참조하여 관리되는 코드에서 JSON 인덱서를 구현할 수 있습니다.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>참고 항목

+ [Azure Search의 인덱서](search-indexer-overview.md)
+ [Azure Search로 Azure Blob Storage 인덱싱](search-howto-index-json-blobs.md)
+ [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob Storage에서 반구조화된 데이터 검색](search-semi-structured-data.md)
