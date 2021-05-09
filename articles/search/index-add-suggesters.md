---
title: 제안기 만들기
titleSuffix: Azure Cognitive Search
description: 제안기를 만들고 자동 완성 또는 자동 제안 쿼리 용어를 호출하는 요청을 구성하여 Azure Cognitive Search에서 자동 완성 쿼리 작업을 사용하도록 설정합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a03ca4bcad9bb577db68e2728ff9dbebb5779a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626829"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>쿼리에서 자동 완성 및 제안된 결과를 사용하도록 설정하는 제안기 만들기

Azure Cognitive Search에서는 *제안기* 를 통해 자동 완성 또는 “입력 시 검색”을 사용할 수 있습니다. 제안기는 추가 토큰화를 수행하는 필드 목록을 제공하여 부분 용어에 대한 일치를 지원하는 접두사 시퀀스를 생성합니다. 예를 들어, 값이 “Seattle”인 City 필드를 포함하는 제안기에는 자동 완성 기능을 지원하는 “sea”, “seat”, “seatt”및 “seattl”의 접두사 조합이 포함되어 있습니다.

부분 용어에 대한 일치는 자동 완성 쿼리 또는 제안된 일치일 수 있습니다. 같은 제안기가 두 환경을 모두 지원합니다.

## <a name="typeahead-experiences-in-cognitive-search"></a>Cognitive Search의 자동 완성 기능

자동 완성 기능은 전체 용어 쿼리에 대한 부분 입력을 완료하는 *자동 완성* 이거나 특정 일치 항목으로 클릭을 유도하는 *제안* 일 수 있습니다. 자동 완성은 쿼리를 생성합니다. 제안은 일치하는 문서를 생성합니다.

