---
title: Computer Vision API C# 빠른 시작 OCR | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 C#과 함께 Computer Vision을 사용하여 이미지에서 인쇄된 텍스트를 추출합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 46193e7aa27285a887005a67bb662449e18d7b36
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750690"
---
# <a name="quickstart-extract-printed-text-ocr---rest-c35"></a>빠른 시작: 인쇄된 텍스트(OCR) 추출 - REST, C&#35;

이 빠른 시작에서는 Computer Vision을 사용하여 이미지에서 OCR(광학 문자 인식)이라고도 하는 인쇄된 텍스트를 추출합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="ocr-request"></a>OCR 요청

[OCR 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)를 사용하여 이미지에서 인쇄된 텍스트를 검색하고, 인식된 문자를 머신에서 사용 가능한 문자 스트림으로 추출할 수 있습니다.

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
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Make the REST API call.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                string requestParameters = "language=unk&detectOrientation=true";

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

## <a name="ocr-response"></a>OCR 응답

작업이 성공하면 반환되는 OCR 결과에는 텍스트, 지역의 경계 상자, 선 및 단어가 포함됩니다. 예:

```json
{
   "language": "en",
   "textAngle": -1.5000000000000335,
   "orientation": "Up",
   "regions": [
      {
         "boundingBox": "154,49,351,575",
         "lines": [
            {
               "boundingBox": "165,49,340,117",
               "words": [
                  {
                     "boundingBox": "165,49,63,109",
                     "text": "A"
                  },
                  {
                     "boundingBox": "261,50,244,116",
                     "text": "GOAL"
                  }
               ]
            },
            {
               "boundingBox": "165,169,339,93",
               "words": [
                  {
                     "boundingBox": "165,169,339,93",
                     "text": "WITHOUT"
                  }
               ]
            },
            {
               "boundingBox": "159,264,342,117",
               "words": [
                  {
                     "boundingBox": "159,264,64,110",
                     "text": "A"
                  },
                  {
                     "boundingBox": "255,266,246,115",
                     "text": "PLAN"
                  }
               ]
            },
            {
               "boundingBox": "161,384,338,119",
               "words": [
                  {
                     "boundingBox": "161,384,86,113",
                     "text": "IS"
                  },
                  {
                     "boundingBox": "274,387,225,116",
                     "text": "JUST"
                  }
               ]
            },
            {
               "boundingBox": "154,506,341,118",
               "words": [
                  {
                     "boundingBox": "154,506,62,111",
                     "text": "A"
                  },
                  {
                     "boundingBox": "248,508,247,116",
                     "text": "WISH"
                  }
               ]
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 기능을 검색하고, 분류하고, 태그를 지정하고, 설명하는 기본 Windows 응용 프로그램을 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API C# 자습서](../Tutorials/CSharpTutorial.md)
