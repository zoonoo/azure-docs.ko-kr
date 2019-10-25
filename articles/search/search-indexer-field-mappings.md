---
title: 인덱서를 사용 하 여 자동화 된 인덱싱에 대 한 필드 매핑
titleSuffix: Azure Cognitive Search
description: 필드 이름과 데이터 표현의 차이를 고려 하 여 인덱서에 필드 매핑을 구성 합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc863ee3dc7f2dc8049fcd22189acac94a855352
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786966"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Cognitive Search 인덱서를 사용 하 여 필드 매핑 및 변환

Azure Cognitive Search 인덱서를 사용 하는 경우 입력 데이터가 대상 인덱스의 스키마와 일치 하지 않는 경우가 종종 있습니다. 이러한 경우 인덱싱 프로세스 중에 **필드 매핑을** 사용 하 여 데이터의 모양을 변경할 수 있습니다.

필드 매핑이 유용한 일부 상황:

* 데이터 원본에 `_id`이라는 필드가 있지만 Azure Cognitive Search에서 이름에 밑줄로 시작 하는 필드 이름을 사용할 수 없습니다. 필드 매핑을 사용 하면 필드를 효과적으로 바꿀 수 있습니다.
* 동일한 데이터 원본 데이터에서 인덱스의 여러 필드를 채워야 합니다. 예를 들어 이러한 필드에 다른 분석기를 적용 하는 것이 좋습니다.
* 두 개 이상의 데이터 원본에 있는 데이터를 사용 하 여 인덱스 필드를 채우면 서로 다른 필드 이름을 사용 합니다.
* 데이터를 Base64 인코딩 또는 디코딩해야 합니다. 필드 매핑은 Base64 인코딩 및 디코딩에 대한 함수를 포함한 여러 **매핑 함수**를 지원합니다.

> [!NOTE]
> Azure Cognitive Search 인덱서의 필드 매핑 기능은 데이터를 변환 하기 위한 몇 가지 옵션을 포함 하 여 데이터 필드를 인덱스 필드에 매핑하는 간단한 방법을 제공 합니다. 더 복잡 한 데이터는 인덱싱 하기 쉬운 형식으로 변형 하기 위해 전처리가 필요할 수 있습니다.
>
> Microsoft Azure Data Factory는 데이터를 가져오고 변환 하기 위한 강력한 클라우드 기반 솔루션입니다. 인덱싱 전에 소스 데이터를 변환 하는 코드를 작성할 수도 있습니다. 코드 예제는 [모델 관계형 데이터](search-example-adventureworks-modeling.md) 및 [모델 다단계 패싯](search-example-adventureworks-multilevel-faceting.md)을 참조 하세요.
>

## <a name="set-up-field-mappings"></a>필드 매핑 설정

필드 매핑은 다음 세 부분으로 구성됩니다.

