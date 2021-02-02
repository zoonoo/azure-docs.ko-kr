---
title: JSON blob 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Blob 인덱서를 사용 하 여 텍스트 콘텐츠에 대 한 Azure JSON blob을 탐색 합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259053"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search에서 Blob 인덱서를 사용 하 여 JSON blob을 인덱싱하는 방법

이 문서에서는 JSON 문서로 구성 된 blob에 대 한 [blob 인덱서를 구성](search-howto-indexing-azure-blob-storage.md) 하는 방법을 보여 줍니다. Azure Blob storage의 JSON blob은 일반적으로 다음과 같은 형식으로 가정 합니다.

+ 단일 JSON 문서
+ 올바른 형식의 JSON 요소 배열을 포함 하는 JSON 문서입니다.
+ 여러 엔터티를 포함 하는 JSON 문서는 줄 바꿈으로 구분 됩니다.

Blob 인덱서는 **`parsingMode`** 다음 옵션으로 구성 된 구조 구문 분석 모드에 따라 검색 문서의 출력을 최적화 하는 매개 변수를 제공 합니다.

| parsingMode | JSON 문서 | 설명 |
|--------------|-------------|--------------|
| **`json`** | Blob 당 하나 | 기본 JSON blob을 텍스트의 단일 청크로 구문 분석 합니다. 각 JSON blob은 단일 검색 문서가 됩니다. |
| **`jsonArray`** | Blob 당 여러 개 | Blob에서 JSON 배열을 구문 분석 합니다. 여기서 배열의 각 요소는 별도의 검색 문서가 됩니다.  |
| **`jsonLines`** | Blob 당 여러 개 | 개별 요소가 줄 바꿈으로 구분 된 여러 JSON 엔터티 (또한 배열)가 포함 된 blob을 구문 분석 합니다. 인덱서는 새 줄 마다 새 검색 문서를 시작 합니다. |

및 둘 다에 대해 **`jsonArray`** **`jsonLines`** [하나의 blob 인덱싱을 검토 하 여 여러 검색 문서를 생성 하](search-howto-index-one-to-many-blobs.md) 여 blob 인덱서가 동일한 blob에서 생성 된 여러 검색 문서에 대 한 문서 키의 명확성을 처리 하는 방법을 이해 해야 합니다.

인덱서 정의 내에서 필요에 따라 [필드 매핑을](search-indexer-field-mappings.md) 설정 하 여 대상 검색 인덱스를 채우는 데 사용 되는 원본 JSON 문서의 속성을 선택할 수 있습니다. 예를 들어 **`jsonArray`** 구문 분석 모드를 사용할 때 배열이 하위 수준 속성으로 존재 하는 경우 **`document root`** blob 내에서 배열이 배치 되는 위치를 나타내는 속성을 설정할 수 있습니다.

다음 섹션에서는 각 모드에 대해 자세히 설명 합니다. 인덱서 클라이언트 및 개념에 대해 잘 모르는 경우 [검색 인덱서 만들기](search-howto-create-indexers.md)를 참조 하세요. 여기에서는 반복 되지 않는 [기본 blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)의 세부 정보에 대해서도 잘 알고 있어야 합니다.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>단일 JSON 문서 (blob 당 하나)를 인덱싱합니다.

기본적으로 blob 인덱서는 단일 텍스트 청크로 JSON blob을 구문 분석 하 고, 컨테이너의 각 blob에 대 한 검색 문서 하나를 검색 합니다. JSON이 구조화 된 경우 검색 문서는 개별 요소가 개별 필드로 표시 된 해당 구조를 반영할 수 있습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Blob 인덱서는 JSON 문서를 단일 검색 문서로 구문 분석 하 고, "text", "datePublished" 및 "tags"를 일치 하 여 인덱스를 로드 하 여 동일한 이름의 및 형식화 된 대상 인덱스 필드에 대해 원본에서 로드 합니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

기본 동작은 JSON blob 당 하나의 검색 문서 이지만 ' json ' 구문 분석 모드를 설정 하면 내용에 대 한 내부 필드 매핑이 변경 되 고 내부 필드가 `content` 검색 인덱스의 실제 필드로 승격 됩니다. 구문 분석 모드의 예제 인덱서 정의는 **`json`** 다음과 같습니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> 모든 인덱서와 마찬가지로 필드가 명확 하 게 일치 하지 않으면 [기본 blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)에 설명 된 대로 blob 콘텐츠 및 메타 데이터에 사용할 수 있는 암시적 필드 매핑을 사용 하지 않는 한 개별 [필드 매핑을](search-indexer-field-mappings.md) 명시적으로 지정 해야 합니다.

### <a name="json-example-single-hotel-json-files"></a>json 예제 (단일 호텔 JSON 파일)

