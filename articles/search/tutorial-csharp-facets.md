---
title: 탐색을 지원하기 위한 패싯 사용에 관한 C# 자습서
titleSuffix: Azure Cognitive Search
description: "\"페이징 결과\"를 계속 진행하여 패싯 탐색을 추가합니다. 패싯을 사용하여 검색 범위를 손쉽게 좁힐 수 있는 방법을 알아봅니다."
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ab15af07c5f63d375d8fdb4fc38e0853e207a0be
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667285"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 패싯 탐색 추가

패싯은 결과를 필터링할 수 있는 링크 세트를 제공하여 자기 주도형 탐색을 가능하게 합니다. 이 자습서에서 패싯 탐색 구조는 페이지의 왼쪽에 배치되고, 레이블 및 클릭 가능한 텍스트를 사용하여 결과를 트리밍합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 모델 속성을 _IsFacetable_로 설정
> * 패싯 탐색을 앱에 추가

## <a name="overview"></a>개요

패싯은 검색 인덱스의 필드를 기반으로 합니다. facet=[string]을 포함하는 쿼리 요청은 패싯 기준이 되는 필드를 제공합니다. `&facet=category&facet=amenities`처럼 여러 패싯을 포함하고 각 패싯을 앰퍼샌드(&) 문자로 구분하는 것이 일반적입니다. 패싯 탐색 구조를 구현하려면 패싯과 필터를 모두 지정해야 합니다. 필터는 결과의 범위를 좁히는 클릭 이벤트에 사용됩니다. 예를 들어 "예산"을 클릭하면 해당 조건에 따라 결과가 필터링됩니다.

이 자습서에서는 [검색 결과에 페이징 추가 ](tutorial-csharp-paging.md) 자습서에서 만든 페이징 프로젝트를 확장합니다.

이 자습서의 완성된 최종 코드 버전은 다음 프로젝트에서 찾을 수 있습니다.

