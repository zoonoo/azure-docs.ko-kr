---
title: 검색 상자에 자동 완성 추가
titleSuffix: Azure Cognitive Search
description: 제안기를 만들고 완성된 용어 또는 구로 검색 상자를 자동 완성하는 요청을 작성하여 Azure Cognitive Search에서 입력과 동시에 검색(search-as-you-type) 쿼리 작업을 사용하도록 설정합니다. 제안된 일치 항목을 반환할 수도 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b260793cc3c0b5ac3ffce8fac909e2db8991c713
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307812"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Azure Cognitive Search를 사용하여 클라이언트 앱에 자동 완성 및 제안 추가

입력과 동시에 검색은 쿼리 생산성을 개선하기 위한 일반적인 기술입니다. Azure Cognitive Search에서 이 환경은 부분 입력을 기반으로 용어 또는 구를 완성하는 *자동 완성* 을 통해 지원됩니다(‘micro’를 입력하면 ‘microsoft’ 완성). 두 번째 사용자 환경은 *제안* 또는 일치하는 문서의 짧은 목록입니다(책에 대한 세부 정보 페이지에 연결할 수 있도록 ID와 함께 책 제목을 반환). 자동 완성과 제안 모두 인덱스의 일치 항목에 기반합니다. 이 서비스는 0의 결과를 반환하는 쿼리를 제공하지 않습니다.

Azure Cognitive Search에서 이러한 환경을 구현하려면 다음이 필요합니다.

+ 인덱스 스키마에 포함된 *제안기* 정의.
+ 요청에 대한 [자동 완성](/rest/api/searchservice/autocomplete) 또는 [제안](/rest/api/searchservice/suggestions) API를 지정하는 *쿼리*.
+ 클라이언트 앱에서 입력과 동시에 검색 상호 작용을 처리하는 *UI 컨트롤*. 기존 JavaScript 라이브러리를 이 용도로 사용하는 것이 좋습니다.

Azure Cognitive Search에서는 자동 완성된 쿼리와 제안된 결과가 검색 인덱스 및 제안기에 등록한 선택된 필드에서 검색됩니다. 제안기는 인덱스의 일부이며, 쿼리를 완료하거나 결과를 제안하거나 둘 다 수행하는 내용을 제공할 필드를 지정합니다. 인덱스가 만들어지고 로드되면 부분 쿼리에서 일치에 사용되는 접두사를 저장하는 제안기 데이터 구조가 내부에서 만들어집니다. 제안의 경우 고유하거나 적어도 반복적이지 않은 적절한 필드를 선택하는 것이 환경에 필수적입니다. 자세한 내용은 [제안기 만들기](index-add-suggesters.md)를 참조하세요.

이 문서의 나머지 부분에서는 쿼리와 클라이언트 코드에 대해 집중적으로 설명합니다. 핵심 사항을 설명하기 위해 JavaScript와 C#을 사용합니다. 각 작업을 간결하게 표시하기 위해 REST API 예제가 사용됩니다. 엔드투엔드 코드 샘플에 대한 링크는 [다음 단계](#next-steps)를 참조하세요.

## <a name="set-up-a-request"></a>요청 설정

요청 요소에는 입력과 동시에 검색 API, 부분 쿼리, 제안기 중 하나가 포함됩니다. 다음 스크립트는 자동 완성 REST API를 예제로 사용하여 요청의 구성 요소를 보여 줍니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

"suggesterName"은 용어 또는 제안을 완성하는 데 사용되는 제안기 인식 필드를 제공합니다. 특히 제안의 경우 필드 목록은 일치하는 결과 중에서 명확한 선택을 제공하는 결과로 구성되어야 합니다. 컴퓨터 게임을 판매하는 사이트에서 이 필드는 게임 타이틀이 될 수 있습니다.

"search" 매개 변수는 jQuery 자동 완성 컨트롤을 통해 쿼리 요청에 문자가 공급되는 부분 쿼리를 제공합니다. 위의 예제에서 "minecraf"는 컨트롤이 전달했을 수 있는 항목의 정적 예시입니다.

API는 부분 쿼리에 최소 길이 요구 사항을 적용하지 않습니다. 문자 하나여도 됩니다. 그러나 jQuery 자동 완성은 최소 길이를 제공합니다. 최소 2~3자가 일반적입니다.

일치 항목은 입력 문자열에서 용어의 시작 부분에 있습니다. ‘the quick brown fox’를 예로 들면 자동 완성과 제안 모두 부분 버전 ‘the’, ‘quick’, ‘brown’ 또는 ‘fox’에서는 비교하지만 ‘rown’ 또는 ‘ox’ 같은 부분 삽입사 용어에서는 비교하지 않습니다. 또한 각 일치 항목은 다운스트림 확장의 범위를 설정합니다. 부분 쿼리 ‘quick br’은 ‘quick brown’ 또는 ‘quick bread’와 비교되지만 ‘quick’이 앞에 오지 않는 한 ‘brown’ 또는 ‘bread’ 자체와는 비교되지 않습니다.