GitHub의 [호텔 json 문서 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) 은 json 구문 분석을 테스트 하는 데 유용 합니다. 여기서 각 blob은 구조화 된 json 파일을 나타냅니다. 데이터 파일을 Blob 저장소에 업로드 하 고 **데이터 가져오기** 마법사를 사용 하 여이 콘텐츠를 개별 검색 문서로 구문 분석 하는 방법을 신속 하 게 평가할 수 있습니다. 

데이터 집합은 5 개의 blob로 구성 되며, 각 blob에는 주소 컬렉션과 방 컬렉션이 포함 된 호텔 문서가 포함 됩니다. Blob 인덱서는 두 컬렉션을 모두 검색 하 고 인덱스 스키마의 입력 문서 구조를 반영 합니다.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON 배열 구문 분석

또는 JSON 배열 옵션을 사용할 수 있습니다. 이 옵션은 blob이 잘 구성 된 JSON 개체 배열을 포함 하 고 각 요소가 별도의 검색 문서가 되도록 하려는 경우에 유용 합니다. **`jsonArrays`** 다음 JSON blob은를 사용 하 여 각각 및 필드가 있는 세 개의 개별 문서를 생성 합니다 `"id"` `"text"` .  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** 인덱서의 속성은 구문 분석 모드 값을 포함 합니다. JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays 예제 (임상 평가판 샘플 데이터)

GitHub의 [임상 평가판 json 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) 은 json 배열 구문 분석을 테스트 하는 데 유용 합니다. 데이터 파일을 Blob 저장소에 업로드 하 고 **데이터 가져오기** 마법사를 사용 하 여이 콘텐츠를 개별 검색 문서로 구문 분석 하는 방법을 신속 하 게 평가할 수 있습니다. 

데이터 집합은 총 100 개의 엔터티에 대해 엔터티의 JSON 배열을 포함 하는 8 개의 blob으로 구성 됩니다. 엔터티는 채워지는 필드에 따라 다르지만, 최종 결과는 모든 blob의 모든 배열에서 엔터티 마다 하나의 검색 문서입니다.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>중첩 된 JSON 배열 구문 분석

중첩 된 요소가 있는 JSON 배열의 경우를 지정 하 여 **`documentRoot`** 다중 수준 구조를 나타낼 수 있습니다. 예를 들어 blob은 다음과 같습니다.

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>줄바꿈로 구분 된 JSON 엔터티 구문 분석

Blob이 줄 바꿈으로 구분 된 여러 JSON 엔터티를 포함 하 고 각 요소가 별도의 검색 문서가 되도록 하려면를 사용 **`jsonLines`** 합니다.

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

JSON 줄의 경우 인덱서 정의는 다음 예제와 유사 하 게 표시 됩니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines 예제 (caselaw 샘플 데이터)

GitHub의 [CASELAW json 데이터 집합](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) 은 json 새 줄 구문 분석을 테스트 하는 데 유용 합니다. 다른 샘플과 마찬가지로이 데이터를 Blob 저장소에 업로드 하 고 **데이터 가져오기** 마법사를 사용 하 여 개별 blob에 대 한 구문 분석 모드의 영향을 신속 하 게 평가할 수 있습니다.

데이터 집합은 새 줄로 구분 된 10 개의 JSON 엔터티를 포함 하는 하나의 blob로 구성 되며, 각 엔터티는 단일 법률 사례를 설명 합니다. 최종 결과는 엔터티 당 하나의 검색 문서입니다.

## <a name="map-json-fields-to-search-fields"></a>JSON 필드를 검색 필드에 매핑

필드 매핑은 필드 이름과 형식이 동일 하지 않은 경우 원본 필드를 대상 필드와 연결 하는 데 사용 됩니다. 그러나 필드 매핑을 사용 하 여 JSON 문서의 일부를 일치 시키고 검색 문서의 최상위 필드로 "리프트" 할 수도 있습니다.

다음 예제에서는이 시나리오를 보여 줍니다. 필드 매핑에 대 한 일반적인 정보는 [필드 매핑](search-indexer-field-mappings.md)을 참조 하세요.

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

원본 필드는 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 표기법을 사용 하 여 지정 됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> **`sourceFieldName`** 가 JSON blob에 없는 속성을 참조 하는 경우에는 오류 없이 해당 매핑을 건너뜁니다. 이 동작을 사용 하면 다른 스키마 (일반적인 사용 사례)를 포함 하는 JSON blob에 대 한 인덱싱을 계속할 수 있습니다. 유효성 검사가 수행 되지 않기 때문에 잘못 된 이유로 문서를 잃지 않도록 매핑을 신중 하 게 확인 해야 합니다.
>

## <a name="next-steps"></a>다음 단계

+ [Blob 인덱서 구성](search-howto-indexing-azure-blob-storage.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 개요](search-indexer-overview.md)
+ [Blob 인덱서를 사용 하 여 CSV blob을 인덱싱하는 방법](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob storage에서 반 구조화 된 데이터 검색](search-semi-structured-data.md)