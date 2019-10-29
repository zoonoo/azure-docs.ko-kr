---
title: 검색 상자에 제안 및 자동 완성 추가
titleSuffix: Azure Cognitive Search
description: 제안기를 만들고 완성된 용어 또는 구를 검색 상자에 채우는 요청을 공식화하여 Azure Cognitive Search에서 자동 완성 쿼리 작업을 사용하도록 설정합니다.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792515"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Azure Cognitive Search 애플리케이션에 제안 또는 자동 완성 추가

이 문서에서는 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 및 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete)을 사용하여 search-as-you-type(입력할 때 자동으로 검색) 동작을 지원하는 강력한 검색 상자를 빌드하는 방법에 대해 알아봅니다.

+ *제안*은 입력하면 생성되는 제안된 결과입니다. 이 경우 각 제안은 지금까지 입력한 것과 일치하는 인덱스의 단일 결과입니다. 

+ *자동 완성*은 사용자가 현재 입력하고 있는 단어 또는 구를 "완성"합니다. 결과를 반환하는 대신 쿼리를 완성한 다음, 이 쿼리를 실행하여 결과를 반환할 수 있습니다. 제안과 마찬가지로 쿼리에서 완성된 단어 또는 구는 인덱스의 일치 항목을 기반으로 하여 단정됩니다. 서비스는 인덱스에서 0의 결과를 반환하는 쿼리를 제공하지 않습니다.

