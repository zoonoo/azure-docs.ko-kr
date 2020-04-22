---
title: 검색 상자에 자동 완성 및 제안 추가
titleSuffix: Azure Cognitive Search
description: 제안자를 만들고 완료된 용어 또는 구로 검색 상자를 자동으로 완료하는 요청을 공식화하여 Azure Cognitive Search에서 사용자 유형별 검색 쿼리 작업을 활성화합니다. 제안된 일치 항목을 반환할 수도 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758114"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>클라이언트 앱에 자동 완성 및 제안 추가

사용자 입력으로 검색하는 것은 사용자가 시작한 쿼리의 생산성을 향상시키는 일반적인 기술입니다. Azure Cognitive Search에서 이 환경은 부분 입력("마이크로"를 "마이크로"로 완료)을 기반으로 용어 또는 구를 완료하는 *자동 완성을*통해 지원됩니다. 또 다른 양식은 *제안사항입니다:* 일치하는 문서의 짧은 목록(세부 정보 페이지에 연결할 수 있도록 ID가 있는 책 제목 반환). 자동 완성과 제안은 모두 인덱스의 일치에 근거합니다. 서비스는 0개의 결과를 반환하는 쿼리를 제공하지 않습니다.

Azure 인지 검색에서 이러한 환경을 구현하려면 다음이 필요합니다.

