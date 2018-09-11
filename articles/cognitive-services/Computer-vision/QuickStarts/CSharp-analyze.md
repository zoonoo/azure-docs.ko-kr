---
title: Computer Vision API C# 빠른 시작 로컬 이미지 분석 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 C#과 함께 Computer Vision을 사용하여 로컬 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 8196787df9f7fadedda72d525aee440afe7b7d34
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750606"
---
# <a name="quickstart-analyze-a-local-image---rest-c35"></a>빠른 시작: 로컬 이미지 분석 - REST, C&#35;

이 빠른 시작에서는 Computer Vision을 사용하여 로컬 이미지를 분석하고 시각적 특징을 추출합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="analyze-image-request"></a>이미지 분석 요청

[이미지 분석 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 사용하면 이미지 콘텐츠를 기반으로 시각적 특징을 추출할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하고 다음과 같이 반환할 특징을 선택할 수 있습니다.

* 이미지 콘텐츠와 관련된 자세한 태그 목록
* 완전한 문장으로 된 이미지 콘텐츠에 대한 설명
* 이미지에 포함된 모든 얼굴의 좌표, 성별 및 나이
* ImageType(클립 아트 또는 선 그리기)
* 기조색, 강조색, 이미지 흑백 여부
* 이 [분류](../Category-Taxonomy.md)에 정의된 범주
* 이미지에 성인 또는 성적인 내용이 암시된 콘텐츠가 포함되어 있나요?

샘플을 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Newtonsoft.Json NuGet 패키지를 설치합니다.
    1. 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭하고 **검색** 상자에 "Newtonsoft.Json"을 입력합니다.
    1. **Newtonsoft.Json**이 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. `Program.cs`를 다음 코드로 바꿉니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uriBase` 값을 구독 키를 가져온 위치로 변경합니다.
1. 프로그램을 실행합니다.
1. 프롬프트에서 로컬 이미지에 경로를 입력합니다.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same region in your REST call as you used to
        // get your subscription keys. For example, if you got your
        // subscription keys from westus, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Analyze an image:");
            Console.Write("Enter the path to the image you wish to analyze: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeAnalysisRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the analysis of the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to analyze.</param>
        static async Task MakeAnalysisRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters =
                    "visualFeatures=Categories,Description,Color";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Make the REST API call.
                    response = await client.PostAsync(uri, content);
                }

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="analyze-image-response"></a>이미지 분석 응답

성공적인 응답이 JSON 형식으로 반환됩니다. 예:

```json
{
   "categories": [
      {
         "name": "abstract_",
         "score": 0.00390625
      },
      {
         "name": "others_",
         "score": 0.0234375
      },
      {
         "name": "outdoor_",
         "score": 0.00390625
      }
   ],
   "description": {
      "tags": [
         "road",
         "building",
         "outdoor",
         "street",
         "night",
         "black",
         "city",
         "white",
         "light",
         "sitting",
         "riding",
         "man",
         "side",
         "empty",
         "rain",
         "corner",
         "traffic",
         "lit",
         "hydrant",
         "stop",
         "board",
         "parked",
         "bus",
         "tall"
      ],
      "captions": [
         {
            "text": "a close up of an empty city street at night",
            "confidence": 0.7965622853462756
         }
      ]
   },
   "requestId": "dddf1ac9-7e66-4c47-bdef-222f3fe5aa23",
   "metadata": {
      "width": 3733,
      "height": 1986,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "Black",
      "dominantColors": [
         "Black",
         "Grey"
      ],
      "accentColor": "666666",
      "isBWImg": true
   }
}
```

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 기본 Windows 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.


> [!div class="nextstepaction"]
> [C#으로 Computer Vision 사용](../Tutorials/CSharpTutorial.md)
