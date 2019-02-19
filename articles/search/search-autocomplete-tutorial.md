---
title: 검색 상자에 자동 완성을 추가하는 방법에 대한 자습서 - Azure Search
description: Azure Search 자동 완성 및 제안 API를 사용하여 데이터 중심 애플리케이션의 최종 사용자 환경을 개선하는 방법을 보여주는 예제입니다.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: de48f3129beba31f80f5bd4d0c131b28f2b1c91a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997169"
---
# <a name="tutorial-add-autocomplete-to-your-search-box-using-azure-search"></a>자습서: Azure Search를 사용하여 검색 상자에 자동 완성 추가

이 자습서에서는 [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) 및 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet)의 [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions), [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 및 [패싯](search-faceted-navigation.md)을 사용하여 강력한 검색 상자를 구성하는 방법을 알아봅니다. 

+ *제안*은 사용자가 지금까지 입력한 내용을 바탕으로 실제 결과를 추천합니다. 
+ Azure Search의 [새 미리 보기 기능](search-api-preview.md)인 *자동 완성*은 인덱스의 용어를 제공하여 사용자가 현재 입력 중인 항목을 완성합니다. 

사용자가 입력할 때 다양한 검색어를 직접 제공하여 사용자 생산성을 높이는 여러 기법을 비교해 보겠습니다.

이 자습서에서는 C#을 사용하여 [Azure Search.NET 클라이언트 라이브러리](https://aka.ms/search-sdk)를 호출하고, JavaScript를 사용하여 Azure Search REST API를 직접 호출하는 ASP.NET MVC 기반 애플리케이션을 사용합니다. 이 자습서용 애플리케이션은 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 데이터를 입력하는 인덱스를 대상으로 합니다. NYC Jobs 데모에서 이미 구성된 인덱스를 사용하거나, NYCJobs 샘플 솔루션에서 데이터 로더를 사용하여 자체 인덱스를 입력할 수 있습니다. 이 샘플은 [jQuery UI](https://jqueryui.com/autocomplete/) 및 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) JavaScript 라이브러리를 사용하여 자동 완성을 지원하는 검색 상자를 빌드합니다. Azure Search와 함께 이러한 구성 요소를 사용하여 검색 상자에서 미리 입력을 통한 자동 완성을 지원하는 방법의 여러 예제를 확인합니다.

다음 작업을 수행합니다.

> [!div class="checklist"]
> * 솔루션 다운로드 및 구성
> * 애플리케이션 설정에 검색 서비스 정보 추가
> * 검색 입력 상자 구현
> * 원격 원본에서 가져오는 자동 완성 목록에 대한 지원 추가 
> * .Net SDK 및 REST API를 사용하여 제안 및 자동 완성 검색
> * 성능 개선을 위해 클라이언트 쪽 캐싱 지원 

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2017. [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) 평가판을 사용할 수 있습니다. 

* 자습서용 샘플 [소스 코드](https://github.com/azure-samples/search-dotnet-getting-started)를 다운로드합니다.

* (선택 사항) 활성 Azure 계정 및 Azure Search 서비스. Azure 계정이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 서비스 프로비전에 대한 도움말은 [검색 서비스 만들기](search-create-service-portal.md) 를 참조하세요. 이 자습서는 다른 데모에서 기존에 만든 호스팅되는 NYCJobs 인덱스를 사용하여 완료할 수 있으므로 계정과 서비스는 선택 사항입니다.

* (선택 사항) 자체 Azure Search 서비스에서 인덱스에 NYCJobs 데이터를 가져오는 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 코드를 다운로드합니다.

> [!Note]
> 체험 Azure Search 서비스를 사용하는 경우 세 가지 인덱스로 제한됩니다. NYCJobs 데이터 로더는 두 개의 인덱스를 만듭니다. 서비스에 새 인덱스를 허용하는 공간이 있는지 확인합니다.

### <a name="set-up-azure-search-optional"></a>Azure Search 설정(선택 사항)

자체 인덱스로 NYCJobs 샘플 애플리케이션에 대한 데이터를 가져오려면 이 섹션의 단계를 따릅니다. 이 단계는 선택 사항입니다.  제공된 샘플 인덱스를 사용하려면 다음 섹션으로 건너뛰어 샘플을 실행합니다.

1. NYCJobs 샘플 코드의 DataLoader 폴더의 DataLoader.sln 솔루션 파일을 Visual Studio에서 엽니다.

1. Azure Search 서비스에 대한 연결 정보를 업데이트합니다.  DataLoader 프로젝트 안에서 App.config를 열고 Azure Search 서비스 및 Azure Search Service API 키에 맞게 TargetSearchServiceName 및 TargetSearchServiceApiKey appSettings를 변경합니다.  이 정보는 Azure Portal에서 찾을 수 있습니다.

1. F5 키를 눌러 애플리케이션을 시작합니다.  그러면 두 인덱스를 만들고 NYCJob 샘플 데이터를 가져옵니다.

1. 자습서 샘플 코드의 AutocompleteTutorial.sln 솔루션 파일을 Visual Studio에서 엽니다.  AutocompleteTutorial 프로젝트 내에서 Web.config를 열고 SearchServiceName 및 SearchServiceApiKey 값을 위와 동일하게 변경합니다.

### <a name="running-the-sample"></a>샘플 실행

이제 자습서 샘플 애플리케이션을 실행할 준비가 되었습니다.  AutocompleteTutorial.sln 솔루션 파일을 Visual Studio에서 열어 자습서를 실행합니다.  솔루션은 ASP.NET MVC 프로젝트를 포함합니다.  원하는 브라우저에서 F5를 눌러 프로젝트를 실행하고 페이지를 로드합니다.  맨 위에 C# 또는 JavaScript를 선택하는 옵션이 표시됩니다.  C# 옵션은 브라우저에서 HomeController를 호출하고 Azure Search .Net SDK를 사용하여 결과를 검색합니다.  브라우저에서 직접 Azure Search REST API를 호출하는 JavaScript 옵션입니다.  이 옵션은 컨트롤러를 흐름 밖으로 꺼내기 때문에 일반적으로 성능이 눈에 띄게 높습니다.  요구 사항 및 언어 기본 설정에 부합하는 옵션을 선택할 수 있습니다.  각각에 대한 지침이 있는 페이지에 몇 가지 자동 완성 예제가 있습니다.  예제마다 테스트해볼 수 있는 몇 가지 권장 샘플 텍스트가 있습니다.  각 검색 상자에 몇 자 입력해보고 결과를 확인합니다.

## <a name="how-this-works-in-code"></a>코드에서의 작동 방식

브라우저에서 예제를 살펴보았으므로 이제 관련 구성 요소와 작동 방식을 검토하기 위해 첫 번째 예제를 상세히 살펴보겠습니다.

### <a name="search-box"></a>검색 상자

어떤 언어에서든 검색 상자는 정확하게 같습니다.  \Views\Home 폴더 아래 Index.cshtml 파일을 엽니다. 검색 상자 자체는 간단합니다.

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

스타일을 위한 클래스, JavaScript 참조 ID 및 자리 표시자 텍스트가 있는 간단한 입력 텍스트 상자입니다.  비밀은 Javascript에 있습니다.

### <a name="javascript-code-c"></a>JavaScript 코드(C#)

C# 언어 샘플에서는 Index.cshtml의 JavaScript를 사용하여 jQuery UI Autocomplete 라이브러리를 활용합니다.  이 라이브러리는 권장 사항을 검색하는 MVC 컨트롤러를 비동기 방식으로 호출하여 검색 상자에 자동 완성 환경을 추가합니다.  첫 번째 예제의 JavaScript 코드를 살펴보겠습니다.

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

이 코드는 "example1a" 입력 상자에 대한 자동 완성을 구성하기 위해 페이지를 로드할 때 브라우저에서 실행됩니다.  `minLength: 3`은 검색 상자에 3자 이상이 입력되었을 때만 권장 사항을 표시하도록 합니다.  원본 값이 중요합니다.

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

이 줄은 자동 완성 API에게 항목 목록을 가져올 위치를 알려 검색 상자 아래에 표시하게 합니다.  MVC 프로젝트이므로 HomeController.cs에서 Suggest 함수를 호출합니다.  자세한 내용은 다음 섹션에서 살펴보겠습니다.  강조 표시, 유사 일치, 조건을 제어하는 몇 가지 매개 변수도 전달합니다.  자동 완성 JavaScript API는 용어 매개 변수를 추가합니다.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>샘플을 확장하여 유사 일치 지원

유사 항목 검색을 사용하면 사용자가 검색 상자에 단어 철자를 틀리게 입력하더라도 가까운 일치를 기준으로 결과를 가져올 수 있습니다.  소스 줄을 변경하여 유사 일치를 사용하도록 테스트해 보겠습니다.

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

### <a name="homecontrollercs-c"></a>HomeController.cs(C#)

샘플에 대한 JavaScript 코드를 검토했으므로 Azure Search .NET SDK를 사용하여 실제로 권장 사항을 검토하는 C# 컨트롤러 코드를 살펴보겠습니다.

1. Controllers 디렉터리에서 HomeController.cs 파일을 엽니다. 

1. InitSearch라고 하는 클래스 맨 위의 메서드가 가장 먼저 보일 것입니다.  이 항목은 Azure Search 서비스에 대해 인증된 HTTP 인덱스 클라이언트가 만듭니다.  이 과정에 대해 자세히 알아보려면 다음 자습서를 살펴보세요. [.NET 애플리케이션에서 Azure Search를 사용하는 방법](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Suggest 함수로 이동합니다.

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

Suggest 함수에는 적중 강조 표시를 반환할지 또는 검색어 이력과 함께 유사 일치를 사용할지 결정하는 두 매개 변수가 있습니다.  이 메서드는 Suggest API로 전달될 SuggestParameters 개체를 만듭니다. 그러면 클라이언트에 표시될 수 있게 결과가 JSON으로 변환됩니다.
(선택 사항) Suggest 함수의 시작 부분에 중단점을 추가하고 코드를 단계별로 확인합니다.  SDK에서 반환하는 응답과, 메서드에서 반환된 결과로 변환되는 방식을 확인합니다.

페이지의 다른 예제는 자동 완성 결과의 클라이언트 쪽 캐싱을 지원하기 위해 동일한 패턴에 따라 적중 강조 표시, 자동 완성 권장 사항을 위한 미리 입력 및 패싯을 추가합니다.  각각을 검토하여 작동 방식과 검색 환경에서의 활용 방법을 살펴봅니다.

### <a name="javascript-language-example"></a>JavaScript 언어 예제

JavaScript 언어 예제의 경우IndexJavaScript.cshtml 페이지의 JavaScript 코드에서는 jQuery UI Autocomplete를 사용합니다.  이것은 그럴 듯한 검색 상자를 표시하는 데 있어 대부분의 중요한 작업을 수행하고 권장 사항 검색을 위해 Azure Search에 대한 간편한 비동기 호출을 제공하는 라이브러리입니다.  첫 번째 예제의 JavaScript 코드를 살펴보겠습니다.

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

이 코드를, Home 컨트롤러를 호출하는 위의 에제와 비교해 보면 몇 가지 유사점을 확인할 수 있습니다.  `minLength` 및 `position`에 대한 자동 완성 구성은 정확히 동일합니다.  여기서 큰 변화는 소스입니다.  홈 컨트롤러에서 Suggest 메서드를 호출하는 대신 JavaScript 함수에서 REST 요청을 만들어 ajax를 통해 실행합니다.  그러면 응답이 "성공" 처리되고 소스로 사용됩니다.

## <a name="takeaways"></a>핵심 내용

이 자습서에서는 자동 완성 및 제안을 지원하는 검색 상자 빌드를 위한 기본 단계를 보여줍니다.  ASP.NET MVC 애플리케이션을 빌드하고 Azure Search .Net SDK 또는 REST API를 사용하여 제안을 검색하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

제안 및 자동 완성을 검색 환경에 통합합니다.  .NET SDK 또는 REST API를 직접 사용하면 사용자 입력에서의 생산성 증대를 위해 Azure Search가 어떻게 도움이 될 수 있는지 살펴봅니다.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Create Index REST API에서 패싯 인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index)