+ 백 엔드의 *제안자입니다.*
+ 요청에 자동 [완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 또는 제안 API를 지정하는 [쿼리입니다.](https://docs.microsoft.com/rest/api/searchservice/suggestions) *query*
+ 클라이언트 앱에서 사용자 유형별 검색 상호 작용을 처리하는 *UI 컨트롤입니다.* 이를 위해 기존 자바스크립트 라이브러리를 사용하는 것이 좋습니다.

Azure Cognitive Search에서 제안자로 등록한 선택한 필드에서 자동 완성된 쿼리 및 제안된 결과가 검색 인덱스에서 검색됩니다. 제안자는 인덱스의 일부이며 쿼리를 완료하거나 결과를 제안하거나 둘 다 수행하는 콘텐츠를 제공할 필드를 지정합니다. 인덱스를 만들고 로드하면 제안자 데이터 구조가 내부적으로 만들어 부분 쿼리에 일치하는 데 사용되는 접두사를 저장합니다. 제안의 경우, 고유하거나 적어도 반복적이지 않은 적절한 필드를 선택하는 것이 경험에 필수적입니다. 자세한 내용은 [제안자 만들기를](index-add-suggesters.md)참조하십시오.

이 문서의 나머지 부분에서는 쿼리 및 클라이언트 코드에 중점을 두습니다. 자바 스크립트와 C #를 사용하여 핵심 사항을 설명합니다. REST API 예제는 각 작업을 간결하게 표시하는 데 사용됩니다. 종단 간 코드 샘플에 대한 링크는 [다음 단계를](#next-steps)참조하십시오.

## <a name="set-up-a-request"></a>요청 설정

요청의 요소에는 검색 유형 API 중 하나, 부분 쿼리 및 제안자가 포함됩니다. 다음 스크립트는 자동 완성 REST API를 예로 사용하여 요청의 구성 요소를 보여 줍니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**제안자Name은** 용어 또는 제안을 완료하는 데 사용되는 제안자 인식 필드를 제공합니다. 특히 제안의 경우 필드 목록은 일치하는 결과 중에서 명확한 선택을 제공하는 목록으로 구성되어야 합니다. 컴퓨터 게임을 판매하는 사이트에서 필드가 게임 제목일 수 있습니다.

**검색** 매개 변수는 jQuery 자동 완성 컨트롤을 통해 문자가 쿼리 요청에 공급되는 부분 쿼리를 제공합니다. 위의 예에서 "minecraf"는 컨트롤이 통과했을 수 있는 내용을 정적인 그림으로 나타미다.

API는 부분 쿼리에 최소 길이 요구 사항을 적용하지 않습니다. 그것은 하나의 문자만큼 작은 수 있습니다. 그러나 jQuery 자동 완성은 최소 길이를 제공합니다. 최소 2~3자입니다.

일치는 입력 문자열의 아무 곳이나 용어의 시작 부분에 있습니다. "빠른 갈색 여우"를 감안할 때, 자동 완성 및 제안은 모두 """, "빠른", "갈색", 또는 "여우"의 부분 버전에 일치하지만 "rown"또는 "황소"와 같은 부분 적인 infix 용어에는 일치하지 않습니다. 또한 각 일치 는 다운스트림 확장의 범위를 설정합니다. "빠른 br"의 부분 쿼리는 "빠른 갈색"또는 "빠른 빵"과 일치하지만 "빠른"이 앞에 가지 않는 한 "갈색"이나 "빵"이 일치하지 않습니다.

### <a name="apis-for-search-as-you-type"></a>검색 유형용 API

나머지 및 .NET SDK 참조 페이지에 대 한 다음 링크를 따르십시오.

+ [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [제안와HttpMessagesAsync 방법](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [자동 완성Http메시지Async 방법](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>응답 구조

자동 완성 및 제안에 대한 응답은 패턴에 대해 예상할 수 있는 내용입니다: [자동 완성은](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) 용어 목록을 [반환하고, 제안서에는](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) 용어와 문서 ID를 반환하여 문서를 가져올 수 있습니다(조회 [문서](https://docs.microsoft.com/rest/api/searchservice/lookup-document) API를 사용하여 세부 정보 페이지에 대한 특정 문서를 가져옵니다).

응답은 요청의 매개 변수에 의해 형성됩니다. 자동 완성의 경우 [**자동 completeMode를**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) 설정하여 텍스트 완성이 하나 또는 두 개의 용어에서 발생하는지 여부를 결정합니다. 제안의 경우 선택한 필드에 응답 내용이 결정됩니다.

제안의 경우 중복되거나 관련이 없는 결과로 보이는 것을 피하기 위해 응답을 더 구체화해야 합니다. 결과를 제어하려면 요청에 더 많은 매개 변수를 포함합니다. 다음 매개 변수는 자동 완성 및 제안 모두에 적용되지만 제안자가 여러 필드를 포함하는 경우 특히 제안에 더 많이 필요합니다.

| 매개 변수 | 사용 |
|-----------|-------|
| **$select** | 제안자에서 **여러 sourceFields가** 있는 경우 **$select** 사용하여 값()을`$select=GameTitle`기여하는 필드를 선택합니다. |
| **searchFields** | 쿼리를 특정 필드로 제한합니다. |
| **$filter** | 결과 집합 ()에`$filter=Category eq 'ActionAdventure'`일치 조건을 적용합니다. |
| **$top** | 결과를 특정 숫자()로`$top=5`제한합니다.|

## <a name="add-user-interaction-code"></a>사용자 상호 작용 코드 추가

쿼리 용어를 자동으로 채우거나 일치하는 링크 목록을 삭제하려면 Azure Search Cognitive 인덱스에 대한 자동 완성 또는 제안 쿼리와 같은 외부 소스의 요청을 사용할 수 있는 사용자 상호 작용 코드(일반적으로 JavaScript)가 필요합니다.

이 코드를 기본적으로 작성할 수는 있지만 기존 JavaScript 라이브러리의 함수를 사용하는 것이 훨씬 쉽습니다. 이 문서에서는 제안에 대한 두 가지와 자동 완성을 위한 두 가지를 보여 줍니다. 

+ [자동 완성 위젯(jQuery UI)은](https://jqueryui.com/autocomplete/) 제안 예제에서 사용됩니다. 검색 상자를 만든 다음 자동 완성 위젯을 사용하는 JavaScript 함수에서 참조할 수 있습니다. 위젯의 속성은 소스(자동 완성 또는 제안 함수), 작업을 수행하기 전에 입력 문자의 최소 길이 및 위치를 설정합니다.

+ [XDSoft 자동 완성 플러그인은](https://xdsoft.net/jqplugins/autocomplete/) 자동 완성 예제를 사용합니다.

이러한 라이브러리를 사용하여 제안과 자동 완성을 모두 지원하는 검색 상자를 빌드합니다. 검색 상자에 수집된 입력은 제안 및 자동 완성 작업과 쌍을 이룹니다.

## <a name="suggestions"></a>제안

이 섹션에서는 검색 상자 정의부터 시작하여 제안된 결과의 구현을 안내합니다. 또한 이 문서에서 참조한 첫 번째 JavaScript 자동 완성 라이브러리를 호출하는 방법과 스크립트를 보여 주기도 합니다.

### <a name="create-a-search-box"></a>검색 상자 만들기

[jQuery UI 자동 완성 라이브러리와](https://jqueryui.com/autocomplete/) C#의 MVC 프로젝트를 가정하면 **Index.cshtml** 파일에서 자바스크립트를 사용하여 검색 상자를 정의할 수 있습니다. 라이브러리는 제안을 검색하기 위해 MVC 컨트롤러에 비동기 호출을 하여 검색 상자에 검색 형 상호 작용을 추가합니다.

**Index.cshtml** 폴더 \Views\Home에서 검색 상자를 만드는 줄은 다음과 같습니다.

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

이 예제는 스타일 지정에 대한 클래스, JavaScript에서 참조할 ID 및 자리 표시자 텍스트가 있는 간단한 입력 텍스트 상자입니다.  

동일한 파일 내에 검색 상자를 참조하는 자바스크립트를 포함합니다. 다음 함수는 부분 용어 입력을 기반으로 일치하는 문서를 요청하는 제안 API를 호출합니다.

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

은 `source` 검색 상자 아래에 표시 할 항목 목록을 얻을 수있는 jQuery UI 자동 완성 함수를 알려줍니다. 이 프로젝트는 MVC 프로젝트이므로 쿼리 제안을 반환하는 논리가 포함된 **HomeController.cs** **제안** 함수를 호출합니다. 또한 이 함수는 강조 표시, 유사 일치, 용어를 제어하는 몇 가지 매개 변수도 전달합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

검색 `minLength: 3` 상자에 3자 이상이 있는 경우에만 권장 사항이 표시됩니다.

### <a name="enable-fuzzy-matching"></a>퍼지 매칭 사용

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 편집 거리는 1이며, 이는 사용자 입력과 일치 간에 한 문자의 최대 불일치가 있을 수 있음을 의미합니다. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>강조 표시 사용

강조 표시는 입력에 해당하는 결과의 문자에 글꼴 스타일을 적용합니다. 예를 들어 부분 입력이 "마이크로"인 경우 결과는 **마이크로**소프트, **마이크로**범위 등으로 나타납니다. 강조 표시는 추천 함수와 인라인으로 정의된 하이라이트사전태그 및 강조 표시 태그 매개 변수를 기반으로 합니다.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>기능 제안

C# 및 MVC 응용 프로그램을 사용하는 경우 컨트롤러 디렉토리 아래에 **있는 HomeController.cs** 파일은 제안된 결과에 대한 클래스를 만들 수 있습니다. .NET에서 제안 함수는 [문서운영 확장.제안 메서드를](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)기반으로 합니다.

메서드는 `InitSearch` Azure 인지 검색 서비스에 인증된 HTTP 인덱스 클라이언트를 만듭니다. .NET SDK에 대한 자세한 내용은 [.NET 응용 프로그램에서 Azure 인지 검색을 사용하는 방법을](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)참조하세요.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다. 메서드는 [SuggestParameters 개체를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="autocomplete"></a>자동 완성

지금까지 검색 UX 코드는 제안에 중점을 두어 왔습니다. 다음 코드 블록은 XDSoft jQuery UI 자동 완성 함수를 사용하여 Azure 인지 검색 자동 완성 요청을 전달하는 자동 완성을 보여 주며, 자동 완성을 표시합니다. 제안 사항과 마찬가지로 C# 응용 프로그램에서 사용자 상호 작용을 지원하는 코드는 **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

### <a name="autocomplete-function"></a>자동 완성 기능

자동 완성은 [문서운영확장.자동 완성 방법을](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)기반으로 합니다. 제안 사항과 마찬가지로 이 코드 블록은 **HomeController.cs** 파일에 들어갑니다.

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 함수는 검색 용어 입력을 사용합니다. 이 메서드는 [AutoCompleteParameters 개체](https://docs.microsoft.com/rest/api/searchservice/autocomplete)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

## <a name="next-steps"></a>다음 단계

이러한 링크를 따라 종단 간 명령이나 유형별 검색 환경을 모두 보여 주는 코드를 따르십시오. 두 코드 예제 모두 제안의 하이브리드 구현과 함께 자동 완성을 포함합니다.

+ [자습서: C# 에서 첫 번째 응용 프로그램을 만들기 (단원 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# 코드 샘플: azure-search-dotnet-샘플/첫 번째 앱 만들기/3-추가-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [REST 나란히 코드 샘플이 있는 C# 및 자바스크립트](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)