[C#으로 첫 번째 앱 만들기](tutorial-csharp-type-ahead-and-suggestions.md)의 다음 스크린샷은 두 가지 모두를 보여 줍니다. 자동 완성은 잠재적인 용어를 예상하고 “tw”를 “in”으로 끝냅니다. 제안은 소규모 검색 결과이며, 호텔 이름과 같은 필드는 인덱스에서 일치하는 호텔 검색 문서를 나타냅니다. 제안을 위해 설명 정보를 제공하는 모든 필드를 표시할 수 있습니다.

![자동 완성 및 제안된 쿼리를 시각적으로 비교](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "자동 완성 및 제안된 쿼리를 시각적으로 비교")

이 기능을 개별적으로 사용하거나 함께 사용할 수 있습니다. Azure Cognitive Search에서 이 동작을 구현하는 인덱스와 쿼리 구성 요소가 있습니다. 

+ 검색 인덱스 정의에 제안기를 추가합니다. 이 문서의 나머지 부분에서는 제안기를 만드는 데 중점을 둡니다.

+ [아래에 나열된 API](#how-to-use-a-suggester) 중 하나를 사용하여 제안 요청 또는 자동 완성 요청의 형식으로 제안기 사용 쿼리를 호출합니다.

입력 시 검색 지원은 문자열 필드의 필드별로 사용하게 설정됩니다. 스크린샷에 표시된 것과 비슷한 환경을 원하는 경우 같은 검색 솔루션에서 두 자동 완성 기능을 모두 구현할 수 있습니다. 두 요청 모두 특정 인덱스의 *문서* 컬렉션을 대상으로 하며 사용자가 3자 이상의 입력 문자열을 제공한 후 응답이 반환됩니다.

## <a name="how-to-create-a-suggester"></a>제안기를 만드는 방법

제안기를 만들려면 [인덱스 정의](/rest/api/searchservice/create-index)에 하나를 추가합니다. 제안기는 자동 완성 기능이 사용으로 설정된 이름과 필드 컬렉션을 사용합니다. 제안기를 만드는 데 가장 좋은 시기는 제안기를 사용할 필드도 정의할 때입니다.

+ 문자열 필드만 사용합니다.

+ 문자열 필드가 복합 형식(예: Address의 City 필드)의 일부인 경우 다음과 같이 필드 경로에 상위를 포함합니다. `"Address/City"`(REST, C# 및 Python) 또는 `["Address"]["City"]`(JavaScript).

+ 필드에서 기본 표준 Lucene 분석기(`"analyzer": null`) 또는 [언어 분석기](index-add-language-analyzers.md)(예: `"analyzer": "en.Microsoft"`)를 사용합니다.

기존 필드를 사용하여 제안기를 만들려는 경우 API에서 허용하지 않습니다. 두 개 이상의 문자 조합의 부분 용어가 전체 용어와 함께 토큰화되는 경우 인덱싱 중에 접두사가 생성됩니다. 기존 필드가 이미 토큰화되어 있기 때문에 제안기에 추가하려면 인덱스를 다시 빌드해야 합니다. 자세한 내용은 [Azure Cognitive Search 인덱스를 다시 빌드하는 방법](search-howto-reindex.md)을 참조하세요.

### <a name="choose-fields"></a>필드 선택

제안기에는 여러 속성이 있지만, 제안기는 기본적으로 입력 시 검색 기능을 사용하도록 설정되는 문자열 필드의 컬렉션입니다. 인덱스마다 하나의 제안기가 있기 때문에 제안기 목록에는 제안과 자동 완성에 관한 콘텐츠를 제공하는 모든 필드가 포함되어야 합니다.

콘텐츠가 추가되면 용어를 완료할 가능성이 커지므로 필드 풀이 더 크면 자동 완성에 유익합니다.

반면 제안은 필드 선택이 선택적일 때 더 나은 결과를 생성합니다. 제안은 검색 문서의 프록시이므로 단일 결과를 가장 잘 나타내는 필드가 필요합니다. 여러 일치 항목을 구분하는 이름, 제목 또는 기타 고유 필드가 가장 적합합니다. 필드가 반복되는 값으로 구성된 경우 제안은 같은 결과로 구성되며 사용자는 어떤 항목을 클릭해야 할지 알 수 없습니다.

입력 시 검색 기능을 모두 충족하려면 자동 완성에 필요한 모든 필드를 추가한 다음, “$select”, “$top”, “$filter”및 “searchFields”를 사용하여 제안 결과를 제어합니다.

### <a name="choose-analyzers"></a>분석기 선택

분석기를 선택하면 필드를 토큰화한 후에 접두사를 지정하는 방법이 결정됩니다. 예를 들어, “context-sensitive”와 같이 하이픈으로 연결된 문자열의 경우 언어 분석기를 사용하면 “context”, “sensitive”, “context-sensitive”와 같은 토큰 조합이 생성됩니다. 표준 Lucene 분석기를 사용했다면 하이픈으로 연결된 문자열은 없습니다. 

분석기를 평가할 때 용어 처리 방식에 관한 인사이트를 얻으려면 [Analyze Text API](/rest/api/searchservice/test-analyzer)를 사용하는 것이 좋습니다. 인덱스를 빌드한 후에는 문자열에서 다양한 분석기를 사용하여 토큰 출력을 볼 수 있습니다.

[사용자 지정 분석기](index-add-custom-analyzers.md) 또는 [기본 제공 분석기](index-add-custom-analyzers.md#built-in-analyzers)(표준 Lucene 제외)를 사용하는 필드는 잘못된 결과를 방지하기 위해 명시적으로 허용되지 않습니다.

> [!NOTE]
> 예를 들어 특정 쿼리 시나리오에 키워드 또는 ngram 분석기가 필요한 경우와 같이 분석기 제약 조건을 해결해야 하는 경우 같은 콘텐츠에 두 개의 개별 필드를 사용해야 합니다. 그러면 필드 중 하나에 제안기가 있고 다른 하나는 사용자 지정 분석기 구성으로 설정할 수 있습니다.

## <a name="create-using-the-portal"></a>포털을 사용하여 만들기

**인덱스 추가** 또는 **데이터 가져오기** 마법사를 사용하여 인덱스를 생성할 때 제안기를 사용으로 설정하는 옵션이 있습니다.

1. 인덱스 정의에서 제안기의 이름을 입력합니다.

1. 새 필드의 각 필드 정의에서 제안기 열의 확인란을 선택합니다. 확인란은 문자열 필드에만 사용할 수 있습니다. 

앞서 언급했듯이 분석기 선택은 토큰화 및 접두사 지정에 영향을 미칩니다. 제안기를 사용하도록 설정할 때 전체 필드 정의를 고려합니다. 

## <a name="create-using-rest"></a>REST를 사용하여 만들기

REST API에서 [인덱스 만들기](/rest/api/searchservice/create-index) 또는 [인덱스 업데이트](/rest/api/searchservice/update-index)를 통해 제안기를 추가합니다. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>.NET을 사용하여 만들기

C#에서 [SearchSuggester 개체](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)를 정의합니다. `Suggesters`는 SearchIndex 개체의 컬렉션이지만 하나의 항목만 사용할 수 있습니다. 인덱스 정의에 제안기를 추가합니다.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>속성 참조

|속성      |Description      |
|--------------|-----------------|
| name        | 제안기 정의에 지정되지만 자동 완성 또는 제안 요청에서도 호출됩니다. |
| sourceFields | 제안기 정의에 지정됩니다. 인덱스에 있는 제안 콘텐츠의 원본인 하나 이상의 필드 목록입니다. 필드는 `Edm.String` 및 `Collection(Edm.String)` 형식이어야 합니다. 분석기가 필드에 지정된 경우 [관련 목록](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)(사용자 지정 분석기가 아님)에서 명명된 어휘 분석기여야 합니다. </br></br>검색 창의 완성된 문자열이든 드롭다운 목록의 완성된 문자열이든 적절한 예상 응답을 제공하는 필드만 지정하는 것이 모범 사례입니다. </br></br>호텔 이름은 정밀도가 있기 때문에 좋은 후보입니다. 설명 및 댓글과 같은 자세한 정보 표시 필드는 너무 복잡합니다. 마찬가지로 범주 및 태그와 같은 반복적인 필드는 효과가 덜합니다. 예제에는 여러 필드를 포함할 수 있음을 보여 주기 위해 “category”가 포함되어 있습니다. |
| searchMode  | REST 전용 매개 변수이지만 포털에도 표시됩니다. 이 매개 변수는 .NET SDK에서 사용할 수 없습니다. 후보 문구를 검색하는 데 사용되는 전략을 나타냅니다. 현재 지원되는 모드는 현재 용어의 시작 부분과 일치하는 `analyzingInfixMatching`뿐입니다.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>제안기 사용

제안기가 쿼리에 사용됩니다. 제안기가 생성된 후 입력 시 검색 기능을 위해 다음 API 중 하나를 호출합니다.

+ [제안 REST API](/rest/api/searchservice/suggestions)
+ [자동 완성 REST API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync 메서드](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync 메서드](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

검색 애플리케이션에서 클라이언트 코드는 [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/)와 같은 라이브러리를 활용하여 부분 쿼리를 수집하고 일치 항목을 제공해야 합니다. 이 작업에 관한 자세한 내용은 [클라이언트 코드에 자동 완성 또는 제안된 결과 추가](search-add-autocomplete-suggestions.md)를 참조하세요.

API 사용법은 다음 자동 완성 REST API 호출에 설명되어 있습니다. 이 예제에는 두 가지 요점이 있습니다. 첫째, 모든 쿼리와 마찬가지로 작업은 인덱스의 문서 컬렉션에 대해 수행되며 쿼리에는 이 경우 부분 쿼리를 제공하는 “search” 매개 변수가 포함됩니다. 두 번째로, 요청에 “suggesterName”을 추가해야 합니다. 제안기가 인덱스에 정의되어 있지 않으면 자동 완성 또는 제안에 대한 호출이 실패합니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>샘플 코드

+ [웹 사이트에 검색 추가(JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog)는 클라이언트 앱에서 부분 용어 완성을 위해 오픈 소스 제안 패키지를 사용합니다.

+ [C#으로 첫 번째 앱 만들기(강의 3 - 입력 시 검색 추가)](tutorial-csharp-type-ahead-and-suggestions.md) 샘플은 제안된 쿼리, 자동 완성 및 패싯 검색을 보여 줍니다. 이 코드는 위젯을 사용하는 대신 자동 완성 기능에 관한 기본 지원을 제공합니다.

## <a name="next-steps"></a>다음 단계

요청\ 공식에 관해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [클라이언트 코드에 자동 완성 및 제안 추가](search-add-autocomplete-suggestions.md)