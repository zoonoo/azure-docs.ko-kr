<properties 
   pageTitle="Azure 검색으로 Azure 앱 서비스 시도" 
   description="TryAzureAppService 템플릿을 사용하여 1시간까지 무료로 Azure 검색을 시도합니다." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>

# Azure 검색으로 Azure 앱 서비스 시도

[Azure 앱 서비스 시도](https://tryappservice.azure.com/)는 Azure 구독 등록 없이 최대 1시간 동안 Azure 검색과 같은 선택한 Azure 서비스를 완전히 무료로 시험 사용해볼 수 있는 새로운 방법입니다.

사이트에서 선택할 수 있는 여러 템플릿을 제공합니다. Azure 검색을 포함한 ASP.NET 템플릿을 선택하면 선택한 서비스에서 지원하는 모든 기능을 갖춘 웹 사이트에 1시간 동안 액세스할 수 있습니다. Azure 검색에서 관리하는 데이터를 업데이트 또는 삭제할 수는 없으나 쿼리를 실행하고 사용자 환경의 모양을 변경하도록 다양한 코드 변경을 수행할 수는 있습니다. 탐색을 완료하기 전에 세션이 만료되면 항상 다른 세션을 통해 다시 시작하거나, 인덱스를 직접 만들거나 로드하는 것이 목표인 경우 평가판 또는 전체 구독으로 전환할 수 있습니다.

[Azure 앱 서비스 시도](https://tryappservice.azure.com/) 사이트에서 Azure 검색은 웹앱 템플릿의 일부로, 풍부한 전체 텍스트 검색 환경과 더불어 Azure 플랫폼의 이 서비스에서만 사용 가능한 다양한 검색 중심 기능도 함께 제공합니다.

SQL 데이터베이스와 같은 기타 Azure 서비스에서도 전체 텍스트 검색을 제공하기는 하지만 Azure 검색 서비스는 튜닝 제어, 페이지 매김 및 개수, 적중 항목 강조, 자동 완성 쿼리 제안, 자연 언어 지원, 패싯 탐색, 필터링 등을 제공합니다. 다양한 [샘플](https://github.com/AzureSearch) 시연에서 볼 수 있듯이, Azure 검색 및 ASP.NET만 사용해서도 모든 기능을 갖춘 검색 기반 응용 프로그램을 개발할 수 있습니다.

[Azure 앱 서비스 시도](https://tryappservice.azure.com/) 제공의 일부로 사용할 Azure 검색 서비스는 읽기 전용입니다. 따라서 세션에 제공된 검색 모음을 사용해야 합니다. 사용자 고유의 인덱스 또는 데이터를 업로드하거나 사용할 수 없습니다. 작업할 데이터는 [미국 지리 설문 조사(USGS)]()로, 미국 전체 랜드마크, 유적지, 건물 및 기타 랜드마크 특징에 대한 약 3백만 행으로 구성됩니다.

1시간 세션을 충분히 활용할 수 있도록 다음 지침에서 쿼리 및 코드를 안내합니다.

진행하기 전에 잠시 시간을 내어 코드, 서비스 및 검색 가능한 데이터에 대한 몇 가지 요점을 살펴봅니다. 아직 Azure 검색에 익숙하지 않은 경우 약간의 배경 지식이 도움이 될 수 있습니다.

##코드 및 Azure 검색에 대한 팩트

Azure 검색은 서비스와 데이터의 [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) 제공으로, 완전히 관리되는 검색 서비스와 Azure 검색의 제한되지 않은 인스턴스(예: Azure 앱 서비스 시도 옵션을 사용하지 않는 경우)를 사용할 때 업로드하는 검색 가능한 데이터로 구성됩니다.

검색 작업에 사용되는 데이터는 Azure에서 검색 서비스와 함께, 데이터의 작업 근접도에 따라 짧은 대기 시간과 일관된 검색 동작이 보장되는 위치에 저장됩니다. 현재 검색 가능한 데이터의 오프라인 또는 원격 저장소는 지원되지 않습니다. 정리하자면 다음과 같습니다.

- 검색 데이터는 Azure 검색에서 관리하는 인덱스에 저장되며 데이터는 검색 가능한 항목당 하나의 문서로 채워집니다. 
- 대부분의 인덱스는 단일 데이터 집합에서 로드되며 사용자는 검색 작업의 컨텍스트에 유용한 필드만 포함하도록 미리 준비합니다. 
- 인덱스를 정의하는 스키마는 사용자가 정의하며 필터 식에 유용할 수 있는 검색 가능 필드, 검색 불가능 필드를 지정하고 튜닝 결과에 대한 점수 매기기 프로필 등을 생성합니다.
- 데이터는 인덱서(Azure SQL 데이터베이스 또는 Azure DocumentDB에서만 지원)에 의해 자동 로드되거나 Azure 검색 API 중 하나를 통해 검색 인덱스에 푸시될 수 있습니다. API를 사용하는 경우 JSON 형식으로 되어 있기만 하다면 모든 데이터 원본에서 데이터를 푸시할 수 있습니다.

[Azure 앱 서비스 시도](https://tryappservice.azure.com/) 옵션에서 ASP.NET + Azure 검색 사이트 템플릿은 웹 응용 프로그램에 대한 소스 코드를 제공하며 Visual Studio Online(1시간 세션의 일부로 사용 가능)에서 수정 가능합니다. 코드를 보거나 변경하는 데 별도의 개발 도구가 필요하지 않습니다.

코드는 인덱스에 대해 쿼리를 실행하기 위해 [Azure 검색 .NET 클라이언트 라이브러리](https://msdn.microsoft.com/library/dn951165.aspx)를 사용하여 C#으로 작성되며 패싯 탐색을 제공하고 웹 페이지에 개수와 검색 결과를 표시합니다.

템플릿에 포함되지 않은 기타 코드는 USGS 검색 인덱스를 빌드 및 로드하는 데 사용됩니다. 서비스는 읽기 전용이므로 쓰기 액세스가 필요한 모든 작업은 미리 완료해야 합니다. 이 문서의 끝에서 스키마를 빌드하는 데 사용된 [스키마 복사본](#schema)을 확인할 수 있습니다.

##시작

1시간 세션이 아직 시작되지 않은 경우 다음 단계를 따라 시작합니다.

1. [https://tryappservice.azure.com](https://tryappservice.azure.com/)으로 이동하고 아래로 스크롤하여 **웹앱**을 선택합니다. 
2. **다음**을 클릭합니다.
3. **ASP.NET + Azure 검색 사이트** 템플릿을 선택합니다.

    ![][1]

4. **만들기**를 클릭합니다.
5. 로그인 방법을 선택하고 사용자 이름 및 암호를 제공합니다.

    ![][2]

6. 사이트를 프로비전하는 동안 기다립니다. 준비되면 다음과 유사한 페이지가 표시됩니다. 각 페이지에는 남아 있는 시간을 항상 알 수 있도록 실행 중인 시계가 표시됩니다.

    ![][3]

7. **Visual Studio Online으로 편집**을 선택하여 솔루션을 보고 사이트를 탐색합니다.
9. Visual Studio Online에서 페이지 맨 위에서 세션 옵션을 확장하고 **웹 사이트 찾아보기**를 클릭합니다.

    ![][4]

10. Azure 검색 웹 사이트에 대한 시작 페이지가 표시됩니다. **시작** 단추를 클릭하여 사이트를 엽니다.

    ![][5]

11. ASP.NET 웹 사이트가 검색 상자를 제공하는 브라우저에 열립니다. *Yellowstone*과 같은 검색할 친숙한 용어 또는 *Mount Rainier*와 같은 잘 알려진 산을 입력합니다. 친숙한 랜드마크부터 시작하면 결과를 보다 쉽게 평가할 수 있습니다.

    ![][6]


##먼저 수행할 작업
검색 인덱스가 제대로 작동 중이므로 첫 번째 단계로 좋은 방법은 몇 가지 쿼리를 시도해보는 것입니다. Azure 검색에서는 모든 표준 검색 연산자(+,-, |), 리터럴 일치를 위한 물음표, 와일드 카드(*) 및 우선 순위 연산자를 모두 지원합니다. 연산자의 전체 목록에 대한 쿼리 구문 참조를 검토할 수 있습니다.

- 별표(`*`)를 추가하여 와일드 카드로 검색을 시작합니다. 그러면 2,262,578 인덱스에 몇 개의 문서가 있는지 알려줍니다.
- 다음으로, "Yellowstone"를 입력한 다음 "+center", "+building", "-ND"를 추가하여 North Dakota의 결과를 제외하고 Yellowstone 방문자 센터만 포함하도록 점진적으로 검색 결과 범위를 좁힙니다. `Yellowstone +center +building -ND`  
- 우선 순위 연산자와 문자열 일치를 결합한 검색 구문 `statue+(park+MT)`을 시도합니다. 아래 스크린샷과 유사한 결과가 표시됩니다. 패싯 범주가 기능 클래스 아래 나타나며 대부분의 검색 응용 프로그램에 공통적으로 발견되는 기능인 패싯 탐색을 통해 자기 주도 필터링을 제공합니다.

    ![][7]

진행할 준비가 되셨습니까? 몇 줄의 코드를 변경하여 전체 텍스트 검색 작업에 어떤 영향이 있는지 확인해보겠습니다.

##searchMode.All 변경

Azure 검색에는 검색 연산자 동작을 제어하는 데 사용할 수 있는 구성 가능한 **searchMode** 속성이 있습니다. 이 속성에 대해 유효한 값은 `Any`(기본값) 또는 `All`입니다. 이러한 옵션 설정에 대한 자세한 내용은 [단순 쿼리 구문](https://msdn.microsoft.com/library/dn798920.aspx)을 참조하세요.

- **searchMode.Any**는 검색 단어에 있는 어떤 항목이라도 일치한다면 검색 결과에 포함하도록 명시합니다. 검색 구문이 `Yellowstone visitor center`를 포함하는 경우 이러한 단어 중 하나라도 포함하는 모든 문서가 검색 결과에 포함됩니다. 이 모드는 *재현율*에 중심을 둡니다.
- 이 샘플에 사용된 **searchModel.All**의 경우 지정된 모든 단어가 문서에 있어야 합니다. 이 모드는 **searchMode.Any**보다 엄격하지만 재현율보다 *정확도*를 선호하는 응용 프로그램에서 적합한 모드입니다. 

> [AZURE.NOTE]**searchMode.Any**는 쿼리 생성이 연산자를 최소로 사용하고 대부분 구문으로 구성된 경우 최적으로 작동합니다. 일반적인 경험상, 전자 상거래 사이트와 같은 소비자 응용 프로그램을 검색하는 사람들은 단어만 사용하는 경향이 있는 반면, 콘텐츠 또는 데이터를 검색하는 사람들은 검색 구문에 연산자를 포함할 가능성이 높습니다. 검색 단어에 연산자가 포함될 가능성이 있다고 판단되는 경우(특히 `NOT (-)` 연산자) **searchModel.All**로 시작합니다. 이와 반대로, 다른 모드인 **searchMode.Any**는 `NOT` 연산자를 다른 검색 단어와 `OR`하여 결과를 정리하기보다는 크게 확장할 수 있습니다. 아래 예제는 이 두 모드의 차이를 이해하는 데 도움이 됩니다.

이 작업에서는 **searchMode**를 변경하고 모드에 따라 검색 결과를 비교합니다.

1. 샘플 응용 프로그램이 포함된 브라우저 창을 열고 **Visual Studio Online에 연결**을 선택합니다.

    ![][8]

2. **Search.cshtml**을 열고 39줄에서 `searchMode.All`을 찾아 `searchMode.Any`로 변경합니다.

    ![][9]

3. 오른쪽 표시줄에서 **실행**을 클릭합니다.

    ![][10]
 
다시 빌드된 응용 프로그램 창에서 `Yellowstone +center +building -ND`처럼 이전에 사용한 검색 단어를 입력하고 **searchMode**의 변경 전/후 결과를 비교합니다.

상당한 차이가 있습니다. 7개이던 검색 결과가, 2백만 가지 이상 표시됩니다.

   ![][11]
 
관찰된 동작은 `NOT` 연산자(이 경우 "-ND")를 포함했기 때문이며 **searchMode**가 `Any`로 설정된 경우 *AND'd*가 아닌 *OR'd*입니다.

이 구성에 따라 검색 결과는 검색 단어 `Yellowstone`, `center` 및 `building`에 대한 적중 항목을 포함하며 모든 문서는 `NOT North Dakota`입니다. 13,081개의 문서만 `North Dakota` 구문을 포함하므로 거의 모든 데이터 집합이 반환됩니다.

물론, 이것은 아마도 가능성이 적은 시나리오이기는 하지만 `NOT` 연산자를 포함하는 검색 구문에서 **searchMode**의 효과를 보여주므로 동작이 발생하는 이유와 원하는 결과가 아닌 경우 변경하는 방법을 파악하는 데 도움이 됩니다.

이 자습서를 계속 진행하려면 **searchMode**를 원래 값(39줄에서 `All`로 설정)으로 되돌리고 프로그램을 실행하며 나머지 작업에 대해 다시 빌드된 앱을 사용합니다.
 
##워싱턴 주에 대한 전역 필터 추가

일반적으로 사용 가능한 데이터의 하위 집합을 검색하려는 경우 데이터를 가져올 때 데이터 원본에서 필터를 설정합니다. 학습 용도로 전용 데이터로 작업하는 경우 워싱턴 주를 포함하는 문서만 반환하도록 응용 프로그램에서 필터를 설정합니다.

1. Search.cshtml을 열고 **SearchParameters**코드 블록(36줄에서 시작)을 찾아 주석 줄과 필터를 추가합니다.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


필터는 OData 구문을 사용하여 지정되며 패싯 탐색에 자주 사용되거나 쿼리 문자열에 포함되어 쿼리를 제한합니다. 자세한 내용은 [OData 필터 구문](https://msdn.microsoft.com/library/azure/dn798921.aspx)을 참조하세요.

2. **실행**을 클릭합니다.

3. 응용 프로그램을 엽니다.

4. 개수를 반환하는 와일드 카드(*)를 입력합니다. 이제 워싱턴 주의 모든 지리적 특징에 대한 모든 문서인 42,411개 항목으로 결과가 제한됩니다.

   ![][12]

##적중 항목 강조 표시 추가

간단한 한 줄짜리 코드 변경을 수행해 보았으므로 이제 여러 곳에서 코드를 변경해야 하는 보다 심도 있는 수정을 시도해보고 싶을 수 있습니다. 다음 버전의 **Search.cshtml**은 현재 세션에서 Search.cshtml 파일 바로 위에 붙여넣을 수 있습니다.

다음 코드는 적중 항목 강조 표시를 추가합니다. [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx)에 새로운 속성이 선언되었습니다. 또한 결과 컬렉션을 반복하여 강조 표시해야 하는 문서와 결과를 렌더링하는 HTML을 가져오는 새로운 함수도 있습니다.

이 코드 샘플을 실행하면 지정된 필드에 해당하는 일치 항목이 있는 검색 단어 입력이 굵게 강조 표시됩니다.

   ![][14]

원래 **Search.cshtml** 파일의 복사본을 저장하여 두 버전이 어떻게 다른지 비교할 수 있습니다.

> [AZURE.NOTE]파일의 크기를 줄이기 위해 주석은 생략했습니다.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }


##다음 단계

[Azure 앱 서비스 시도](https://tryappservice.azure.com/) 사이트에 제공된 읽기 전용 서비스를 사용하여 쿼리 구문과 전체 텍스트 검색 작동을 확인하고 searchMode 및 필터에 대해 학습했으며 검색 응용 프로그램에 적중 항목 강조 표시를 추가해보았습니다. 다음 단계로 인덱스 만들기 및 업데이트를 진행합니다. 이 단계를 통해 다음을 수행할 수 있습니다.

- [점수 매기기 프로필 정의](https://msdn.microsoft.com/library/dn798928.aspx): 검색 점수 튜닝에 사용되며 점수가 높은 항목을 먼저 표시합니다.
- [확인기 정의](https://msdn.microsoft.com/library/mt131377.aspx): 사용자 입력에 반응하여 자동 완성 또는 미리 입력 쿼리 제안을 추가합니다.
- [인덱서 정의](https://msdn.microsoft.com/library/dn946891.aspx): 데이터 원본이 Azure SQL 데이터베이스 또는 Azure DocumentDB인 경우 항상 인덱스를 자동으로 업데이트합니다.

이러한 모든 작업을 수행하려면 서비스에서 인덱스를 만들고 채울 수 있는 Azure 구독이 필요합니다. 무료 평가판에 등록하는 방법에 대한 자세한 내용은 [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

Azure 검색에 대해 자세히 알아보려면 [http://azure.microsoft.com](http://azure.microsoft.com)에서 [설명서 페이지](http://azure.microsoft.com/documentation/services/search/)를 참조하거나 전체 Azure 검색 기능을 살펴보는 다양한 [샘플 및 비디오](https://msdn.microsoft.com/library/dn818681.aspx)를 확인하세요.

<a name="Schema"></a>
##스키마 정보

다음 스크린샷은 이 템플릿에 사용된 인덱스를 만드는 스키마를 보여줍니다.
 
   ![][13]

###Schema.json 파일

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }


<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=July15_HO3-->