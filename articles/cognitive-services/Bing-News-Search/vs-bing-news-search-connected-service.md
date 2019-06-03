---
title: Visual Studio의 연결된 서비스 및 C#을 사용하여 Bing News Search API에 연결
titleSuffix: Azure Cognitive Services
description: ASP.NET Core 웹 애플리케이션에서 Bing News Search에 연결합니다.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 6f697c91851e7ddc95f84239987bb4378bafd094
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798633"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>자습서: Visual Studio의 연결된 서비스 및 C#을 사용하여 Bing News Search API에 연결

Bing News Search를 사용하여 앱 및 서비스가 웹까지 적용되는 광고 없는 검색 엔진을 활용하도록 할 수 있습니다. Bing News Search는 Cognitive Services에서 사용할 수 있는 검색 서비스 중 하나입니다.

이 문서에서는 Bing News Search에 대한 Visual Studio 연결된 서비스 기능을 사용하는 방법에 대한 자세한 정보를 제공합니다. 이 기능은 Cognitive Services 확장이 설치된 Visual Studio 2017 15.7 이상에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- Web Development 워크로드가 설치된 Visual Studio 2017 버전 15.7 이상. [여기에서 다운로드하세요](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>프로젝트에 Bing News Search API에 대한 지원 추가

1. MyWebApplication이라는 새 ASP.NET Core 웹 프로젝트를 만듭니다. 모든 기본 설정을 사용하여 **웹 애플리케이션(Model-View-Controller)** 프로젝트 템플릿을 사용합니다. 프로젝트에 코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 MyWebApplication으로 지정하는 것이 중요합니다. 

1. **솔루션 탐색기**에서 **추가** > **연결된 서비스**를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.

   ![연결된 서비스 추가 메뉴 항목 스크린샷](../media/vs-common/Connected-Service-Menu.PNG)

1. 사용 가능한 서비스 메뉴에서 **Bring Intelligent Search To Your Apps**(앱에 지능형 검색 추가)를 선택합니다.

   ![연결된 서비스 목록 스크린샷](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다. 사용하려는 구독을 선택하고, Bing News Search API의 이름을 선택합니다. **편집**을 선택하여 자동으로 생성된 이름을 수정할 수도 있습니다.

   ![구독 및 이름 필드의 스크린샷](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. 리소스 그룹 및 가격 책정 계층을 선택합니다.

   ![리소스 그룹 및 가격 책정 계층 필드의 스크린샷](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   가격 책정 계층에 대한 자세한 내용을 보려면 **가격 책정 검토**를 선택합니다.

1. **추가**를 선택하여 연결된 서비스에 대한 지원을 추가합니다.
   Visual Studio는 Bing News Search API에 대한 연결을 지원하기 위해 프로젝트를 수정하여 NuGet 패키지, 구성 파일 항목 및 기타 변경 사항을 추가합니다. 출력에는 프로젝트에 나타나는 결과에 대한 로그가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   appsettings.json 파일에는 이제 다음과 같은 새 설정이 포함됩니다.

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Bing News Search API를 사용하여 웹 페이지에 검색 기능 추가

프로젝트에 Bing News Search API에 대한 지원을 추가했으므로 아래에서 API를 사용하여 웹 페이지에 지능형 검색 기능을 추가하는 방법을 확인합니다.

1. *Startup.cs*의 `ConfigureServices` 메서드에서 `IServiceCollection.AddSingleton`에 대한 호출을 추가합니다. 이렇게 하면 키 설정을 포함하는 구성 개체를 프로젝트의 코드에서 사용할 수 있습니다.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. **Models** 폴더 아래에 *BingNewsModel.cs*라는 새 클래스 파일을 추가합니다. 프로젝트 이름을 다르게 지정한 경우 MyWebApplication 대신 사용자 고유의 프로젝트 네임스페이스를 사용합니다. 해당 내용을 다음 코드로 바꿉니다.
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   이 모델은 Bing News Search 서비스에 대한 호출 결과를 저장하는 데 사용됩니다.
 
1. **Controllers** 폴더에 *IntelligentSearchController.cs*라는 새 클래스 파일을 추가합니다. 해당 내용을 다음 코드로 바꿉니다.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   이 코드에서 생성자는 키를 포함하는 구성 개체를 설정합니다. `Search` 경로에 대한 메서드는 `BingSearchResult` 함수에 대한 리디렉션에 불과합니다. 이 메서드는 `GetNewsSearchClient` 메서드를 호출하여 `NewsSearchAPI` 클라이언트 개체를 가져옵니다.  `NewsSearchAPI` 클라이언트 개체에는 실제로 서비스를 호출하고 방금 만든 `SearchResult` 모델에서 결과를 반환하는 `SearchAsync` 메서드가 포함됩니다. 

1. 이전 코드에서 참조되었던 클래스 `MyHandler`를 추가합니다. 이 클래스는 검색 서비스에 대한 비동기 호출을 기본 클래스 `DelegatingHandler`에 위임합니다.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. 검색을 제출하고 결과를 보기 위한 지원을 추가하려면 **Views** 폴더에서 **IntelligentSearch**라는 새 폴더를 만듭니다. 이 새 폴더에서 보기 *BingSearchResult.cshtml*을 추가합니다. 다음 코드를 복사합니다.

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. 로컬에서 웹 애플리케이션을 시작하고 방금 만든 페이지의 URL(IntelligentSearch/BingSearchResult)을 입력한 후 검색 단추를 사용하여 검색 요청을 게시합니다.

   ![Bing News Search 결과 스크린샷](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹이 더 이상 필요하지 않은 경우 삭제할 수 있습니다. 이렇게 하면 Cognitive Service 및 관련 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 삭제하려는 리소스 그룹을 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력** 상자에 리소스 그룹의 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

Bing News Search API에 대한 자세한 내용은 [Bing News Search란?](index.yml)을 참조하세요.
