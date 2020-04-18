---
title: 제안기 만들기
titleSuffix: Azure Cognitive Search
description: 제안자를 만들고 자동 완성 또는 자동 제안 쿼리 용어를 호출하는 요청을 공식화하여 Azure Cognitive Search에서 미리 입력 된 쿼리 작업을 활성화합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641336"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>제안자 만들기하여 쿼리에서 자동 완성 및 제안된 결과를 사용하도록 설정합니다.

Azure Cognitive Search에서 검색 [인덱스에](search-what-is-an-index.md)추가된 **제안** 자 구문에서 "사용자 유형으로 검색"을 사용할 수 있습니다. 제안자는 용어 또는 구를 완료하는 *자동 완성및*일치하는 문서의 짧은 목록을 반환하는 *제안의* 두 가지 환경을 지원합니다.  

[C#에서 첫 번째 앱 만들기의](tutorial-csharp-type-ahead-and-suggestions.md) 다음 스크린샷은 둘 다 를 보여 줍니다. 자동 완성은 잠재적인 용어를 예상하여 "in"으로 "tw"를 마무리합니다. 제안은 호텔 이름과 같은 필드가 인덱스에서 일치하는 호텔 검색 문서를 나타내는 미니 검색 결과입니다. 제안사항의 경우 설명 정보를 제공하는 모든 필드를 표시할 수 있습니다.

![자동 완성 및 제안된 쿼리의 시각적 비교](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "자동 완성 및 제안된 쿼리의 시각적 비교")

이러한 기능을 개별적으로 또는 함께 사용할 수 있습니다. Azure Cognitive Search에서 이러한 동작을 구현하기 위해 인덱스 및 쿼리 구성 요소가 있습니다. 

+ 인덱스에서 제안기를 인덱스에 추가합니다. 포털, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)또는 [.NET SDK를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)사용할 수 있습니다. 이 문서의 나머지 부분에서는 제안자 만들기에 중점을 두습니다.

