---
title: Visual Studio 연결된 서비스 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Visual Studio 연결된 서비스 기능을 사용하여 ASP.NET Core 웹 애플리케이션의 Computer Vision API에 연결합니다.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 24d9a5fa1e9c2f44ef32ac1fc05ad09f8a550f12
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827743"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Visual Studio의 연결된 서비스를 사용하여 Computer Vision API에 연결

이 문서 및 해당 도우미 문서 Cognitive Services Computer Vision API에 대 한 Visual Studio 연결 된 서비스 기능을 사용 하는 것에 대 한 세부 정보를 제공 합니다. 이 기능은 Cognitive Services 확장이 설치된 Visual Studio 2017 15.7 이상에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- **Web Development** 워크로드가 설치된 Visual Studio 2017 버전 15.7 이상. [여기에서 다운로드하세요](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>프로젝트에 Cognitive Services Computer Vision API에 대한 지원 추가

1. 새 ASP.NET Core 웹 프로젝트를 만듭니다. 빈 프로젝트 템플릿을 사용합니다. 

1. **솔루션 탐색기**에서 **추가** > **연결된 서비스**를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.

   ![Visual Studio 프로젝트에서 오른쪽 클릭 메뉴: 추가 > 연결된 서비스](../media/vs-common/Connected-Service-Menu.PNG)

1. 사용 가능한 서비스 메뉴에서 **Cognitive Services Computer Vision API**를 선택합니다.

   ![연결된 서비스 메뉴: 이미지를 분석하는 중...이 설명되어 있습니다.](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다.

   ![구독 드롭다운이 강조 표시된 Computer Vision API 창](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. 사용하려는 구독을 선택하고, Computer Vision API의 이름을 선택하거나 편집 링크를 선택하여 자동으로 생성된 이름을 수정하고 리소스 그룹 및 가격 책정 계층을 선택합니다.

   ![연결된 서비스 세부 정보 편집](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   가격 책정 계층에 대한 자세한 내용은 링크를 따릅니다.

1. 추가를 선택하여 연결된 서비스에 대한 지원을 추가합니다.
   Visual Studio는 Computer Vision API에 대한 연결을 지원하기 위해 프로젝트를 수정하여 NuGet 패키지, 구성 파일 항목 및 기타 변경 사항을 추가합니다. 출력 창에는 프로젝트에 나타나는 결과에 대한 로그가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Computer Vision API를 사용하여 이미지의 특성 감지

1. Startup.cs에서 다음 using 문을 추가합니다.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 구성 필드를 추가하고 `Startup` 클래스의 구성 필드를 초기화하여 프로그램에서 Configuration을 사용하도록 설정하는 생성자를 추가합니다.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. 프로젝트의 wwwroot 폴더에 images 폴더를 추가하고 wwwroot 폴더에 이미지 파일을 추가합니다. 예를 들어, 이 [Computer Vision API 페이지](https://azure.microsoft.com/services/cognitive-services/computer-vision/)에 나오는 이미지 중 하나를 사용할 수 있습니다. 이미지 중 하나를 마우스 오른쪽 단추로 클릭하고, 로컬 하드 드라이브에 저장한 후 솔루션 탐색기에서 images 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **기존 항목**을 선택하여 프로젝트에 추가합니다. 프로젝트는 솔루션 탐색기에서 다음과 같이 표시됩니다. 
  
   ![선택한 이미지 파일을 사용한 솔루션 탐색기 보기의 스크린샷](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. 이미지 파일을 마우스 오른쪽 단추로 클릭하고, 속성을 선택한 후 **변경된 내용만 복사**를 선택합니다. 

   ![이미지 속성 창에서 새로 복사할 출력 디렉터리 집합으로 복사](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Configure 메서드를 다음 코드로 바꾸어 Computer Vision API에 액세스하고 이미지를 테스트합니다.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    여기에 나오는 코드는 URI 및 이미지를 Computer Vision REST API 호출을 위한 이진 콘텐츠로 사용하여 HTTP 요청을 생성합니다.

1. 도우미 함수 GetImageAsByteArray 및 JsonPrettyPrint를 추가합니다.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. 웹 애플리케이션을 실행하고 이미지에서 Computer Vision API가 이미지에서 찾은 결과를 확인합니다.

   ![Computer Vision API 이미지 및 서식이 지정된 결과](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 이렇게 하면 Cognitive Service 및 관련 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Computer Vision API 설명서](Home.md)를 읽어보고 Computer Vision API에 대해 자세히 알아봅니다.