1. `sourceFieldName`은(는) 데이터 원본의 필드를 나타냅니다. 이 속성은 필수입니다.
2. 선택 사항 `targetFieldName`은(는) 검색 인덱스의 필드를 나타냅니다. 생략된 경우 데이터 원본과 동일한 이름이 사용됩니다.
3. 선택 사항 `mappingFunction`은(는) 미리 정의된 여러 함수 중 하나를 사용하여 데이터를 변환할 수 있습니다. 함수의 전체 목록은 [아래](#mappingFunctions)입니다.

필드 매핑은 인덱서 정의의 `fieldMappings` 배열에 추가 됩니다.

## <a name="map-fields-using-the-rest-api"></a>REST API를 사용 하 여 필드 매핑

[인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API 요청을 사용 하 여 새 인덱서를 만들 때 필드 매핑을 추가할 수 있습니다. [Update 인덱서](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API 요청을 사용 하 여 기존 인덱서의 필드 매핑을 관리할 수 있습니다.

예를 들어 소스 필드를 다른 이름으로 대상 필드에 매핑하는 방법은 다음과 같습니다.

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

원본 필드는 여러 필드 매핑에서 참조할 수 있습니다. 다음 예에서는 필드를 "포크" 하 여 동일한 원본 필드를 두 개의 서로 다른 인덱스 필드로 복사 하는 방법을 보여 줍니다.

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search는 대/소문자를 구분 하지 않는 비교를 사용 하 여 필드 매핑의 필드 및 함수 이름을 확인 합니다. 이는 편리(모든 대/소문자를 올바르게 할 필요가 없음)하지만 데이터 원본 또는 인덱스가 대/소문자만으로 다른 필드를 가질 수 없음을 의미합니다.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>.NET SDK를 사용 하 여 필드 매핑

`SourceFieldName` 및 `TargetFieldName`속성과 선택적 `MappingFunction` 참조를 포함 하는 [Fieldmapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) 클래스를 사용 하 여 .net SDK에서 필드 매핑을 정의 합니다.

인덱서를 생성할 때 또는 나중에 `Indexer.FieldMappings` 속성을 직접 설정 하 여 필드 매핑을 지정할 수 있습니다.

다음 C# 예에서는 인덱서를 생성할 때 필드 매핑을 설정 합니다.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>필드 매핑 함수

필드 매핑 함수는 인덱스에 저장 되기 전에 필드의 내용을 변환 합니다. 현재 지원 되는 매핑 함수는 다음과 같습니다.

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 함수

입력된 문자열의 *URL 안전* Base64 인코딩을 수행합니다. 입력이 UTF-8 인코딩되었다고 가정합니다.

#### <a name="example---document-key-lookup"></a>예제-문서 키 조회

고객이 [조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 를 사용 하 여 문서를 처리할 수 있어야 하기 때문에 URL 안전 문자만 Azure Cognitive Search 문서 키에 나타날 수 있습니다. 키의 원본 필드에 URL-안전 하지 않은 문자가 포함 된 경우 `base64Encode` 함수를 사용 하 여 인덱싱할 때 변환할 수 있습니다.

검색 시 인코딩된 키를 검색 하는 경우 `base64Decode` 함수를 사용 하 여 원래 키 값을 가져온 다음이를 사용 하 여 소스 문서를 검색할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

매핑 함수에 대 한 매개 변수 속성을 포함 하지 않는 경우 기본적으로 `{"useHttpServerUtilityUrlTokenEncode" : true}`값으로 설정 됩니다.

Azure Cognitive Search는 두 개의 다른 Base64 인코딩을 지원 합니다. 동일한 필드를 인코딩 및 디코딩할 때 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 사용할 매개 변수를 결정 하는 [base64 인코딩 옵션](#base64details) 을 참조 하세요.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 함수

입력 문자열의 Base64 디코딩을 수행합니다. 입력은 *URL 안전* Base64 인코딩된 문자열로 간주 됩니다.

#### <a name="example---decode-blob-metadata-or-urls"></a>예제-blob 메타 데이터 또는 Url 디코딩

원본 데이터에는 일반 텍스트로 검색 하려는 blob 메타 데이터 문자열 또는 웹 Url과 같은 Base64 인코딩 문자열이 포함 될 수 있습니다. `base64Decode` 함수를 사용 하 여 검색 인덱스를 채울 때 인코딩된 데이터를 일반 문자열로 다시 전환할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

매개 변수 속성을 포함 하지 않는 경우 기본적으로 `{"useHttpServerUtilityUrlTokenEncode" : true}`값으로 설정 됩니다.

Azure Cognitive Search는 두 개의 다른 Base64 인코딩을 지원 합니다. 동일한 필드를 인코딩 및 디코딩할 때 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 사용할 매개 변수를 결정 하는 [base64 인코딩 옵션](#base64details) 을 참조 하세요.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 인코딩 옵션

Azure Cognitive Search는 두 가지 다른 Base64 인코딩, 즉 **HTTPSERVERUTILITY url 토큰**및 **패딩 없이 url 안전 base64 인코딩을**지원 합니다. 인덱싱을 수행 하는 동안 base64로 인코딩된 문자열은 나중에 동일한 인코딩 옵션을 사용 하 여 디코딩해야 합니다. 그렇지 않으면 결과가 원본과 일치 하지 않습니다.

인코딩 및 디코딩에 대 한 `useHttpServerUtilityUrlTokenEncode` 또는 `useHttpServerUtilityUrlTokenDecode` 매개 변수가 각각 `true`로 설정 된 경우 `base64Encode`는 `base64Decode` [HttpServerUtility](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) 와 같이 [동작 합니다.](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)

전체 .NET Framework (즉, .NET Core 또는 다른 프레임 워크를 사용 하 여)를 사용 하지 않는 경우 Azure Cognitive Search 동작을 에뮬레이트하는 키 값을 생성 하려면 `useHttpServerUtilityUrlTokenEncode`를 설정 하 고 `useHttpServerUtilityUrlTokenDecode`를 `false`으로 설정 해야 합니다. 사용 하는 라이브러리에 따라 base64 인코딩 및 디코딩 함수는 Azure Cognitive Search에서 사용 하는 것과 다를 수 있습니다.

다음 표에서는 문자열 `00>00?00`의 서로 다른 base64 인코딩을 비교합니다. base64 함수에 필요한 추가 처리를 판단하려면(있는 경우) `00>00?00` 문자열에서 라이브러리 인코딩 함수를 적용하고 출력을 `MDA-MDA_MDA` 예상 출력과 비교합니다.

| Encoding | Base64 인코딩 출력 | 라이브러리 인코딩 후 추가 처리 | 라이브러리 인코딩 전 추가 처리 |
| --- | --- | --- | --- |
| Base64(패딩 있음) | `MDA+MDA/MDA=` | URL 지원 문자 사용 및 패딩 제거 | 표준 base64 문자 사용 및 패딩 추가 |
| Base64(패딩 없음) | `MDA+MDA/MDA` | URL 지원 문자 사용 | 표준 base64 문자 사용 |
| URL 지원 Base64(패딩 있음) | `MDA-MDA_MDA=` | 패딩 제거 | 패딩 추가 |
| URL 지원 Base64(패딩 없음) | `MDA-MDA_MDA` | 없음 | 없음 |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 함수

지정된 구분 기호를 사용하여 문자열 필드를 분할하고 결과 분할의 지정된 위치에서 토큰을 선택합니다.

이 함수는 다음 매개 변수를 사용 합니다.

* `delimiter`: 입력 문자열을 분할할 때 구분 기호로 사용할 문자열입니다.
* `position`: 입력 문자열을 분할한 후 선택할 정수 0부터 시작하는 토큰의 위치입니다.

예를 들어 입력은 `Jane Doe`, `delimiter`는 `" "`(공백), `position`은 0인 경우 결과는 `Jane`입니다. `position`이 1이면 결과는 `Doe`입니다. 위치가 없는 토큰을 참조하는 경우 오류가 반환됩니다.

#### <a name="example---extract-a-name"></a>예-이름 추출

데이터 원본이 `PersonName` 필드를 포함하고 두 개의 개별 `FirstName` 및 `LastName` 필드로 인덱싱하려고 합니다. 구분 기호로 공백 문자를 사용하여 입력을 분할하는 데 이 함수를 사용할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 함수

JSON 문자열 배열 형식으로 생성된 문자열을 인덱스의 `Collection(Edm.String)` 필드를 채우는 데 사용할 수 있는 문자열 배열로 변환합니다.

예를 들어 입력 문자열이 `["red", "white", "blue"]`이면 `Collection(Edm.String)` 형식의 대상 필드는 세 개의 값 `red`, `white` 및 `blue`로 채워집니다. JSON 문자열 배열로 구문 분석할 수 없는 입력 값의 경우 오류가 반환됩니다.

#### <a name="example---populate-collection-from-relational-data"></a>예-관계형 데이터에서 컬렉션 채우기

Azure SQL Database는 기본적으로 Azure Cognitive Search의 `Collection(Edm.String)` 필드에 매핑되는 기본 제공 데이터 형식이 없습니다. 문자열 컬렉션 필드를 채우기 위해 원본 데이터를 JSON 문자열 배열로 미리 처리 한 다음 `jsonArrayToStringCollection` 매핑 함수를 사용할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

관계형 데이터를 인덱스 컬렉션 필드로 변환 하는 자세한 예제는 [모델 관계형 데이터](search-example-adventureworks-modeling.md)를 참조 하세요.

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 함수

이 함수를 사용 하 여 문자열을 "URL safe"로 인코딩할 수 있습니다. URL에 허용 되지 않는 문자가 포함 된 문자열을 사용 하는 경우이 함수는 "unsafe" 문자를 해당 하는 문자 엔터티로 변환 합니다. 이 함수는 UTF-8 인코딩 형식을 사용 합니다.

#### <a name="example---document-key-lookup"></a>예제-문서 키 조회

`urlEncode` 함수를 `base64Encode` 함수 대신 사용할 수 있습니다 .이 경우에는 URL로 안전 하지 않은 문자만 변환할 수 있지만 다른 문자는 그대로 유지 해야 합니다.

예를 들어 입력 문자열이 `<hello>` 된 다음 `(Edm.String)` 형식의 대상 필드가 값으로 채워집니다 `%3chello%3e`

검색 시 인코딩된 키를 검색 하는 경우 `urlDecode` 함수를 사용 하 여 원래 키 값을 가져온 다음이를 사용 하 여 소스 문서를 검색할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode 함수

 이 함수는 UTF-8 인코딩 형식을 사용 하 여 URL로 인코딩된 문자열을 디코딩된 문자열로 변환 합니다.

 ### <a name="example---decode-blob-metadata"></a>예제-blob 메타 데이터 디코딩

 일부 Azure storage 클라이언트는 ASCII가 아닌 문자를 포함 하는 경우 blob 메타 데이터를 자동으로 인코딩합니다. 그러나 이러한 메타 데이터를 일반 텍스트로 검색 가능 하 게 설정 하려는 경우 `urlDecode` 함수를 사용 하 여 검색 인덱스를 채울 때 인코딩된 데이터를 일반 문자열로 다시 전환할 수 있습니다.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```