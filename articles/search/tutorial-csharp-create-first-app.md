---
title: 첫 번째 앱 만들기 C# 자습서
titleSuffix: Azure Cognitive Search
description: 첫 번째 C# 검색 앱을 단계별로 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 GitHub의 작동하는 앱 다운로드 링크와 앱을 처음부터 빌드하는 전체 프로세스의 링크를 제공합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2414570a1d483cd7630e628b13c92dbdc331370d
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759138"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 첫 번째 검색 앱 만들기

이 자습서에서는 Azure Cognitive Search 및 Visual Studio를 사용하여 검색 인덱스에서 결과를 쿼리하고 반환하는 웹앱을 만드는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 구조 모델링
> * 쿼리 입력을 수집하고 결과를 표시하는 웹 페이지 만들기
> * 검색 방법 정의
> * 앱 테스트

또한 검색 호출이 얼마나 간단한지 알아보겠습니다. 여러분이 개발할 코드의 핵심 명령문이 다음 몇 줄에 캡슐화되어 있습니다.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

한 번만 호출하면 인덱스를 쿼리하고 결과를 반환합니다.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="*풀* 검색" border="true":::

## <a name="overview"></a>개요

이 자습서에서는 여러분이 요청에 대한 쿼리 문자열을 수집하고 결과를 반환하는 검색 페이지를 만드는 데 집중할 수 있도록 기존의 호스트된 샘플 인덱스를 사용합니다. 인덱스에는 가상의 호텔 데이터가 포함되어 있습니다. 기본 페이지를 만든 후에는 후속 단원에서 페이징, 패싯 및 자동 완성 환경을 포함하도록 기본 페이지를 개선할 수 있습니다.

이 자습서의 완성된 최종 코드 버전은 다음 프로젝트에서 찾을 수 있습니다.