**DotNetHowToAutocomplete**에서 샘플 코드를 다운로드하고 실행하여 이러한 기능을 평가할 수 있습니다. 샘플 코드는 [NYCJobs 데모 데이터](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)로 채워진 미리 작성된 인덱스를 대상으로 합니다. NYCJobs 인덱스에는 제안 또는 자동 완성을 사용하는 데 필요한 [제안기 구문](index-add-suggesters.md)이 포함되어 있습니다. 샌드박스 서비스에서 호스팅되어 준비된 인덱스를 사용하거나 NYCJobs 샘플 솔루션의 데이터 로더를 사용하여 [고유한 인덱스를 채울](#configure-app) 수 있습니다. 

**DotNetHowToAutocomplete** 샘플은 C# 및 JavaScript 언어 버전 모두에서 제안과 자동 완성을 모두 보여 줍니다. C# 개발자는 [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk)를 사용하는 ASP.NET MVC 기반 애플리케이션을 단계별로 실행할 수 있습니다. 자동 완성 및 제안된 쿼리 호출을 수행하는 논리는 HomeController.cs 파일에서 찾을 수 있습니다. JavaScript 개발자는 [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)에 대한 직접 호출이 포함된 IndexJavaScript.cshtml에서 동등한 쿼리 논리를 찾을 수 있습니다. 

두 언어 버전 모두에서 프런트 엔드 사용자 환경은 [jQuery UI](https://jqueryui.com/autocomplete/) 및 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) 라이브러리를 기반으로 합니다. 이러한 라이브러리를 사용하여 제안과 자동 완성을 모두 지원하는 검색 상자를 빌드합니다. 검색 상자에 수집된 입력은 HomeController.cs 또는 IndexJavaScript.cshtml에 정의된 것과 같은 제안 및 자동 완성 작업과 쌍을 이룹니다.

이 연습에서 안내하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * JavaScript에서 검색 입력 상자를 구현하고, 제안된 일치 항목 또는 자동 완성 용어에 대한 요청을 실행합니다.
> * C#에서 제안 및 자동 완성 작업을 HomeController.cs에 정의합니다.
> * JavaScript에서 REST API를 직접 호출하여 동일한 기능을 제공합니다.

## <a name="prerequisites"></a>필수 조건

솔루션은 준비된 NYCJobs 데모 인덱스를 호스팅하는 라이브 샌드박스 서비스를 사용하므로 이 연습에서 Azure Cognitive Search 서비스는 선택 사항입니다. 사용자 고유의 검색 서비스에서 이 예제를 실행하려면 [NYC 작업 인덱스 구성](#configure-app)의 지침을 참조하세요.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

* [DotNetHowToAutoComplete 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)을 다운로드합니다.

샘플은 제안, 자동 완성, 패싯 탐색 및 클라이언트 쪽 캐싱을 포괄적으로 처리합니다. 샘플에서 제공하는 기능에 대한 자세한 설명은 추가 정보 및 설명을 검토하세요.

## <a name="run-the-sample"></a>샘플 실행

1. Visual Studio에서 **AutocompleteTutorial.sln**을 엽니다. 이 솔루션에는 NYC 작업 데모 인덱스와 연결된 ASP.NET MVC 프로젝트가 포함되어 있습니다.

2. 원하는 브라우저에서 F5를 눌러 프로젝트를 실행하고 페이지를 로드합니다.

맨 위에 C# 또는 JavaScript를 선택하는 옵션이 표시됩니다. C# 옵션은 브라우저에서 HomeController를 호출하고 Azure Cognitive Search .NET SDK를 사용하여 결과를 검색합니다. 

JavaScript 옵션은 브라우저에서 Azure Cognitive Search REST API를 직접 호출합니다. 이 옵션은 컨트롤러를 흐름 밖으로 꺼내기 때문에 일반적으로 성능이 눈에 띄게 높습니다. 요구 사항 및 언어 기본 설정에 부합하는 옵션을 선택할 수 있습니다. 각각에 대한 지침이 있는 페이지에 몇 가지 자동 완성 예제가 있습니다. 예제마다 테스트해볼 수 있는 몇 가지 권장 샘플 텍스트가 있습니다.  

각 검색 상자에 몇 자 입력해보고 결과를 확인합니다.

## <a name="search-box"></a>검색 상자

C# 및 JavaScript 버전 모두에서 검색 상자 구현은 정확히 동일합니다. 

\Views\Home 폴더 아래에서 **Index.cshtml** 파일을 열어 코드를 확인합니다.

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

이 예제는 스타일 지정에 대한 클래스, JavaScript에서 참조할 ID 및 자리 표시자 텍스트가 있는 간단한 입력 텍스트 상자입니다.  매직은 포함된 JavaScript에 있습니다.

C# 언어 샘플에서는 Index.cshtml의 JavaScript를 사용하여 [jQuery UI Autocomplete 라이브러리](https://jqueryui.com/autocomplete/)를 활용합니다. 이 라이브러리는 제안을 검색하는 MVC 컨트롤러를 비동기 방식으로 호출하여 자동 완성 환경을 검색 상자에 추가합니다. JavaScript 언어 버전은 IndexJavaScript.cshtml에 있습니다. 여기에는 검색 창에 대한 아래의 스크립트와 Azure Cognitive Search에 대한 REST API 호출이 포함되어 있습니다.

jQuery UI Autocomplete 함수를 호출하고 제안 요청을 전달하는 첫 번째 예제의 JavaScript 코드를 살펴보겠습니다.

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

위의 코드는 "example1a" 입력 상자에 대한 jQuery UI Autocomplete을 구성하기 위해 페이지를 로드할 때 브라우저에서 실행됩니다.  `minLength: 3`은 검색 상자에 3자 이상이 입력되었을 때만 권장 사항을 표시하도록 합니다.  원본 값이 중요합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

위의 줄에서는 검색 상자 아래에 표시할 항목 목록을 가져올 위치를 jQuery UI Autocomplete 함수에 알려줍니다. 이 프로젝트는 MVC 프로젝트이므로 쿼리 제안을 반환하는 논리가 포함된 HomeController.cs의 Suggest 함수를 호출합니다(다음 섹션에서 Suggest에 대해 자세히 설명함). 또한 이 함수는 강조 표시, 유사 일치, 용어를 제어하는 몇 가지 매개 변수도 전달합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>샘플을 확장하여 유사 일치 지원

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 반드시 필요하지는 않지만, 자동 완성 환경의 견고성을 크게 향상시킵니다. 소스 줄을 변경하여 유사 일치를 사용하도록 테스트해 보겠습니다.

다음 줄을

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

다음과 같이 변경합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

F5를 눌러 애플리케이션을 시작합니다.

"execative" 등의 항목을 입력하면 입력한 글자가 정확히 일치하지 않더라도 "executive"에 대한 결과가 불러지는 것을 확인합니다.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Azure Cognitive Search 자동 완성에서 지원하는 jQuery Autocomplete

지금까지 검색 UX 코드는 제안에 중점을 두었습니다. 다음 코드 블록에서는 jQuery UI Autocomplete 함수(index.cshtml의 91번 줄)를 표시하고, Azure Cognitive Search 자동 완성 요청을 전달합니다.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# 예제

이제 웹 페이지에 대한 JavaScript 코드를 검토했으므로 Azure Cognitive Search .NET SDK를 사용하여 제안된 일치 항목을 실제로 검색하는 C# 서버 쪽 컨트롤러 코드를 살펴보겠습니다.

Controllers 디렉터리에서 **HomeController.cs** 파일을 엽니다. 

가장 먼저 알 수 있는 것은 `InitSearch`라는 클래스의 맨 위에 있는 메서드입니다. 이 메서드는 인증된 HTTP 인덱스 클라이언트를 Azure Cognitive Search 서비스에 만듭니다. 자세한 내용은 [.NET 애플리케이션에서 Azure Cognitive Search를 사용하는 방법](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)을 참조하세요.

41번 줄에서 Suggest 함수를 확인합니다. 이는 [DocumentsOperationsExtensions.Suggest 메서드](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)를 기반으로 합니다.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
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

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다. 이 메서드는 [SuggestParameters 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)를 만든 다음, 제안 API로 전달합니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

69번 줄에서 Autocomplete 함수를 확인합니다. 이는 [DocumentsOperationsExtensions.Autocomplete 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)를 기반으로 합니다.

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

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 함수는 검색 용어 입력을 사용합니다. 이 메서드는 [AutoCompleteParameters 개체](https://docs.microsoft.com/rest/api/searchservice/autocomplete)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

(선택 사항) Suggest 함수의 시작 부분에 중단점을 추가하고 코드를 단계별로 확인합니다. SDK에서 반환하는 응답과 메서드에서 반환된 결과로 변환되는 방법을 확인합니다.

페이지의 다른 예제에서는 동일한 패턴에 따라 적중 강조 표시와 패싯을 추가하여 자동 완성 결과의 클라이언트 쪽 캐싱을 지원합니다. 각각을 검토하여 작동 방식과 검색 환경에서의 활용 방법을 살펴봅니다.

## <a name="javascript-example"></a>JavaScript 예제

자동 완성 및 제안의 Javascript 구현은 REST API를 호출하고, URI를 원본으로 사용하여 인덱스와 작업을 지정합니다. 

JavaScript 구현을 검토하려면 **IndexJavaScript.cshtml**을 엽니다. jQuery UI Autocomplete 함수도 검색 상자에 사용되어 검색 용어 입력을 수집하고 Azure Cognitive Search에 대한 비동기 호출을 수행하여 제안된 일치 항목 또는 완성된 용어를 검색합니다. 

첫 번째 예제의 JavaScript 코드를 살펴보겠습니다.

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

이 예제를 Home 컨트롤러를 호출하는 위의 예제와 비교하면 몇 가지 유사성을 확인할 수 있습니다.  `minLength` 및 `position`에 대한 자동 완성 구성은 정확히 동일합니다. 

여기서 큰 변화는 소스입니다. Home 컨트롤러에서 Suggest 메서드를 호출하는 대신 JavaScript 함수에서 REST 요청을 만들고 Ajax를 사용하여 이를 실행합니다. 그러면 응답이 "성공" 처리되고 소스로 사용됩니다.

REST 호출은 URI를 사용하여 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 또는 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 호출을 수행하는지 여부를 지정합니다. 다음 URI는 각각 9번 및 10번 줄에 있습니다.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

148번 줄에서 `autocompleteUri`를 호출하는 스크립트를 찾을 수 있습니다. `suggestUri`에 대한 첫 번째 호출은 39번 줄에 있습니다.

> [!Note]
> JavaScript에서 서비스에 대한 REST 호출을 수행하는 것은 REST API에 대한 편리한 데모로 제공되지만, 모범 사례 또는 추천 사항으로 해석하면 안 됩니다. API 키와 엔드포인트가 스크립트에 포함되면 서비스를 열어 스크립트에서 이러한 값을 읽을 수 있는 모든 사용자에게 서비스 거부 공격이 발생할 수 있습니다. 체험 서비스에서 호스팅되는 인덱스에서 학습 목적으로 JavaScript를 사용하는 것이 안전하지만, 프로덕션 코드에서는 인덱싱 및 쿼리 작업에 Java 또는 C#을 사용하는 것이 좋습니다. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>서비스에서 실행되도록 NYCJobs 구성

지금까지 호스팅된 NYCJobs 데모 인덱스를 사용했습니다. 인덱스를 포함하여 모든 코드를 완전히 살펴보려면 다음 지침에 따라 사용자 고유의 검색 서비스에서 인덱스를 만들고 로드합니다.

1. 현재 구독에서 [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 예제에서는 체험 서비스를 사용할 수 있습니다. 

   > [!Note]
   > 체험 Azure Cognitive Search 서비스를 사용하는 경우 세 개의 인덱스로 제한됩니다. NYCJobs 데이터 로더는 두 개의 인덱스를 만듭니다. 서비스에 새 인덱스를 허용하는 공간이 있는지 확인합니다.

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 코드를 다운로드합니다.

1. Visual Studio에서 NYCJobs 샘플 코드의 DataLoader 폴더에 있는 **DataLoader.sln**을 엽니다.

1. Azure Cognitive Search 서비스에 대한 연결 정보를 추가합니다. DataLoader 프로젝트 내에서 App.config를 열고, Azure Cognitive Search 서비스 및 Azure Cognitive Search 서비스 API 키를 반영하도록 TargetSearchServiceName 및 TargetSearchServiceApiKey appSettings를 변경합니다. 이 정보는 Azure Portal에서 확인할 수 있습니다.

1. F5 키를 눌러 애플리케이션을 시작하고, 두 개의 인덱스를 만들고, NYCJob 샘플 데이터를 가져옵니다.

1. **AutocompleteTutorial.sln**을 열고, **AutocompleteTutorial** 프로젝트에서 Web.config를 편집합니다. SearchServiceName 및 SearchServiceApiKey 값을 검색 서비스에 유효한 값으로 변경합니다.

1. F5 키를 눌러 애플리케이션을 실행합니다. 샘플 웹앱이 기본 브라우저에서 열립니다. 환경은 샌드박스 버전과 동일하며, 인덱스와 데이터만 서비스에서 호스팅됩니다.

## <a name="next-steps"></a>다음 단계

이 예제에서는 자동 완성 및 제안을 지원하는 검색 상자를 빌드하기 위한 기본 단계를 보여 줍니다. ASP.NET MVC 애플리케이션을 빌드하고, Azure Cognitive Search .NET SDK 또는 REST API를 사용하여 제안을 검색하는 방법을 살펴보았습니다.

다음 단계로 제안 및 자동 완성을 검색 환경에 통합해 보세요. 도움이 되는 참고 자료 문서는 다음과 같습니다.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Create Index REST API에서 패싯 인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index)

