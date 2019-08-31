---
title: 검색 상자에 제안 사항 및 자동 완성 추가-Azure Search
description: 완료 된 사용 약관을 사용 하 여 검색 상자를 채우는 확인 기 및 공식화 요청을 만들어 Azure Search에서 형식 미리 쿼리 작업을 사용 하도록 설정 합니다.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183283"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Azure Search 응용 프로그램에 제안 사항 또는 자동 완성 추가

이 문서에서는 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 및 [자동 완성 기능](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 을 사용 하 여 검색 형식 동작을 지 원하는 강력한 검색 상자를 빌드하는 방법에 대해 알아봅니다.

+ 입력 시 제안 된 결과가 제안 됩니다. 여기서 각 제안은 지금까지 입력 한 것과 일치 하는 인덱스의 단일 결과입니다. 

+ *자동 완성* 사용자가 현재 입력 하 고 있는 단어 또는 구를 "완료" 합니다. 결과를 반환 하는 대신 쿼리를 완료 하 여 결과를 반환 하는 쿼리를 실행 합니다. 제안에 따라 쿼리의 완성 된 단어 또는 구가 인덱스에서 일치 하는 항목에 대해 예측 됩니다. 이 서비스는 인덱스에서 결과를 0으로 반환 하는 쿼리를 제공 하지 않습니다.

**DotNetHowToAutocomplete** 에서 샘플 코드를 다운로드 하 고 실행 하 여 이러한 기능을 평가할 수 있습니다. 샘플 코드는 [NYCJobs demo 데이터로](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)채워진 미리 작성 된 인덱스를 대상으로 합니다. NYCJobs 인덱스에는 제안 또는 자동 완성을 사용 하기 위한 요구 사항인 [확인 기 구문이](index-add-suggesters.md)포함 되어 있습니다. 샌드박스 서비스에서 호스트 되는 준비 된 인덱스를 사용 하거나 NYCJobs 샘플 솔루션의 데이터 로더를 사용 하 여 [사용자 고유의 인덱스를 채울](#configure-app) 수 있습니다. 

**DotNetHowToAutocomplete** 샘플에서는 및 JavaScript 언어 버전 모두 C# 에서 제안과 자동 완성을 모두 보여 줍니다. C#개발자는 [Azure Search .NET SDK](https://aka.ms/search-sdk)를 사용 하는 ASP.NET MVC 기반 응용 프로그램을 단계별로 실행 할 수 있습니다. 자동 완성 및 제안 된 쿼리 호출을 만들기 위한 논리는 HomeController.cs 파일에서 찾을 수 있습니다. JavaScript 개발자는 [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)에 대 한 직접 호출을 포함 하는 indexjavascript. cshtml에서 동등한 쿼리 논리를 찾을 수 있습니다. 

두 언어 버전 모두에 대 한 프런트 엔드 사용자 환경은 [JQUERY UI](https://jqueryui.com/autocomplete/) 및 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) 라이브러리를 기반으로 합니다. 이러한 라이브러리를 사용 하 여 제안과 자동 완성을 모두 지 원하는 검색 상자를 빌드합니다. 검색 상자에 수집 된 입력은 HomeController.cs 또는 IndexJavaScript. cshtml에 정의 된 것과 같은 제안 및 자동 완성 작업과 쌍을 이룹니다.

이 연습에서는 다음 작업을 안내 합니다.

> [!div class="checklist"]
> * JavaScript에서 검색 입력 상자를 구현 하 고 제안 된 일치 또는 자동 완성 용어에 대 한 요청을 실행 합니다.
> * 에서 C#HomeController.cs의 제안 및 자동 완성 작업을 정의 합니다.
> * JavaScript에서는 REST Api를 직접 호출 하 여 동일한 기능을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

솔루션은 준비 된 NYCJobs demo 인덱스를 호스팅하는 라이브 샌드박스 서비스를 사용 하기 때문에이 연습에서는 Azure Search 서비스를 선택 해야 합니다. 사용자 고유의 검색 서비스에서이 예제를 실행 하려면 [NYC 작업 인덱스 구성](#configure-app) 에서 지침을 참조 하세요.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

* [DotNetHowToAutoComplete 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)을 다운로드 합니다.

이 샘플은 제안, 자동 완성, 패싯 탐색, 클라이언트 쪽 캐싱 등 포괄적입니다. 이 샘플에서 제공 하는 내용에 대 한 자세한 설명은 추가 정보 및 설명을 참조 하세요.

## <a name="run-the-sample"></a>샘플 실행

1. Visual Studio에서 **AutocompleteTutorial** 을 엽니다. 솔루션에는 NYC Jobs 데모 인덱스에 연결 된 ASP.NET MVC 프로젝트가 포함 되어 있습니다.

2. 원하는 브라우저에서 F5를 눌러 프로젝트를 실행하고 페이지를 로드합니다.

맨 위에 C# 또는 JavaScript를 선택하는 옵션이 표시됩니다. 옵션 C# 은 브라우저에서 HomeController을 호출 하 고 AZURE SEARCH .net SDK를 사용 하 여 결과를 검색 합니다. 

브라우저에서 직접 Azure Search REST API를 호출하는 JavaScript 옵션입니다. 이 옵션은 컨트롤러를 흐름 밖으로 꺼내기 때문에 일반적으로 성능이 눈에 띄게 높습니다. 요구 사항 및 언어 기본 설정에 부합하는 옵션을 선택할 수 있습니다. 각각에 대한 지침이 있는 페이지에 몇 가지 자동 완성 예제가 있습니다. 예제마다 테스트해볼 수 있는 몇 가지 권장 샘플 텍스트가 있습니다.  

각 검색 상자에 몇 자 입력해보고 결과를 확인합니다.

## <a name="search-box"></a>검색 상자

및 JavaScript C# 버전 모두에서 검색 상자 구현은 정확히 동일 합니다. 

\Views\Home 폴더 아래에 있는 파일을 열어 코드를 확인 합니다.

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

이 예제는 스타일 지정에 대 한 클래스, JavaScript에서 참조할 ID 및 자리 표시자 텍스트를 사용 하는 간단한 입력 텍스트 상자입니다.  매직은 포함 된 JavaScript에 있습니다.

Language C# 샘플은 JQuery에서 JavaScript를 사용 하 여 [JQuery UI 자동 완성 라이브러리](https://jqueryui.com/autocomplete/)를 활용 합니다. 이 라이브러리는 MVC 컨트롤러에 대 한 비동기 호출을 수행 하 여 제안을 검색 하는 방식으로 검색 상자에 자동 완성 환경을 추가 합니다. JavaScript 언어 버전은 IndexJavaScript. cshtml에 있습니다. 여기에는 검색 표시줄에 대 한 아래의 스크립트와 Azure Search에 대 한 REST API 호출이 포함 됩니다.

JQuery UI 자동 완성 함수를 호출 하 여 제안 요청을 전달 하는 첫 번째 예제에 대 한 JavaScript 코드를 살펴보겠습니다.

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

위의 코드는 페이지 로드 시 브라우저에서 실행 되어 "example1a" 입력 상자에 대 한 jQuery UI 자동 완성을 구성 합니다.  `minLength: 3`은 검색 상자에 3자 이상이 입력되었을 때만 권장 사항을 표시하도록 합니다.  원본 값이 중요합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

위의 줄에서는 검색 상자 아래에 표시할 항목 목록을 가져오는 jQuery UI 자동 완성 기능을 제공 합니다. 이 프로젝트는 MVC 프로젝트 이므로 쿼리 제안을 반환 하는 논리를 포함 하는 HomeController.cs의 제안 함수를 호출 합니다 (다음 섹션에서 제안 하는 방법에 대 한 자세한 정보). 이 함수는 또한 몇 가지 매개 변수를 전달 하 여 강조 표시, 유사 항목 일치 및 용어를 제어 합니다. 자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>샘플을 확장하여 유사 일치 지원

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다. 반드시 필요한 것은 아니지만,이를 통해 사전에 제공 되는 환경의 견고성을 크게 향상 시킬 수 있습니다. 소스 줄을 변경하여 유사 일치를 사용하도록 테스트해 보겠습니다.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Azure Search 자동 완성으로 지원 되는 jQuery 자동 완성

지금까지 검색 UX 코드는 제안에 집중 되어 있습니다. 다음 코드 블록은 jQuery UI 자동 완성 함수 (index. cshtml의 줄 91)를 보여주고 Azure Search 자동 완성 요청을 전달 합니다.

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

이제 웹 페이지의 JavaScript 코드를 검토 했으므로 Azure Search .NET SDK를 사용 하 여 제안 된 C# 일치 항목을 실제로 검색 하는 서버측 컨트롤러 코드를 살펴보겠습니다.

Controller 디렉터리에서 **HomeController.cs** 파일을 엽니다. 

가장 먼저 알 수 있는 것은 라는 `InitSearch`클래스 맨 위에 있는 메서드입니다. 이 메서드는 Azure Search 서비스에 대 한 인증 된 HTTP 인덱스 클라이언트를 만듭니다. 자세한 내용은 [.Net 응용 프로그램에서 Azure Search를 사용 하는 방법](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)을 참조 하세요.

41 줄에서 제안 함수를 확인 합니다. [DocumentsOperationsExtensions 메서드](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)를 기반으로 합니다.

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

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다. 메서드는 제안 API에 전달 되는 [SuggestParameters 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

69 줄에서 자동 완성 함수를 확인 합니다. [DocumentsOperationsExtensions 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)를 기반으로 합니다.

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

자동 완성 함수는 검색어 입력을 사용 합니다. 메서드는 [Autocompleteparameters 개체](https://docs.microsoft.com/rest/api/searchservice/autocomplete)를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.

(선택 사항) Suggest 함수의 시작 부분에 중단점을 추가하고 코드를 단계별로 확인합니다. SDK에서 반환 된 응답과 메서드에서 반환 된 결과로 변환 되는 방법에 대해 확인 합니다.

페이지의 다른 예에서는 동일한 패턴을 따라 적중 항목 강조 표시와 패싯을 추가 하 여 자동 완성 결과의 클라이언트 쪽 캐싱을 지원 합니다. 각각을 검토하여 작동 방식과 검색 환경에서의 활용 방법을 살펴봅니다.

## <a name="javascript-example"></a>JavaScript 예제

자동 완성 및 제안의 Javascript 구현은 URI를 소스로 사용 하 여 인덱스와 작업을 지정 하는 REST API를 호출 합니다. 

JavaScript 구현을 검토 하려면 **Indexjavascript. cshtml**를 엽니다. JQuery UI 자동 완성 함수는 검색 상자에도 사용 되며, 검색 용어 입력을 수집 하 고, Azure Search에 대 한 비동기 호출을 수행 하 여 제안 된 일치 항목 또는 완료 된 용어를 검색 합니다. 

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

이 예를 홈 컨트롤러를 호출 하는 위의 예제와 비교 하는 경우 여러 유사성을 확인할 수 있습니다.  `minLength` 및 `position`에 대한 자동 완성 구성은 정확히 동일합니다. 

여기서 큰 변화는 소스입니다. Home 컨트롤러에서 제안 메서드를 호출 하는 대신, JavaScript 함수에서 REST 요청을 만들고 Ajax를 사용 하 여 실행 합니다. 그러면 응답이 "성공" 처리되고 소스로 사용됩니다.

REST 호출에서 Uri를 사용 하 여 [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 또는 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 호출을 수행 하는지 여부를 지정 합니다. 다음 Uri는 각각 9와 10 줄에 있습니다.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

148 줄에서를 호출 `autocompleteUri`하는 스크립트를 찾을 수 있습니다. 에 대 한 `suggestUri` 첫 번째 호출은 39 줄에 있습니다.

> [!Note]
> JavaScript에서 서비스에 대 한 REST 호출을 REST API에 대 한 편리한 데모로 제공 하지만 모범 사례 또는 권장 사항으로 사실을 진술한 것 해서는 안 됩니다. 스크립트에 API 키와 끝점이 포함 되어 있으면 스크립트에서 해당 값을 읽을 수 있는 모든 사용자에 대 한 서비스 거부 공격까지 서비스를 엽니다. 학습 목적으로 JavaScript를 사용 하는 것이 안전 하지만 무료 서비스에서 호스트 되는 인덱스의 경우에는 Java C# 또는를 사용 하 여 프로덕션 코드의 인덱싱 및 쿼리 작업을 수행 하는 것이 좋습니다. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>서비스에서 실행 되도록 NYCJobs 구성

지금 까지는 호스트 된 NYCJobs demo 인덱스를 사용 했습니다. 인덱스를 포함 하 여 모든 코드에 대 한 완전 한 가시성을 원하는 경우 다음 지침에 따라 사용자의 검색 서비스에서 인덱스를 만들고 로드 합니다.

1. [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 예제에서는 무료 서비스를 사용할 수 있습니다. 

   > [!Note]
   > 체험 Azure Search 서비스를 사용하는 경우 세 가지 인덱스로 제한됩니다. NYCJobs 데이터 로더는 두 개의 인덱스를 만듭니다. 서비스에 새 인덱스를 허용하는 공간이 있는지 확인합니다.

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 코드를 다운로드 합니다.

1. NYCJobs 샘플 코드의 DataLoader 폴더에서 Visual Studio에서 **DataLoader** 를 엽니다.

1. Azure Search 서비스에 대 한 연결 정보를 추가 합니다. DataLoader 프로젝트 안에서 App.config를 열고 Azure Search 서비스 및 Azure Search Service API 키에 맞게 TargetSearchServiceName 및 TargetSearchServiceApiKey appSettings를 변경합니다. 이 정보는 Azure Portal에서 찾을 수 있습니다.

1. F5 키를 눌러 응용 프로그램을 시작 하 고 두 개의 인덱스를 만들고 NYCJob 샘플 데이터를 가져옵니다.

1. **AutocompleteTutorial** 를 열고 **AutocompleteTutorial** 프로젝트에서 web.config를 편집 합니다. SearchServiceName 및 SearchServiceApiKey 값을 검색 서비스에 유효한 값으로 변경 합니다.

1. F5 키를 눌러 애플리케이션을 실행합니다. 샘플 웹 앱이 기본 브라우저에서 열립니다. 이 환경은 sandbox 버전과 동일 합니다. 인덱스와 데이터만 서비스에서 호스팅됩니다.

## <a name="next-steps"></a>다음 단계

이 예제에서는 자동 완성 및 제안 사항을 지 원하는 검색 상자를 빌드하기 위한 기본 단계를 보여 줍니다. ASP.NET MVC 응용 프로그램을 빌드하고 Azure Search .NET SDK 또는 REST API를 사용 하 여 제안을 검색 하는 방법을 살펴보았습니다.

다음 단계로 제안 및 자동 완성 기능을 검색 환경에 통합 해 보세요. 다음 참조 문서는 도움이 될 것입니다.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Create Index REST API에서 패싯 인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index)