* [1-basic-search-page(GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

이 자습서는 Azure.Search.Documents(버전 11) 패키지를 사용하도록 업데이트되었습니다. 이전 버전의 .NET SDK는 [Microsoft.Azure.Search(버전 10) 코드 샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Microsoft에서 호스트하는 공용 샘플 검색 인덱스를 사용 중이기 때문에 이 자습서에서는 검색 서비스 또는 Azure 계정이 필요 없습니다.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure Cognitive Search 클라이언트 라이브러리(버전 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>GitHub에서 프로젝트를 설치하고 실행

작동 중인 앱으로 바로 이동하려면 아래 단계에 따라 완성된 코드를 다운로드하고 실행합니다.

1. GitHub에서 [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples/v11)의 버전을 설치해도 됩니다.

1. [루트 폴더](https://github.com/Azure-Samples/azure-search-dotnet-samples)에서 **코드**를 선택한 다음, **복제** 또는 **ZIP 다운로드**를 선택하여 프로젝트의 프라이빗 로컬 복사본을 만듭니다.

1. Visual Studio를 사용하여 기본 검색 페이지("1-basic-search-page")의 솔루션을 찾아서 열고, **디버깅 없이 시작**을 선택(또는 F5 키)하여 프로그램을 빌드하고 실행합니다.

1. 이 인덱스는 호텔 인덱스이므로 호텔을 검색하는 데 사용할 수 있는 몇 가지 단어(예: "wifi", "뷰", "바", "주차")를 입력하고 결과를 검사합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="*풀* 검색" border="true":::

이 프로젝트가 원활하게 진행되어 웹앱이 실행되기를 바랍니다. 보다 세련된 검색 기능을 구현하기 위한 여러 필수 구성 요소가 이 앱 하나에 포함되어 있으므로, 이 앱을 살펴보고 단계별로 만들어 보는 것이 좋습니다. 다음 섹션에서는 이러한 단계를 설명합니다.

## <a name="set-up-a-development-environment"></a>개발 환경 설정

이 프로젝트를 처음부터 새로 만들고 마음 속에 그리고 있는 Azure Cognitive Search 개념을 강화하려면 Visual Studio 프로젝트로 시작하세요.

1. Visual Studio에서 **새로 만들기** > **프로젝트**를 선택한 다음, **ASP.NET Core 웹 애플리케이션**을 선택합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="*풀* 검색" border="true":::

1. 프로젝트 이름(예: "FirstSearchApp")을 지정하고 위치를 설정합니다. **만들기**를 선택합니다.

1. **웹 애플리케이션(Model-View-Controller)** 프로젝트 템플릿을 선택합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="*풀* 검색" border="true":::

1. 클라이언트 라이브러리를 설치합니다. **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리...** 에서 **찾아보기**를 선택한 다음, "azure.search.documents"를 검색합니다. **Azure.Search.Documents**(버전 11 이상)를 설치하고, 라이선스 계약 및 종속성에 동의합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="*풀* 검색" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search 초기화

이 샘플에서는 공개적으로 제공되는 호텔 데이터를 사용하겠습니다. 이 데이터는 오직 데모 데이터를 제공할 목적으로 만든 임의의 50개 가상 호텔 이름과 설명 컬렉션입니다. 이 데이터에 액세스하려면 이름 및 API 키를 지정합니다.

1. **appsettings.json** 파일을 열고 기본 줄을 다음 이름과 키로 바꿉니다. 여기에 표시된 API 키는 키 예제가 아니라, *정확하게* 호텔 데이터에 액세스할 때 필요한 키입니다. 이제 파일이 다음과 비슷할 것입니다.

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. 솔루션 탐색기에서 파일을 선택하고, [속성]에서 **출력 디렉터리로 복사** 설정을 **새 버전이면 복사**로 변경합니다.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="*풀* 검색" border="true":::

## <a name="model-data-structures"></a>데이터 구조 모델링

모델(C# 클래스)은 클라이언트(보기), 서버(컨트롤러), 그리고 MVC(모델, 보기, 컨트롤러) 아키텍처를 사용하는 Azure 클라우드 간에 데이터를 교환하는 데 사용됩니다. 일반적으로 이러한 모델은 액세스하는 데이터의 구조를 반영합니다.

이 단계에서는 보기/컨트롤러 통신에 사용되는 검색 문자열뿐 아니라 검색 인덱스의 데이터 구조를 모델링합니다. 호텔 인덱스에서, 각 호텔에는 여러 개의 객실이 있고 각 호텔의 주소는 여러 부분으로 구성되어 있습니다. 이러한 것들이 합쳐진 호텔의 완전한 표현은 계층 구조와 중첩된 데이터 구조입니다. 각 구성 요소를 만들려면 다음과 같은 세 가지 클래스가 필요합니다.

**Hotel**, **Address** 및 **Room** 클래스 세트를 [*복합 형식*](search-howto-complex-data-types.md)이라고 하며, Azure Cognitive Search의 중요한 기능입니다. 복합 형식은 다양한 클래스 및 하위 클래스 레벨 깊이를 지원하며, *단순 형식*(기본 멤버만 포함하는 클래스)을 사용할 때보다 훨씬 복잡한 데이터 구조를 지원합니다.

1. 솔루션 탐색기에서 **모델** > **추가** > **새 항목**을 마우스 오른쪽 단추로 클릭합니다.

1. **클래스**를 선택하고 항목 이름을 Hotel.cs로 지정합니다. Hotel.cs의 모든 콘텐츠를 다음 코드로 바꿉니다. 클래스의 **Address** 및 **Room** 멤버를 보면, 필드 자체가 클래스이므로 필드에도 모델이 필요합니다.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. 똑같은 프로세스에 따라 **Address** 클래스에 대한 모델을 만들고 Address.cs 파일의 이름을 지정합니다. 콘텐츠를 다음으로 바꿉니다.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. 마찬가지로. 똑같은 프로세스에 따라 **Room** 클래스를 만들고 파일 이름을 Room.cs로 지정합니다.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. 이 자습서에서 만들 마지막 모델은 **SearchData**라는 클래스이며, 이 클래스는 사용자의 입력(**searchText**) 및 검색의 출력(**resultList**)을 나타냅니다. 출력 형식 **SearchResults&lt;Hotel&gt;** 이 매우 중요합니다. 이 형식은 검색 결과와 정확하게 일치하므로 이 참조를 보기에 전달해야 합니다. 기본 템플릿을 다음 코드로 바꿉니다.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>웹 페이지 만들기

프로젝트 템플릿은 **Views** 폴더에 여러 클라이언트 보기가 함께 제공됩니다. 정확한 보기는 사용하는 Core .NET 버전에 따라 달라집니다(이 샘플에서는 3.1 사용). 이 자습서에서는 검색 페이지의 요소를 포함하도록 **Index.cshtml**을 수정합니다.

Index.cshtml의 콘텐츠 전체를 삭제하고, 다음 단계에 따라 파일을 빌드합니다.

1. 이 자습서에서는 보기의 작은 이미지 두 개, 즉, Azure 로고와 검색 돋보기 아이콘(azure-logo.png 및 search.png)을 사용합니다. GitHub 프로젝트의 이미지를 프로젝트의 **wwwroot/images** 폴더로 복사합니다.

1. Index.cshtml의 첫 번째 줄은 클라이언트(보기)와 서버(컨트롤러) 간에 데이터를 교환하는 데 사용되는 모델, 즉, 앞에서 만든 **SearchData** 모델을 참조해야 합니다. 이 줄을 Index.cshtml 파일에 추가합니다.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. 보기의 제목을 입력하는 표준 연습이므로, 다음 줄은 아래와 같아야 합니다.

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. 제목 뒤에 HTML 스타일 시트의 참조를 입력합니다. 잠시 후에 만들겠습니다.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. 보기 본문은 두 가지 사용 사례를 처리합니다. 첫째, 처음 사용하는 경우 검색 텍스트를 입력하기 전에 빈 페이지를 제공해야 합니다. 둘째, 반복해서 쿼리할 수 있도록 검색 텍스트 상자에 추가하는 것 외에도 결과를 처리해야 합니다.

   이러한 두 가지 상황을 모두 처리하려면 보기에 제공된 모델이 Null인지 확인해야 합니다. Null 모델은 첫 번째 사용 사례(앱의 초기 실행)를 나타냅니다. 다음 명령을 Index.cshtml 파일에 추가하고 주석을 읽어봅니다.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. 스타일 시트를 추가합니다. Visual Studio의 **파일**> **새로 만들기** > **파일**에서 **스타일 시트**를 선택합니다(**일반**이 강조 표시된 상태로).

   기본 코드를 다음 코드로 바꿉니다. 이 파일을 자세히 살펴보지는 않겠습니다. 스타일은 표준 HTML입니다.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. 스타일 시트 파일을 기본 site.css 파일과 함께 **wwwroot/css** 폴더에 hotels.css로 저장합니다.

이것으로 보기가 완성되었습니다. 모델과 보기가 완성되었습니다. 이제 컨트롤러만 완성하면 모든 것이 연결됩니다.

## <a name="define-methods"></a>메서드 정의

이 단계에서는 **홈 컨트롤러**의 콘텐츠를 수정합니다.

1. HomeController.cs 파일을 열고 **using** 문을 다음과 같이 바꿉니다.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>인덱스 메서드 추가

MVC 앱에서 **Index()** 메서드는 모든 컨트롤러에 사용 가능한 기본 작업 메서드입니다. 이 메서드는 인덱스 HTML 페이지를 엽니다. 이 자습서에서는 애플리케이션 시작 사용 사례: 빈 검색 페이지 렌더링이라는 교육 목적을 달성하기 위해 매개 변수를 사용하지 않는 기본 메서드를 사용합니다.

이 섹션에서는 두 번째 사용 사례: 사용자가 검색 텍스트를 입력하면 페이지 렌더링을 지원하도록 메서드를 확장합니다. 이 사용 사례를 지원하기 위해 인덱스 메서드는 모델을 매개 변수로 사용하도록 확장됩니다.

1. 다음 메서드를 기본 **Index()** 메서드 뒤에 추가합니다.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    메서드의 **async** 선언과 **RunQueryAsync**에 대한 **await** 호출을 살펴봅니다. 두 키워드는 호출을 비동기적으로 처리하므로 서버의 스레드를 차단하지 않습니다.

    **catch** 블록은 기본적으로 생성되는 오류 모델을 사용합니다.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>오류 처리 및 기타 기본 보기와 메서드 확인

사용하는 .NET Core 버전에 따라 조금씩 다른 기본 보기 세트가 생성됩니다. .NET Core 3.1의 기본 보기는 인덱스, 개인 정보 및 오류입니다. 앱을 실행할 때 이러한 기본 페이지를 볼 수 있으며, 컨트롤러에서 어떻게 처리되는지 검사할 수 있습니다.

오류 보기는 이 자습서의 뒷부분에서 테스트하겠습니다.

GitHub 샘플에서 사용하지 않는 보기와 관련 작업이 삭제되었습니다.

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync 메서드 추가

Azure Cognitive Search 호출은 **RunQueryAsync** 메서드에 캡슐화됩니다.

1. 먼저 Azure 서비스를 설정하는 몇 가지 정적 변수를 추가하고, 이러한 정적 변수를 시작하는 호출을 추가합니다.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. 이제 **RunQueryAsync** 메서드 자체를 추가합니다.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    이 메서드에서는 먼저 Azure 구성이 시작되는지 확인한 다음, 검색 옵션을 설정합니다. **선택** 옵션은 결과에 반환할 필드를 지정하므로 **hotel** 클래스의 속성 이름과 일치합니다. **선택**을 생략하면 숨겨진 필드가 모두 반환되며, 가능한 모든 필드의 하위 집합에만 관심이 있는 경우에는 이것이 비효율적일 수 있습니다.

    검색에 대한 비동기 호출은 요청(**searchText**로 모델링) 및 응답(**searchResult**로 모델링)을 작성합니다. 이 코드를 디버깅할 때 **model.resultList**의 내용을 검사해야 하는 경우 **SearchResult** 클래스는 중단점을 설정하기에 적합한 후보입니다. 이 방법이 직관적이고, 요청한 데이터를 받을 수 있으며, 그 외의 데이터는 별로 반환되지 않는다는 것을 알 수 있습니다.

### <a name="test-the-app"></a>앱 테스트

이제 앱이 올바르게 실행되는지 확인하겠습니다.

1. **디버그** > **디버그하지 않고 시작**을 선택하거나 **F5** 키를 누릅니다. 앱이 예상대로 실행되면 초기 인덱스 보기가 제공됩니다.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="*풀* 검색" border="true":::

1. "해변"(또는 떠오르는 아무 텍스트) 등의 텍스트를 입력하고, 검색 아이콘을 클릭하여 요청을 보냅니다.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="*풀* 검색" border="true":::

1. "5성"을 입력합니다. 이 쿼리는 결과를 반환하지 않습니다. 보다 세련된 검색은 "5성"을 그와 동의어인 "고급"으로 처리하고 해당 결과를 반환하는 것입니다. [동의어](search-synonyms.md) 지원은 Azure Cognitive Search에서 제공되지만, 이 자습서 시리즈에서는 다루지 않습니다.

1. 검색 텍스트로 "hot"를 입력합니다. "hotel"이라는 단어가 포함된 항목이 반환되지 _않습니다_. 몇 가지 결과가 반환되기는 하지만, 현재 검색은 오직 전체 단어만 찾습니다.

1. "풀", "햇살", "보기" 등의 다른 단어를 입력해 봅니다. Azure Cognitive Search가 가장 간단하게 작동하지만, 여전히 설득력 있는 수준입니다.

## <a name="test-edge-conditions-and-errors"></a>에지 조건 및 오류 테스트

모든 것이 완벽하게 작동하더라도 오류 처리 기능이 정상적으로 작동하는지 확인해야 합니다. 

1. **Index** 메서드에서 **try {** 호출 뒤에 **Throw new Exception()** 줄을 입력합니다. 이 예외는 텍스트를 검색할 때 오류를 강제로 발생시킵니다.

2. 앱을 실행하고, 검색 텍스트로 "바"를 입력하고, 검색 아이콘을 클릭합니다. 예외가 오류 보기로 이어집니다.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="*풀* 검색" border="true":::

    > [!Important]
    > 오류 페이지의 내부 오류 번호를 반환하면 보안 위험이 높아집니다. 앱이 범용인 경우 오류 발생 시 무엇을 반환해야 하는지 보안 및 모범 사례를 조사하세요.

3. 오류 처리 작업에 만족하는 경우 **Throw new Exception()** 을 제거합니다.

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* Azure Cognitive Search 호출은 간결하며, 결과를 쉽게 해석할 수 있습니다.
* 비동기 호출은 컨트롤러의 복잡성이 살짝 증가하지만, 고품질의 앱을 개발하려는 경우 가장 좋은 방법입니다.
* 이 앱은 **searchOptions**에 설정된 정의에 따라 간단한 텍스트 검색을 수행했습니다. 그러나 이 클래스를 검색의 정교함을 높이는 여러 멤버로 채울 수 있습니다. 이 앱의 성능을 높이기 위해 그렇게 많은 추가 작업이 필요하지는 않습니다.

## <a name="next-steps"></a>다음 단계

사용자 환경을 개선하려면 더 많은 기능, 특히 페이징(페이지 번호 또는 무한 스크롤을 사용하여) 기능과 자동 완성/제안 기능을 추가합니다. 보다 정교한 검색 옵션(예: 특정 지점에서 지정된 반경 내에 있는 호텔의 지리적 위치 검색 및 검색 결과 정렬)도 고민해야 합니다.

이러한 다음 단계는 나머지 자습서에서 다룹니다. 페이징부터 시작하겠습니다.

> [!div class="nextstepaction"]
> [C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md)