---
title: '자습서: Visual Studio에서 연결된 서비스를 사용하여 Text Analytics 서비스에 연결'
titleSuffix: Azure Cognitive Services
description: ASP.NET Core 웹 애플리케이션에서 Text Analytics에 연결하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 75228b8c939cb5b8dd04471662ba86b46cfc808c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860468"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>자습서: Visual Studio에서 연결된 서비스를 사용하여 Text Analytics 서비스에 연결

Text Analytics 서비스를 사용하여 다양한 정보를 추출하여 시각적 데이터를 분류 및 처리하고 이미지의 기계 지원 수정을 수행하여 서비스 조정을 지원할 수 있습니다.

이 문서 및 함께 제공되는 문서에서는 Text Analytics 서비스용 Visual Studio 연결된 서비스 기능 사용에 대한 세부 정보를 제공합니다. 이 기능은 Cognitive Services 확장이 설치된 Visual Studio 2019 이상에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- 웹 개발 워크로드가 설치된 Visual Studio 2019. [여기에서 다운로드하세요](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Text Analytics 서비스에 대한 지원을 프로젝트에 추가

1. TextAnalyticsDemo 라는 새 ASP.NET Core 웹 프로젝트를 만듭니다. 모든 기본 설정을 사용하여 웹 애플리케이션(Model-View-Controller) 프로젝트 템플릿을 사용합니다. 프로젝트에 코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 MyWebApplication으로 지정하는 것이 중요합니다.  이 문서의 예제에서는 MVC를 사용하지만 모든 ASP.NET 프로젝트 형식에 Text Analytics 연결된 서비스를 사용할 수 있습니다.

1. **솔루션 탐색기**에서 **연결된 서비스** 항목을 두 번 클릭합니다.
   연결된 서비스 페이지가 나타나고 프로젝트에 추가할 수 있는 서비스가 표시됩니다.

   ![솔루션 탐색기의 연결된 서비스 스크린샷](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. 사용 가능한 서비스 메뉴에서 **Text Analytics를 사용하여 감정 평가**를 선택합니다.

   ![연결된 서비스 화면 스크린샷](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다.

   ![Text Analytics 연결된 서비스 화면 스크린샷](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. 사용하려는 구독을 선택하고, Text Analytics 서비스의 이름을 선택하거나 **편집** 링크를 선택하여 자동으로 생성된 이름을 수정하고 리소스 그룹 및 가격 책정 계층을 선택합니다.

   ![리소스 그룹 및 가격 책정 계층 필드 스크린샷](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   가격 책정 계층에 대한 자세한 내용은 링크를 따릅니다.

1. **추가**를 선택하여 연결된 서비스에 대한 지원을 추가합니다.
   Visual Studio는 Text Analytics 서비스에 대한 연결을 지원하기 위해 프로젝트를 수정하여 NuGet 패키지, 구성 파일 항목 및 기타 변경 사항을 추가합니다. **출력 창**에는 프로젝트에 나타나는 결과에 대한 로그가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Text Analytics 서비스를 사용하여 텍스트 샘플에 대한 언어를 검색합니다.

1. Startup.cs에서 다음 using 문을 추가합니다.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. 구성 필드를 추가하고 Startup 클래스의 구성 필드를 초기화하여 프로그램에서 Configuration을 사용하도록 설정하는 생성자를 추가합니다.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. DemoTextAnalyzeController라는 Controllers 폴더에 클래스 파일을 추가하고 해당 내용을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    이 코드에는 Text Analytics API를 호출하는 데 사용할 수 있는 클라이언트 개체를 가져오는 GetTextAnalyzeClient와 지정된 텍스트에 대해 DetectLanguage를 호출하는 요청 처리기가 포함됩니다.

1. 위의 코드에서 사용되는 MyHandler 도우미 클래스를 추가합니다.

    ```csharp
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

1. Models 폴더에 모델의 클래스를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. 분석된 텍스트, 결정된 언어 및 분석의 신뢰도를 나타내는 점수를 표시하는 보기를 추가합니다. 이렇게 하려면 **Views** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 후 **보기**를 선택합니다. 나타나는 대화 상자에서 이름 _TextAnalyzeResult_를 지정하고 기본값을 그대로 적용하여 _TextAnalyzeResult.cshtml_이라는 새 파일을 **Views** 폴더에 추가하고 다음 콘텐츠를 복사합니다.
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. 예제를 로컬로 빌드하고 실행합니다. 일부 텍스트를 입력하고 Text Analytics가 감지하는 언어를 확인합니다.
   
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 이렇게 하면 Cognitive Service 및 관련 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 자습서에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **TYPE THE RESOURCE GROUP NAME:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Text Analytics 서비스 설명서](index.yml)를 읽고 Text Analytics 서비스에 대해 자세히 알아봅니다.