+ 쿼리 요청에서 아래에 나열된 [API](#how-to-use-a-suggester)중 하나를 호출합니다.

문자열 필드에 대해 필드별로 검색 유형 지원이 활성화됩니다. 스크린샷에 표시된 것과 유사한 환경을 원하는 경우 동일한 검색 솔루션 내에서 두 typeahead 동작을 모두 구현할 수 있습니다. 두 요청 모두 특정 인덱스의 *문서* 컬렉션을 대상으로 하며 사용자가 세 문자 입력 문자열을 제공한 후 응답이 반환됩니다.

## <a name="what-is-a-suggester"></a>제안자란 무엇입니까?

제안자는 부분 쿼리에 일치하기 위한 접두사를 저장하여 사용자 유형별 검색 동작을 지원하는 데이터 구조입니다. 토큰화된 용어와 마찬가지로 접두사는 제안자 필드 컬렉션에 지정된 각 필드에 대해 하나씩 반전된 인덱스에 저장됩니다.

접두사를 만들 때 제안자는 전체 텍스트 검색에 사용되는 것과 유사한 자체 분석 체인을 가있습니다. 그러나 전체 텍스트 검색에서 분석과 달리 제안자는 표준 Lucene 분석기(기본값) 또는 [언어 분석기를](index-add-language-analyzers.md)사용하는 필드에서만 작동할 수 있습니다. [사용자 지정 분석기](index-add-custom-analyzers.md) 또는 [미리 정의된 분석기를](index-add-custom-analyzers.md#predefined-analyzers-reference) 사용하는 필드(표준 Lucene 제외)는 잘못된 결과를 방지하기 위해 명시적으로 허용되지 않습니다.

> [!NOTE]
> 분석기 제약 조건을 해결해야 하는 경우 동일한 콘텐츠에 대해 두 개의 별도 필드를 사용합니다. 이렇게 하면 필드 중 하나에 제안자가 있고 다른 필드는 사용자 지정 분석기 구성으로 설정할 수 있습니다.

## <a name="define-a-suggester"></a>제안자 정의

제안기를 만들려면 [인덱스 스키마에](https://docs.microsoft.com/rest/api/searchservice/create-index) 하나를 추가하고 [각 속성을 설정합니다.](#property-reference) 인덱스에서 하나의 제안자(특히 제안자 컬렉션의 제안자 1명)를 가질 수 있습니다. 제안기를 만드는 가장 좋은 시기는 제안기를 사용할 필드를 정의하는 경우입니다.

### <a name="choose-fields"></a>필드 선택

제안자는 여러 속성을 가지고 있지만 주로 검색 유형 환경을 사용하도록 설정하는 필드 컬렉션입니다. 특히 제안의 경우 단일 결과를 가장 잘 나타내는 필드를 선택합니다. 여러 일치 항목을 구분하는 이름, 제목 또는 기타 고유 필드가 가장 적합합니다. 필드가 반복값으로 구성된 경우 제안 내용은 동일한 결과로 구성되며 사용자는 클릭할 값을 알 수 없습니다.

각 필드는 인덱싱 중에 어휘 분석을 수행하는 분석기를 사용하는지 확인합니다. 기본 표준 Lucene 분석기 ()`"analyzer": null`또는 [언어 분석기](index-add-language-analyzers.md) (예 `"analyzer": "en.Microsoft"`:)를 사용할 수 있습니다. 

분석기를 선택하면 필드가 토큰화되고 이후에 접두사되는 방식이 결정됩니다. 예를 들어 "컨텍스트에 민감한"과 같은 하이픈이 있는 문자열의 경우 언어 분석기를 사용하면 "컨텍스트", "민감", "컨텍스트 구분"과 같은 토큰 조합이 생성됩니다. 표준 Lucene 분석기를 사용한 경우 하이픈이 있는 문자열이 존재하지 않습니다.

> [!TIP]
> [텍스트 분석 API를](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 사용하여 용어가 토큰화되고 이후에 접두사되는 방법에 대한 통찰력을 가지는 것이 좋습니다. 인덱스를 작성하면 문자열에서 다양한 분석기를 사용하여 인덱스가 방출되는 토큰을 볼 수 있습니다.

### <a name="when-to-create-a-suggester"></a>제안자 만들기 시기

제안기를 만드는 가장 좋은 시기는 필드 정의 자체를 만드는 경우입니다.

기존 필드를 사용하여 제안기를 만들려고 하면 API에서 제안자가 허용되지 않습니다. 접두사는 인덱싱 중에 생성되며, 두 개 이상의 문자 조합의 부분 용어가 전체 용어와 함께 토큰화될 때 생성됩니다. 기존 필드가 이미 토큰화되어 있으므로 제안자에 추가하려면 인덱스를 다시 작성해야 합니다. 자세한 내용은 [Azure 인지 검색 인덱스를 다시 빌드하는 방법을](search-howto-reindex.md)참조하세요.

## <a name="create-using-rest"></a>REST를 사용하여 만들기

REST API에서 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)를 통해 제안자를 추가합니다. 

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

C#에서 [제안자 개체를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)정의합니다. `Suggesters`컬렉션이지만 하나의 항목만 가져갈 수 있습니다. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>속성 참조

|속성      |Description      |
|--------------|-----------------|
|`name`        |제안기의 이름입니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 유일한 `analyzingInfixMatching`모드는 용어의 시작 부분에 현재 일치하는 모드입니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 필드는 형식 `Edm.String` 및 `Collection(Edm.String)`에 있어야 합니다. 필드에 분석기를 지정한 경우 사용자 지정 분석기가 아닌 [이 목록에서](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 명명된 분석기여야 합니다.<p/> 검색 표시줄에서 완료된 문자열이든 드롭다운 목록이든 관계없이 예상되고 적절한 응답에 자신을 제공하는 필드만 지정하는 것이 좋습니다.<p/>호텔 이름은 정밀도가 있기 때문에 좋은 후보입니다. 설명 및 주석과 같은 자세한 필드는 너무 조밀합니다. 마찬가지로 범주 및 태그와 같은 반복 필드는 효율성이 낮습니다. 예제에서는 여러 필드를 포함할 수 있음을 보여 주는 "범주"를 포함합니다. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>제안자 사용

제안자는 쿼리에 사용됩니다. 제안자가 생성된 후 다음 API 중 하나를 호출하여 사용자 유형별 검색 환경을 찾습니다.

+ [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [제안와HttpMessagesAsync 방법](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [자동 완성Http메시지Async 방법](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

검색 응용 프로그램에서 클라이언트 코드는 [jQuery UI 자동 완성과](https://jqueryui.com/autocomplete/) 같은 라이브러리를 활용하여 부분 쿼리를 수집하고 일치를 제공해야 합니다. 이 작업에 대한 자세한 내용은 [클라이언트 코드에 자동 완성 또는 제안된 결과 추가를](search-autocomplete-tutorial.md)참조하십시오.

API 사용량은 자동 완성 REST API에 대한 다음 호출에서 설명합니다. 이 예제에는 두 가지 테이크아웃이 있습니다. 첫째, 모든 쿼리와 마찬가지로 작업은 인덱스의 문서 컬렉션에 대한 작업이며 쿼리에는 부분 쿼리를 제공하는 **검색** 매개 변수가 포함됩니다. 둘째, 요청에 **제안자이름을** 추가해야 합니다. 제안자가 인덱스에 정의되지 않은 경우 자동 완성 또는 제안 호출이 실패합니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>예제 코드

+ [C# (단원 3 - 유형별 검색 추가) 샘플에서 첫 번째 앱을 만들면](tutorial-csharp-type-ahead-and-suggestions.md) 제안자 구성, 제안된 쿼리, 자동 완성 및 면이 있는 탐색이 표시됩니다. 이 코드 샘플은 샌드박스 Azure Cognitive Search 서비스에서 실행되며 미리 로드된 호텔 인덱스를 사용하므로 F5를 눌러 응용 프로그램을 실행하기만 하면 됩니다. 구독또는 로그인이 필요하지 않습니다.

+ [DotNetHowToAutocomplete는](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) C# 및 Java 코드를 모두 포함하는 이전 샘플입니다. 또한 제안자 구성, 제안된 쿼리, 자동 완성 및 면 탐색을 보여 줍니다. 이 코드 샘플은 호스팅된 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 데이터를 사용합니다. 

## <a name="next-steps"></a>다음 단계

다음 예제에서는 요청이 공식화되는 방법을 확인하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [클라이언트 코드에 자동 완성 및 제안 추가](search-autocomplete-tutorial.md) 