### <a name="apis-for-search-as-you-type"></a>입력과 동시에 검색을 위한 API

REST 및 .NET SDK 참조 페이지에 대한 다음 링크를 따라가세요.

+ [제안 REST API](/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestAsync 메서드](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync 메서드](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>응답 구성

자동 완성 및 제안에 대한 응답은 패턴을 통해 예상할 수 있습니다. [자동 완성](/rest/api/searchservice/autocomplete#response)은 용어 목록을 반환하고, [제안](/rest/api/searchservice/suggestions#response)은 용어 외에도 문서를 가져올 수 있도록 문서 ID도 반환합니다(특정 문서의 상세 페이지를 가져오려면 [문서 조회](/rest/api/searchservice/lookup-document) API를 사용하세요).

응답은 요청에서의 매개 변수에 따라 다음과 같이 구성됩니다.

+ 자동 완성의 경우 [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters)를 설정하여 텍스트 완성이 하나의 용어에서 발생하는지 두 용어에서 발생하는지 여부를 결정합니다. 

+ 제안의 경우 [$select](/rest/api/searchservice/suggestions#query-parameters)를 선택하여 이름과 설명처럼 고유하거나 구별되는 값이 포함된 필드를 반환합니다. 중복 값(예: 범주 또는 도시)을 포함하는 필드는 사용하지 않는 것이 좋습니다.

다음 추가 매개 변수는 자동 완성과 제안에 모두 적용되지만 특히 제안기에 여러 필드가 포함된 경우 제안에 더 필요할 수 있습니다.

| 매개 변수 | 사용량 |
|-----------|-------|
| searchFields | 쿼리를 특정 필드로 제한합니다. |
| $filter | 결과 집합에서 일치 조건을 적용합니다(`$filter=Category eq 'ActionAdventure'`). |
| $top | 결과를 특정 숫자로 제한합니다(`$top=5`).|

## <a name="add-user-interaction-code"></a>사용자 상호 작용 코드 추가

쿼리 용어를 자동으로 채우거나 일치하는 링크 목록을 드롭다운하려면 사용자 상호 작용 코드(일반적으로 JavaScript)가 필요합니다. 이 코드는 Azure Cognitive Search 인덱스에 대한 자동 완성 또는 제안 쿼리와 같은 외부 원본의 요청을 사용할 수 있습니다.

이 코드를 고유하게 작성할 수도 있지만 다음 중 하나와 같은 기존 JavaScript 라이브러리의 함수를 사용하는 것이 훨씬 쉽습니다. 

+ [자동 완성 위젯(jQuery UI)](https://jqueryui.com/autocomplete/)이 제안 코드 조각에 나타납니다. 검색 상자를 만든 다음 자동 완성 위젯을 사용하는 JavaScript 함수에서 검색 상자를 참조할 수 있습니다. 위젯의 속성은 원본(자동 완성 또는 제안 함수), 작업을 수행하기 전의 최소 입력 문자 길이, 위치 지정을 설정합니다.

+ [XDSoft 자동 완성 플러그 인](https://xdsoft.net/jqplugins/autocomplete/)이 자동 완성 코드 조각에 나타납니다.

+ [제안](https://www.npmjs.com/package/suggestions)이 [JavaScript 자습서](tutorial-javascript-overview.md) 및 코드 샘플에 나타납니다.

클라이언트에서 이러한 라이브러리를 사용하여 제안과 자동 완성을 모두 지원하는 검색 상자를 만듭니다. 검색 상자에 수집된 입력은 검색 서비스에서의 제안 및 자동 완성 작업에 쌍으로 연결할 수 있습니다.

## <a name="suggestions"></a>제안

이 섹션에서는 검색 상자 정의부터 시작하여 제안된 결과를 구현하는 과정을 안내합니다. 또한 이 문서에서 참조하는 첫 번째 JavaScript 자동 완성 라이브러리를 호출하는 스크립트를 작성하는 방법을 보여 줍니다.

### <a name="create-a-search-box"></a>검색 상자 만들기

[jQuery UI 자동 완성 라이브러리](https://jqueryui.com/autocomplete/) 및 C#의 MVC 프로젝트가 있다면 **Index. cshtml** 파일에서 JavaScript를 사용하여 검색 상자를 정의할 수 있습니다. 이 라이브러리는 제안을 검색하는 MVC 컨트롤러를 비동기 방식으로 호출하여 입력과 동시에 검색 상호 작용을 검색 상자에 추가합니다.

\Views\Home 폴더 아래의 **Index.cshtml** 에서 검색 상자를 만드는 줄은 다음과 같습니다.

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

이 예제는 스타일 지정에 대한 클래스, JavaScript에서 참조할 ID 및 자리 표시자 텍스트가 있는 간단한 입력 텍스트 상자입니다.  

동일한 파일 내에서 검색 상자를 참조하는 JavaScript를 포함합니다. 다음 함수는 부분 용어 입력을 기반으로 제안된 일치 문서를 요청하는 제안 API를 호출합니다.

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source`는 검색 상자 아래에 표시할 항목 목록을 가져올 위치를 jQuery UI Autocomplete 함수에 알려 줍니다. 이 프로젝트는 MVC 프로젝트이므로 쿼리 제안을 반환하는 논리가 포함된 **HomeController.cs** 의 **Suggest** 함수를 호출합니다. 또한 이 함수는 강조 표시, 유사 일치, 용어를 제어하는 몇 가지 매개 변수도 전달합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

`minLength: 3`은 검색 상자에 3자 이상이 입력되었을 때만 권장 사항을 표시하도록 합니다.

### <a name="enable-fuzzy-matching"></a>유사 일치 사용

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 편집 거리는 1입니다. 즉, 사용자 입력과 일치 사이에는 최대한 문자의 불일치가 있을 수 있습니다. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>강조 표시 사용

강조 표시는 결과에서 입력과 일치하는 문자에 글꼴 스타일을 적용합니다. 예를 들어 부분 입력이 ‘micro’이면 결과는 **micro** soft, **micro** scope 등으로 표시됩니다. 강조 표시는 Suggestion 함수를 사용하여 인라인으로 정의된 HighlightPreTag 및 HighlightPostTag 매개 변수를 기반으로 합니다.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Suggest 함수

C# 및 MVC 애플리케이션을 사용하는 경우 Controllers 디렉터리 아래의 **HomeController.cs** 파일에서 제안된 결과의 클래스를 만들 수 있습니다. .NET에서 Suggest 함수는 [SuggestAsync 메서드](/dotnet/api/azure.search.documents.searchclient.suggestasync)를 기반으로 합니다. .NET SDK에 대한 자세한 내용은 [.NET 애플리케이션에서 Azure Cognitive Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

`InitSearch` 메서드는 인증된 HTTP 인덱스 클라이언트를 Azure Cognitive Search 서비스에 만듭니다. [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) 클래스의 속성은 결과에서 검색되고 반환되는 필드, 일치 항목 수, 유사 일치가 사용되는지 여부를 결정합니다. 

자동 완성의 경우 유사 일치는 1 편집 거리(생략되거나 잘못된 문자 하나)로 제한됩니다. 자동 완성 쿼리의 유사 일치는 인덱스 크기 및 분할된 방식에 따라 예기치 않은 결과를 생성하는 경우도 있습니다. 자세한 내용은 [파티션 및 분할 개념](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)을 참조하세요.

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

SuggestAsync 함수는 적중 강조 표시를 반환할지 또는 검색어 입력 외에 유사 일치를 사용할지 결정하는 두 매개 변수를 사용합니다. 제안된 결과에는 최대 8개의 일치 항목이 포함될 수 있습니다. 이 메서드는 [SuggestOptions 개체](/dotnet/api/azure.search.documents.suggestoptions)를 만든 다음 Suggest API로 전달합니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="autocomplete"></a>자동 완성

지금까지 검색 UX 코드는 제안에 중점을 두었습니다. 다음 코드 블록에서는 XDSoft jQuery UI Autocomplete 함수를 사용하여 Azure Cognitive Search 자동 완성 요청을 전달하는 자동 완성을 보여 줍니다. 제안과 마찬가지로 C# 애플리케이션에서는 사용자 상호 작용을 지원하는 코드가 **index.cshtml** 에 들어갑니다.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Autocomplete 함수

자동 완성은 [AutocompleteAsync 메서드](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)를 기반으로 합니다. 제안과 마찬가지로 이 코드 블록은 **HomeController.cs** 파일에 들어갑니다.

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Autocomplete 함수는 검색 용어 입력을 사용합니다. 이 메서드는 [AutoCompleteParameters 개체](/rest/api/searchservice/autocomplete)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="next-steps"></a>다음 단계

입력과 동시에 검색 환경의 엔드투엔드 지침 또는 코드 데모를 보려면 다음 링크를 따라가세요. 샘플은 제안과 자동 완성의 하이브리드 구현을 보여 줍니다.

+ [웹 사이트에 검색 추가(JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog)는 클라이언트 앱에서 부분 용어 완성을 위해 오픈 소스 제안 패키지를 사용합니다.
+ [자습서: C#으로 첫 번째 앱 만들기(단원 3)](tutorial-csharp-type-ahead-and-suggestions.md) 및 연결된 [C# 코드 샘플: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)는 자동 완성 안료에 대한 네이티브 지원을 보여 줍니다.
