---
title: '자습서: Face API C#'
titleSuffix: Azure Cognitive Services
description: Cognitive Services Face API를 사용하여 이미지에서 얼굴 특성을 감지하는 간단한 Windows 앱을 만듭니다.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: 492161a9a47627db8a06686daf953e99ca652e02
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827672"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Visual Studio의 연결된 서비스를 사용하여 Cognitive Services Face API에 연결

Cognitive Services Face API를 사용하여 사진의 얼굴을 감지, 분석 및 구성하고 태그를 지정할 수 있습니다.

이 문서 및 함께 제공되는 문서에서는 Cognitive Services Face API용 Visual Studio 연결된 서비스 기능 사용에 대한 세부 정보를 제공합니다. 이 기능은 Cognitive Services 확장이 설치된 Visual Studio 2017 15.7 이상에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- **Web Development** 워크로드가 설치된 Visual Studio 2017 버전 15.7 이상. [여기에서 다운로드하세요](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>프로젝트를 만들고 Cognitive Services Face API에 대한 지원 추가

1. 새 ASP.NET Core 웹 프로젝트를 만듭니다. 빈 프로젝트 템플릿을 사용합니다. 

1. **솔루션 탐색기**에서 **추가** > **연결된 서비스**를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.

   ![연결된 서비스 추가 메뉴 항목](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. 사용 가능한 서비스 메뉴에서 **Cognitive Services Face API**를 선택합니다.

   ![연결할 서비스를 선택합니다.](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다.

   ![구독 선택](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. 사용하려는 구독을 선택하고, Face API의 이름을 선택하거나 편집 링크를 선택하여 자동으로 생성된 이름을 수정하고 리소스 그룹 및 가격 책정 계층을 선택합니다.

   ![연결된 서비스 세부 정보 편집](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   가격 책정 계층에 대한 자세한 내용은 링크를 따릅니다.

1. 추가를 선택하여 연결된 서비스에 대한 지원을 추가합니다.
   Visual Studio는 Face API에 대한 연결을 지원하기 위해 프로젝트를 수정하여 NuGet 패키지, 구성 파일 항목 및 기타 변경 사항을 추가합니다.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Face API를 사용하여 이미지에서 얼굴 특성 감지

1. Startup.cs에서 다음 using 문을 추가합니다.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 구성 필드를 추가하고 Startup 클래스의 구성 필드를 초기화하여 프로그램에서 Configuration을 사용하도록 설정하는 생성자를 추가합니다.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. 프로젝트의 wwwroot 폴더에 images 폴더를 추가하고 wwwroot 폴더에 이미지 파일을 추가합니다. 예를 들어, 이 [Face API 페이지](https://azure.microsoft.com/services/cognitive-services/face/)에 나오는 이미지 중 하나를 사용할 수 있습니다. 이미지 중 하나를 마우스 오른쪽 단추로 클릭하고, 로컬 하드 드라이브에 저장한 후 솔루션 탐색기에서 images 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **기존 항목**을 선택하여 프로젝트에 추가합니다. 프로젝트는 솔루션 탐색기에서 다음과 같이 표시됩니다.
 
   ![이미지 파일이 있는 images 폴더](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. 이미지 파일을 마우스 오른쪽 단추로 클릭하고, 속성을 선택한 후 **변경된 내용만 복사**를 선택합니다.

   ![변경된 내용만 복사](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Configure 메서드를 다음 코드로 바꾸어 Face API에 액세스하고 이미지를 테스트합니다. imagePath 문자열을 얼굴 이미지에 대한 올바른 경로 및 파일 이름으로 변경합니다.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    이 단계의 코드는 연결된 서비스를 추가했을 때 추가한 키를 사용하여 Face REST API를 호출하는 HTTP 요청을 생성합니다.

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

1. 웹 애플리케이션을 실행하고 Face API가 이미지에서 찾은 결과를 확인합니다.
 
   ![Face API 이미지 및 서식이 지정된 결과](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 이렇게 하면 Cognitive Service 및 관련 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. **리소스 그룹 이름 입력** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Face API 설명서](Overview.md)를 읽어 Face API에 대해 자세히 알아봅니다.
