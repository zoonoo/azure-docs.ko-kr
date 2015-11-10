<properties
	pageTitle="ASP.NET MVC 웹앱에 Azure 검색 연결 | Microsoft Azure"
	description="호스트된 클라우드 검색 서비스인 Azure 검색으로 ASP.NET MVC 웹앱에 연결합니다. .Net 라이브러리 또는 REST API를 사용하여 결과를 연결, 쿼리 및 렌더링하는 방법을 알아봅니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

#ASP.NET MVC 웹앱에 Azure 검색을 통합하는 방법

ASP.NET MVC는 Azure 검색에 통합되는 사용자 지정 솔루션에서 널리 사용되는 웹 응용 프로그램 프레임워크입니다. 이 문서에서 Azure 검색에 ASP.NET 웹앱을 연결하고 일반 작업에 대한 디자인 패턴을 수립하며 개발을 원활하게 할 수 있는 코딩 사례를 검토하는 방법을 알아봅니다.

##ASP.NET 및 Azure 검색을 사용하는 샘플 및 데모

검색을 ASP.NET과 통합하는 방법을 보여주는 여러 코드 샘플이 이미 있습니다. 이러한 링크를 방문하여 코드 또는 데모 앱으로 바로 이동할 수 있습니다.

- [뉴욕시(NYC) 작업 데모 사이트](http://aka.ms/azjobsdemo)
- [앱 서비스 + Azure 검색 시도](search-tryappservice.md)
- [비디오, 자습서, 데모 및 코드 샘플의 전체 목록](earch-video-demo-tutorial-list.md)

##해당 서비스에 연결

서비스 및 문제 요청에 연결을 설정하려면 웹 응용 프로그램에 세 가지가 필요합니다.

- https://<service-name>.search.windows.net로 포맷된 프로비전한 Azure 검색 서비스의 URL
- Azure 검색에 연결을 인증하는 API 키(문자열)
- 연결 요청을 공식화하는 HTTPClient 또는 SearchServiceClient

####URL 및 API 키

[포털](search-create-service-portal.md)에서 URL 및 API 키를 찾거나 [관리 REST API](https://msdn.microsoft.com/library/dn832684.aspx)를 사용하여 프로그래밍 방식으로 검색할 수 있습니다.

일반적으로 URL 및 키 모두를 사용자 상호 작용 프로그램의 web.config 파일에 배치합니다.

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

검색 서비스 이름은 연결에 도메인(search.windows.net)을 추가하는 한 프로비전 동안 지정된 짧은 이름일 수 있습니다. 또는 web.config에서 접두사에 HTTPS 없이 완전히 정규화된 이름(<service-name>.search.windows.net)을 지정할 수 있습니다.

API 키는 서비스 프로비전 중에(관리자 키만) 또는 포털에서 쿼리 키를 만드는 경우 손으로 생성된 인증 토큰입니다. 응용 프로그램에 사용할 수 있는 검색 작업을 결정하는 키의 유형입니다.

- 관리자 키(읽기/쓰기 권한, 서비스 당 2개)
- 쿼리 키(읽기 전용, 서비스 당 최대 50개)

API 키는 32자의 문자열입니다. 시각적으로 관리자 및 쿼리 키 간의 구분이 없습니다. 코드에서 지정한 키 유형을 잊어버린 경우 포털을 확인하거나 관리 REST API를 사용하여 키 유형을 반환해야 합니다. 키에 대한 자세한 내용은 [Azure 검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 참조하세요.

> [AZURE.TIP]쿼리 키는 클라이언트에 읽기 전용 환경을 제공합니다. [TryAppService + Azure 검색](search-tryappservice.md)을 참조하여 읽기 전용 서비스에서 사용할 수 있는 Azure 검색 작업을 시험 사용합니다. TryAppService에서 웹앱 코드는 완벽 하게 수정할 수 있습니다. ASP.NET 프로젝트에서 C# 코드를 변경하여 웹 페이지 레이아웃, 검색 쿼리 생성, 또는 검색 결과를 수정할 수 있습니다. 읽기 전용, 서비스 연결에서 쿼리 api-키를 포함하는 Azure 검색 서비스 인덱스 및 문서 로드 작업입니다.

####클라이언트 연결

다음 두 코드 조각은 URL과 API 키를 사용하여 검색 서비스에 연결을 설정합니다. 서비스 이름 및 API 키를 web.config 파일에 지정한다는 사실에 유의합니다. REST 호출의 경우 쿼리 키가 머리글 또는 URL에 직접 전달될 수 있는 반면 관리자 키는 요청 헤더에 전달되어야 합니다.

REST API 호출을 사용하는 **[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx)**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

.NET을 사용하는 **[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx)**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##디자인 패턴

Azure 검색과 통합된 웹앱은 쿼리를 작성하고 결과를 렌더링해야 합니다. 이 섹션에서는 사용자 상호 작용 코드를 포함하는 프로그램에서 실행되는 작업에 코드를 구성하는 방법에 대한 지침을 제공합니다. 스키마 정의, 인덱스 생성 및 데이터 수집은 의도적으로 제외됩니다. 이러한 작업을 코딩하는 방법에 대한 지침은 [Azure 검색의 동영상, 샘플 및 자습서](search-video-demo-tutorial-list.md)에 나열된 연습 및 예제를 참조하세요.

###쿼리 공식

인덱스의 전체 텍스트 검색은 인덱스를 정의하는 스키마의 **isSearchable**로 표시된 필드에서 실행됩니다. 검색 용어 입력을 고려하면 ("q" 문자열에 의해 아래에 표시됨) 검색 엔진은 모든 검색 가능한 필드 내에서 일치를 찾고 **isRetrievable**로 표시된 필드에서 결과를 반환합니다.

> [AZURE.NOTE]대부분의 필드를 검색할 수 있는 반면 인덱스는 필터식에만 사용 되는 필드를 포함하며 이 경우 검색할 수 없다고 표시하여 전체 텍스트 검색에서 제외하고 검색 결과에서 제외됩니다.

검색 쿼리는 대상 인덱스를 지정하는 검색 요청 및 필터에 사용되거나 요청을 구체화하는 매개 변수에 사용자가 제공한 입력 용어를 래핑합니다. +,-또는 |와 같은 검색 문자열 안에 포함된 연산자는 자동으로 처리되며 이는 검색 단어를 구문 분석하기 위한 코딩 요구 사항이 없다는 것을 의미합니다. 모든 구문 분석을 내부 작업과 같이 검색 엔진에서 수행합니다. 엔진에서 전달한 문자열을 구문 분석하고 분석한다고 가정할 수 있습니다.

검색 쿼리는 **검색** 또는 **제안**의 두 가지 형태입니다. 쿼리의 각 유형에 대해 별도의 메서드를 정의합니다. **검색**은 인덱스에서 필드의 전체 텍스트를 검색합니다. **제안**은 사용자가 입력한 처음 세 문자를 기반으로 잠재적인 검색 용어의 목록을 작성하는 Azure 검색의 미리 입력 또는 자동 완성 쿼리 기능입니다. 대부분의 경우 구분되지 않는 데이터가 포함된 필드 대신 상대적으로 고유하거나 구별된 값(예: 제품 또는 게시 이름)을 포함하는 해당 필드로 **제안**을 제한합니다.

다음 코드 조각는 REST API를 사용하는 프로그램에 검색 용어 입력을 캡처합니다. 입력 용어는 q 문자열에 의해 표시되고 나머지 매개 변수는 동일한 검색 페이지의 패싯된 탐색 구조에서 필터 값을 전달하는 데 사용됩니다. 입력 용어 및 필터 매개 변수는 모두 검색 방법에서 사용됩니다.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
이 쿼리를 허용하는 **검색** 메서드를 다음과 같이 정의합니다. 쿼리 문자열에 매개 변수, 패싯된 탐색 구조(축소된 검색 결과에서 주요 작업을 수행하는 필터를 통해 지원됨) 및 정렬 순서를 정의합니다.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

MVC 뷰 또는 컨트롤러에 검색 문자열을 생성하는 .NET 메서드를 배치할 수 있습니다. 이 함수는 홈 컨트롤러에 문자열을 전달합니다. 또한 결과에 대한 데이터 구조를 정의합니다.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

**검색**을 호출하는 .NET 메서드는 연결 및 검색 작업을 제공하는 주요 C# 프로그램에 포함되고 이와 유사할 수 있습니다.

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###검색 결과 처리

검색 결과는 인덱스 스키마에서 isRetrievable로 표시된 필드로 구성된 행 집합으로 반환됩니다. 결과 집합을 렌더링하는 간단한 방법은 MVC에서 ViewBag 시스템 개체를 사용 하는 것입니다. 다음 코드 조각은 [CodePlex의 AdventureWorksDemo 프로젝트](https://azuresearchadventureworksdemo.codeplex.com/) 중 Index.cshtml에서 나옵니다.

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###패싯 탐색

동일한 Index.cshmtl 파일에서 색, 가격 또는 범주에 따라 검색 결과를 점진적으로 축소하여 자기 주도 필터링에 대한 분류를 제공하는 패싯된 탐색 구조를 작성하는 데 HTML를 사용할 수 있습니다.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###적중 항목 강조 표시

검색 결과에서 검색 용어의 인스턴스에 스타일을 적용하면 적중 항목 강조 표시라고 합니다. Azure 검색에서 적중 항목 강조 표시는 항목 강조 표시 검색 매개 변수를 통해 쿼리에 지정되고 여기로 용어 일치를 검색하는 필드의 쉼표로 구분된 목록을 가져옵니다. 적용할 실제 스타일은 사용자가 선택할 수 있습니다. 다음 세 개의 코드 조각은 [TryAppService + Azure 검색 자습서](search-tryappservice.md)에서 옵니다.

우선 적중 항목 강조 표시를 검색 매개 변수로 지정하고 필드를 나열하여 용어 일치를 확인합니다. 적중 항목 강조 표시에 사용할 HTML 스타일을 지정합니다.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

다음으로 검색 결과를 반복하여 강조를 표시해야 하는 문자열을 찾습니다. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

마지막으로 이전 코드 조각에서 확인된 결과 집합을 지정하여 검색 결과의 레이아웃을 제공합니다.

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


##일반적인 코딩 사례

MVC, 프로그래밍,.NET 또는 REST API를 처음 사용하세요? 이 섹션에서는 몇 가지 코딩 방법을 제공하여 더 빠르게 작업할 수 있습니다.

###MVC 템플릿

다음 테이블은 Azure 검색을 포함하는 응용 프로그램에서 MVC 템플릿 구성 요소를 사용하는 방법을 요약합니다. MVC 4 또는 MVC 5를 사용하는 경우 일반적으로 Azure 검색을 통합하는 코드를 이러한 모듈에 추가합니다.

파일|설명
----|-----------
Web.config|서비스 URL 및 api-key 제공 주 프로그램 모듈의 System.Configuration에 참조를 추가하여 값을 읽습니다.
Program.cs|주 프로그램에서 HttpClient 또는 SearchServiceClient를 설정하여 서비스에 연결을 설정합니다. 이 프로그램에 검색 메서드를 추가합니다.
DataModel|사용되지 않습니다. 인덱스 생성 및 데이터 로드 작업은 다른 프로그램이라고 가정하면 데이터 모델은 웹 응용 프로그램의 Azure 검색에 필요하지 않습니다.
뷰|뷰는 응용 프로그램 웹 페이지에서 검색 상자 입력에서 검색 결과 처리하기 위한 동적 HTML까지 HTML을 포함합니다.
Controllers|쿼리 생성 및 오류 처리는 HomeContoller.cs에서 일반적으로 발견됩니다. 최소한 컨트롤러는 Azure 검색에서 결과를 검색하고 결과 집합을 보기로 전달하는 검색 메서드를 포함해야 합니다. 

선택적으로 자동 완성 쿼리에 제안을 사용하는 경우 인덱스가 사용자가 제공한 검색 용어 입력에 일치하는 값을 포함하는지에 따라 제안된 쿼리를 반환하는 메서드를 포함합니다.

###.NET 클라이언트 라이브러리를 사용 하는 경우 및 REST API

ASP.NET 응용 프로그램의 경우 .NET 클라이언트 라이브러리는 HTTP 연결을 설정하고 JSON 직렬화 및 역직렬화를 처리하기 때문에 코드를 간소화할 수 있어서 더 좋습니다.

경우에 따라 기능 패리티가 두 가지 방법 간에 API의 선택을 지정할 수 있습니다. 일반적으로 [.NET 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn951165.aspx) 및 [서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)는 필요한 작업이 양쪽에서 구현되는 한 바꿔 사용할 수 있습니다. 그러나 때로 새 기능은 미리 보기 릴리스의 일환으로 REST API에서 첫 번째로 표시되고 몇 개월 후에 .NET 라이브러리에 추가됩니다. 예를 들어 특정 데이터 원본 유형에서 데이터 로드 작업을 자동화하는 데 사용되는 인덱서는 몇 개월 후에 클라이언트 라이브러리에 표시되기 전에 REST API 미리 보기에서 먼저 나타납니다. 기능 구현에 대한 제한은 기능 설명서에 나와 있습니다.

###REST API의 JSON 직렬화 및 역직렬화에 AzureSearchHelper.cs 포함

이 단계를 수행하는 .NET 라이브러리와 달리 서비스 REST API는 서비스와의 요청-응답 교환에서 JSON 문서를 직렬화 및 역직렬화해야 합니다. JSON은 인덱스에서 문서를 로드하거나 새로 고치는 경우 데이터 전송에 대한 페이로드 형식입니다.

JSON serialization에 대한 코드는 **AzureSearchHelper.cs**라는 파일에서 여러 샘플을 찾을 수 있습니다.

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}

###코드 구성

동일한 Visual Studio 솔루션 내에서 워크로드를 독립 실행형 프로젝트로 나누면 각 프로그램을 보다 유연하게 설계, 유지, 실행할 수 있습니다. 다음 세 가지를 사용하는 것이 좋습니다.

- 인덱스 생성 코드
- 데이터 수집 코드
- 사용자 상호작용 코드

Azure 검색에서 문서 추가 또는 업데이트 또는 쿼리 실행 등 인덱싱 작업 및 문서 작업은 서로 완전히 독립적입니다. 즉, 검색 요청을 공식화하고 결과를 렌더링하는 ASP.NET 사용자 상호 작용 코드에서 인덱스 관리를 분리할 수 있습니다.

검색 요청을 처리하고 응답하는 코드를 ASP.NET MVC 응용 프로그램 프로젝트에 배치하는 동안 대부분의 코드 샘플에서 인덱스는 한 프로젝트(다양한 샘플에서 DataIndexer, CatalogIndexer 또는 DataCatalog라고 함)에 생성 및 로드됩니다. 코드 샘플에서 하나의 프로젝트에서 번들 인덱스를 생성하고 문서를 업로드하는 것이 실용적이지만 프로덕션 코드는 이러한 작업을 격리할 것입니다. 인덱스를 만들면 거의 변경되지 않는(변경 내용에 따라 다시 작성해야 할 수도 있음) 반면 문서를 반복적으로 새로 고쳐야 할 수 있습니다.

워크로드를 분리하면 Azure 검색에 대한 다른 수준의 권한(완전한 관리자 권한 및 쿼리 전용 권한), 다른 프로그래밍 언어, 프로그램에 따라 보다 구체적인 종속성, 및 프로그램을 독립적으로 수정하거나 여러 프런트엔드 응용 프로그램을 만들 수 있는 기능이라는 형태로 다른 이점이 있습니다. 모든 것은 작성된 인덱스에서 작동하고 핵심 인덱싱 응용 프로그램에 의해 유지됩니다.

##다음 단계

Azure 검색 및 ASP.NET 통합을 이해하려면 다음 링크를 방문합니다.

- [.NET 응용 프로그램에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md) 
- [Azure 검색 개발자 사례 연구](search-dev-case-study-whattopedia.md)
- [Azure 검색 개발을 위한 일반적인 워크플로](search-workflow.md) 

<!---HONumber=Nov15_HO2-->