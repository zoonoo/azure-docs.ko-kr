---
title: 인덱서를 사용한 자동 인덱싱을 위한 필드 매핑 - Azure Search
description: 필드 이름 및 데이터 표현의 차이를 처리하도록 Azure Search 인덱서 필드 매핑을 구성합니다.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147801"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>필드 매핑 및 Azure Search 인덱서를 사용 하 여 변환

Azure Search 인덱서를 사용할 때는 경우가 종종 있습니다 입력된 데이터가 대상 인덱스의 스키마와 일치 상당히 하지 않습니다. 이러한 경우에 사용할 수 있습니다 **필드 매핑** 인덱싱 프로세스 중에 데이터 모양을 변경 합니다.

필드 매핑이 유용한 일부 상황:

* 데이터 원본 이라는 필드가 `_id`, 있지만 Azure Search 필드 이름이 밑줄로 시작 하는 사용할 수 없습니다. 필드 매핑을 사용 하면 효과적으로 필드 이름을 바꿀 수 있습니다.
* 동일한 데이터 원본에서에서 인덱스에 여러 필드를 입력 해야 합니다. 예를 들어, 다음 해당 필드에 다른 분석기를 적용 하는 것이 좋습니다.
* 둘 이상의 데이터 원본에서 데이터를 사용 하 여 인덱스 필드를 채 우 시겠습니까 및 각 데이터 원본에 다른 필드 이름을 사용 합니다.
* 데이터를 Base64 인코딩 또는 디코딩해야 합니다. 필드 매핑은 Base64 인코딩 및 디코딩에 대한 함수를 포함한 여러 **매핑 함수**를 지원합니다.

> [!NOTE]
> Azure Search 인덱서의 필드 매핑 기능에는 데이터 변환에 대 한 몇 가지 옵션을 사용 하 여 인덱스 필드에 데이터 필드를 매핑할 수 있는 간단한 방법을 제공 합니다. 더 복잡 한 데이터 사전 처리 인덱스 하기 쉬운 형태로 변형에 필요할 수 있습니다.
>
> Microsoft Azure Data Factory가를 가져오고 데이터를 변환 하기 위한 강력한 클라우드 기반 솔루션입니다. 또한 인덱싱 전에 원본 데이터를 변환 하는 코드를 작성할 수 있습니다. 코드 예제를 보려면 [관계형 데이터 모델링](search-example-adventureworks-modeling.md) 하 고 [다중 레벨 패싯 모델](search-example-adventureworks-multilevel-faceting.md)합니다.
>

## <a name="set-up-field-mappings"></a>필드 매핑 설정

필드 매핑은 다음 세 부분으로 구성됩니다.