* [4-add-facet-navigation(GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>필수 구성 요소

* [2a-add-paging(GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) solution. 이 프로젝트는 이전 자습서에서 빌드한 개발자 고유의 버전일 수도 있고 GitHub의 복사본일 수도 있습니다.

이 자습서는 [Azure.Search.Documents(버전 11)](https://www.nuget.org/packages/Azure.Search.Documents/) 패키지를 사용하도록 업데이트되었습니다. 이전 버전의 .NET SDK는 [Microsoft.Azure.Search(버전 10) 코드 샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)을 참조하세요.

## <a name="set-model-properties-as-isfacetable"></a>모델 속성을 IsFacetable로 설정

모델 속성을 패싯 검색에 배치하려면 **IsFacetable**로 태그를 지정해야 합니다.

1. **Hotel** 클래스를 검사합니다. 예를 들어 **Category** 및 **Tags**는 **IsFacetable**로 태그가 지정되지만 **HotelName** 및 **Description**은 해당되지 않습니다. 

    ```cs
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
    ```

1. 이 자습서의 일부로 태그를 변경하지 않을 것이므로 hotel.cs 파일을 변경하지 않고 닫습니다.

    > [!Note]
    > 검색에서 요청한 필드에 적절한 태그가 지정되지 않은 경우 패싯 검색은 오류를 throw합니다.

## <a name="add-facet-navigation-to-your-app"></a>패싯 탐색을 앱에 추가

이 예제에서 사용자가 결과의 왼쪽에 표시된 링크 목록에서 호텔의 한 범주 또는 하나의 편의 시설을 선택하도록 설정해보겠습니다. 사용자는 먼저 일부 검색 텍스트를 입력한 다음, 범주 또는 편의 시설을 선택하여 검색 결과의 범위를 좁힙니다.

패싯 목록을 보기로 전달하는 것은 컨트롤러의 몫입니다. 검색이 진행되는 동안 사용자가 선택한 내용을 그대로 유지할 수 있도록, 임시 스토리지를 상태 보존 메커니즘으로 사용합니다.

![패싯 탐색을 사용하여 “풀” 검색의 범위를 좁히기](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>SearchData 모델에 필터 문자열 추가

1. SearchData.cs 파일을 열고 문자열 속성을 **SearchData** 클래스에 추가하여 패싯 필터 문자열을 저장합니다.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>패싯 동작 메서드 추가

홈 컨트롤러에는 하나의 새 작업 **Facet**이 필요하며, **RunQueryAsync** 메서드로 업데이트하고 기존 **Index** 및 **Page** 작업으로 업데이트합니다.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. **Index(SearchData 모델)** 작업 메서드를 바꿉니다.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. **PageAsync(SearchData 모델)** 작업 메서드를 바꿉니다.

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. **FacetAsync(SearchData 모델)** 작업 메서드를 사용하여 사용자가 패싯 링크를 클릭하면 활성화되도록 합니다. 모델에는 범주 또는 편의 시설 검색 필터가 포함됩니다. 이러한 필터를 **PageAsync** 작업 뒤에 추가합니다.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>검색 필터 설정

예를 들어 사용자가 특정 패싯을 선택할 때 **리조트 및 스파** 범주를 클릭하면 이 범주로 지정된 호텔만 결과에서 반환되어야 합니다. 이러한 방식으로 검색 범위를 좁히려면 _필터_를 설정해야 합니다.

1. **RunQueryAsync** 메서드를 다음 코드로 바꿉니다. 기본적으로 범주 필터 문자열과 편의 시설 필터 문자열이 필요하며 **SearchOptions**의 **Filter** 매개 변수를 설정합니다.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    반환할 **Select** 항목의 목록에 **Category** 및 **Tags** 속성이 추가됩니다. 이 추가 사항은 패싯 탐색이 작동하기 위한 요건은 아니지만, 필터가 올바르게 작동하는지 확인하기 위해 이 정보를 사용합니다.

### <a name="add-lists-of-facet-links-to-the-view"></a>패싯 링크의 목록을 보기에 추가

보기에는 몇 가지 중요한 변경 사항이 필요합니다. 

1. hotels.css 파일(wwwroot/css 폴더에 있음)을 열어 시작한 후 다음 클래스를 추가합니다.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. 보기의 경우 출력을 테이블로 구성하여 왼쪽에 있는 패싯 목록과 오른쪽에 있는 결과에 맞게 깔끔하게 정렬합니다. index.cshtml 파일을 엽니다. HTML &lt;body&gt; 태그의 전체 콘텐츠를 다음 코드로 바꿉니다.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    **Html.ActionLink** 호출을 사용한다는 점에 유의하세요. 이 호출은 사용자가 패싯 링크를 클릭할 때 컨트롤러에 유효한 필터 문자열을 전달합니다. 

### <a name="run-and-test-the-app"></a>앱 실행 및 테스트

패싯 탐색이 사용자에게 좋은 점은 클릭 한 번으로 검색 범위를 좁힐 수 있다는 것입니다. 다음 순서에서 확인할 수 있습니다.

1. 앱을 실행하고 검색 텍스트로 “공항”을 입력합니다. 패싯 목록이 왼쪽에 깔끔하게 나타나는지 확인합니다. 이러한 패싯은 텍스트 데이터에 “공항”이 있는 호텔에 모두 적용되며, 얼마나 자주 발생하는지를 나타내는 횟수와 함께 표시됩니다.

    ![“공항” 검색 범위를 좁히기 위해 패싯 탐색 사용](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. **리조트 및 스파** 범주를 클릭합니다. 모든 결과가 이 범주에 있는지 확인합니다.

    ![검색 범위를 “리조트 및 스파”로 좁히기](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. **유럽식 아침 식사** 편의 시설을 클릭합니다. 모든 결과가 여전히 선택한 편의 시설과 함께 “리조트 및 스파” 범주에 있는지 확인합니다.

    ![검색 범위를 “유럽식 아침 식사”로 좁히기](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. 다른 범주를 선택해본 다음, 편의 시설을 하나 선택하고 좁혀진 결과를 확인합니다. 그런 다음, 다른 방법을 시도해보고, 편의 시설을 하나 선택한 다음, 범주를 하나 선택합니다. 빈 검색을 전송하여 페이지를 초기화합니다.

    >[!Note]
    > 패싯 목록(예: 범주)에서 한 항목을 선택하면 범주 목록 내에서 이전의 모든 선택을 재정의합니다.

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* 패싯 탐색에 포함할 각 패싯 가능 필드를 **IsFacetable** 속성으로 표시해야 합니다.
* 결과를 줄이기 위해 패싯이 필터와 결합됩니다.
* 패싯은 누적되며, 결과 범위를 더 좁히기 위해 각 선택 항목은 이전 선택 항목 위에 작성됩니다.

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 결과 정렬에 대해 살펴봅니다. 이 시점까지 결과는 단순히 데이터베이스에 있는 순서대로 정렬됩니다.

> [!div class="nextstepaction"]
> [C# 자습서: 결과 정렬 - Azure Cognitive Search](tutorial-csharp-orders.md)