1. `sourceFieldName`은(는) 데이터 원본의 필드를 나타냅니다. 이 속성은 필수입니다.
2. 선택 사항 `targetFieldName`은(는) 검색 인덱스의 필드를 나타냅니다. 생략된 경우 데이터 원본과 동일한 이름이 사용됩니다.
3. 선택 사항 `mappingFunction`은(는) 미리 정의된 여러 함수 중 하나를 사용하여 데이터를 변환할 수 있습니다. 함수의 전체 목록은 [아래](#mappingFunctions)입니다.

필드 매핑을 추가할를 `fieldMappings` 인덱서 정의의 배열입니다.

## <a name="map-fields-using-the-rest-api"></a>REST API를 사용 하 여 필드 매핑

사용 하 여 새 인덱서를 만들 때 필드 매핑을 추가할 수는 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API 요청 합니다. 필드 매핑을 사용 하 여 기존 인덱서를 관리할 수 있습니다 합니다 [인덱서 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API 요청 합니다.

예를 들어 다음과 같습니다. 다른 이름 사용 하 여 대상 필드에 원본 필드를 매핑하는 방법

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

여러 필드 매핑의 원본 필드를 참조할 수 있습니다. 다음 예제에서는 "분기" 필드를 두 개의 다른 인덱스 필드에 동일한 원본 필드를 복사 하는 방법을 보여 줍니다.

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search는 대/소문자 구분 비교를 사용하여 필드 매핑의 필드 및 함수 이름을 확인합니다. 이는 편리(모든 대/소문자를 올바르게 할 필요가 없음)하지만 데이터 원본 또는 인덱스가 대/소문자만으로 다른 필드를 가질 수 없음을 의미합니다.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>.NET SDK를 사용 하 여 필드 매핑

필드 매핑을 사용 하 여.NET SDK 정의 [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) 속성이 있는 클래스 `SourceFieldName` 하 고 `TargetFieldName`, 및 선택적 `MappingFunction` 참조 합니다.

인덱서를 생성할 때 또는 나중에 직접 설정 하 여 필드 매핑을 지정할 수 있습니다는 `Indexer.FieldMappings` 속성입니다.

다음 C# 인덱서를 생성할 때 필드 매핑을 설정 하는 예제입니다.

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

필드 매핑 함수는 인덱스에 저장 되기 전에 필드의 콘텐츠를 변환 합니다. 다음 매핑 함수는 현재 지원 됩니다.

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64encode 일 함수

입력된 문자열의 *URL 안전* Base64 인코딩을 수행합니다. 입력이 UTF-8 인코딩되었다고 가정합니다.

#### <a name="example---document-key-lookup"></a>예제-문서 키 조회

URL 지원 문자 Azure Search 문서 키에 나타날 수 있습니다 (고객이 사용 하 여 문서를 처리할 수 있어야 하므로 합니다 [조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). URL 안전 하지 않은 문자를 포함 하는 키에 대 한 원본 필드를 사용할 수 있습니다는 `base64Encode` 인덱싱할 때 변환 하는 함수입니다.

검색 시 인코딩된 키를 검색할 때 사용할 수는 `base64Decode` 원래 키 값을 가져오려면 함수를 사용 하는 원본 문서를 검색 합니다.

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

매핑 함수에 대 한 매개 변수 속성을 포함 하지 않으면, 기본값은 값 `{"useHttpServerUtilityUrlTokenEncode" : true}`합니다.

Azure Search에는 두 개의 서로 다른 Base64 인코딩을 지원합니다. 인코딩 및 디코딩과 같은 필드를 하는 경우 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 [base64 인코딩 옵션](#base64details) 사용 하는 매개 변수를 결정 합니다.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 함수

입력 문자열의 Base64 디코딩을 수행합니다. 입력으로 간주 됩니다는 *URL 로부터 안전한* Base64로 인코딩된 문자열입니다.

#### <a name="example---decode-blob-metadata-or-urls"></a>예제-blob 메타 데이터 또는 Url 디코딩

원본 데이터가 blob 메타 데이터 문자열 등 일반 텍스트로 검색 가능한 확인 하려는 웹 Url, Base64로 인코딩된 문자열을 포함할 수 있습니다. 사용할 수는 `base64Decode` 검색 인덱스를 채울 때 일반 문자열로 인코딩된 데이터를 설정 하는 함수입니다.

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

매개 변수 속성을 포함 하지 않으면 기본값은 값 `{"useHttpServerUtilityUrlTokenEncode" : true}`합니다.

Azure Search에는 두 개의 서로 다른 Base64 인코딩을 지원합니다. 인코딩 및 디코딩과 같은 필드를 하는 경우 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 참조 하세요. [base64 인코딩 옵션](#base64details) 사용 하는 매개 변수를 결정 합니다.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 인코딩 옵션

Azure Search에서는 두 개의 서로 다른 Base64 인코딩을 지원합니다. **HttpServerUtility URL 토큰**, 및 **패딩 URL 안전 Base64 인코딩을**합니다. 동일한 인코딩 옵션을 사용 하 여 나중에 디코딩해야 하는 base64로 인코딩된 인덱싱하는 동안 문자열. 그렇지 않으면 결과 원래 일치 하지 않습니다.

경우는 `useHttpServerUtilityUrlTokenEncode` 또는 `useHttpServerUtilityUrlTokenDecode` 인코딩 및 디코딩에 각각에 대 한 매개 변수를 설정 `true`, 한 다음 `base64Encode` 처럼 [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) 및 `base64Decode` 처럼 [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)합니다.

전체.NET Framework를 사용 하지 않는 경우 (즉, 사용 하는.NET Core 또는 다른 프레임 워크) Azure Search의 동작을 에뮬레이트해야 키 값을 생성 하려면 다음 설정 해야 `useHttpServerUtilityUrlTokenEncode` 하 고 `useHttpServerUtilityUrlTokenDecode` 에 `false`입니다. 사용 하는 라이브러리에 따라 base64 인코딩 및 디코딩 함수는 Azure Search에서 사용 하는 것과 달라질 수 있습니다.

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

이 함수는 다음 매개 변수를 사용합니다.

* `delimiter`: 입력 문자열을 분할할 때 구분 기호로 사용할 문자열입니다.
* `position`: 입력 문자열을 분할한 후 선택할 정수 0부터 시작하는 토큰의 위치입니다.

예를 들어 입력은 `Jane Doe`, `delimiter`는 `" "`(공백), `position`은 0인 경우 결과는 `Jane`입니다. `position`이 1이면 결과는 `Doe`입니다. 위치가 없는 토큰을 참조하는 경우 오류가 반환됩니다.

#### <a name="example---extract-a-name"></a>예제-이름을 추출

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

#### <a name="example---populate-collection-from-relational-data"></a>예-관계형 데이터에서 컬렉션을 채우는

Azure SQL Database에 자연스럽 게 매핑하는 기본 제공 데이터 형식 없는 `Collection(Edm.String)` Azure Search의 필드입니다. 문자열 컬렉션 필드를 채우려면 JSON 문자열 배열로 원본 데이터를 사전 처리를 사용 하 여는 `jsonArrayToStringCollection` 매핑 함수입니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

인덱스 컬렉션 필드를 관계형 데이터를 변환 하는 자세한 예제를 참조 하세요 [관계형 데이터 모델링](search-example-adventureworks-modeling.md)합니다.
