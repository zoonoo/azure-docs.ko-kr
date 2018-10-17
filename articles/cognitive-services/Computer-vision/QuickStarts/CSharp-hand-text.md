---
title: '빠른 시작: 필기 텍스트 추출 - REST, C# - Computer Vision'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 C#과 함께 Computer Vision API를 사용하여 이미지의 필기 텍스트를 추출합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: v-deken
ms.openlocfilehash: bf7eae1bc7d0b0db1eaa37b2ab84dc21ef9712b7
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45628578"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-c35-in-computer-vision"></a>빠른 시작: Computer Vision에서 REST API 및 C#을 사용하여 필기 텍스트 추출

이 빠른 시작에서는 Computer Vision의 REST API를 사용하여 이미지의 필기 텍스트를 추출합니다. [텍스트 인식](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 및 [텍스트 인식 작업 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) 메서드를 사용하면 이미지의 필기 텍스트를 감지한 후 인식된 문자를 머신에서 사용 가능한 문자 스트림으로 추출할 수 있습니다.

> [!IMPORTANT]
> [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 메서드와 달리, [텍스트 인식](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 메서드는 비동기적으로 실행됩니다. 이 메서드는 성공한 응답의 본문에 있는 정보를 반환하지 않습니다. 대신 텍스트 인식 메서드는 `Operation-Content` 응답 헤더 필드의 값으로 URI를 반환합니다. 그런 다음, [텍스트 인식 작업 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) 메서드를 나타내는 이 URI를 호출하여 상태를 확인하고 텍스트 인식 메서드 호출 결과를 반환할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) 이상이 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. 구독 키를 가져오려면 [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="create-and-run-the-sample-application"></a>응용 프로그램 예제 만들기 및 실행

Visual Studio에서 샘플을 만들려면 다음 단계를 수행합니다.

1. Visual C# 콘솔 앱 템플릿을 사용하여 Visual Studio에서 새 Visual Studio 솔루션을 만듭니다.
1. Newtonsoft.Json NuGet 패키지를 설치합니다.
    1. 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭하고 **검색** 상자에 "Newtonsoft.Json"을 입력합니다.
    1. **Newtonsoft.Json**이 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. `Program.cs`의 코드를 다음 코드로 바꾼 다음, 필요한 경우 코드에서 다음 내용을 변경합니다.
    1. `subscriptionKey`의 값을 구독 키로 바꿉니다.
    1. 필요한 경우 `uriBase`의 값을 구독 키를 가져온 Azure 지역의 [텍스트 인식](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 메서드에 대한 엔드포인트 URL로 바꿉니다.
1. 프로그램을 실행합니다.
1. 프롬프트에서 로컬 이미지에 경로를 입력합니다.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the West Central US region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/recognizeText";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Handwriting Recognition:");
            Console.Write(
                "Enter the path to an image with handwritten text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadHandwrittenText(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the handwritten text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with handwritten text.</param>
        static async Task ReadHandwrittenText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameter.
                string requestParameters = "mode=Handwritten";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Two REST API methods are required to extract handwritten text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Recognize Text, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response header for the Recognize Text method contains the URI
                // of the second method, Get Recognize Text Operation Result, which
                // returns the results of the process in the response body.
                // The Recognize Text operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Handwriting
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the handwritten text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

                if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

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
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 응용 프로그램 예제는 다음 예제와 유사하게 콘솔 창에서 성공한 응답을 구문 분석하고 표시합니다.

```json
{
    "status": "Succeeded",
    "recognitionResult": {
        "lines": [
            {
                "boundingBox": [
                    99,
                    195,
                    1309,
                    45,
                    1340,
                    292,
                    130,
                    442
                ],
                "text": "when you write them down",
                "words": [
                    {
                        "boundingBox": [
                            152,
                            191,
                            383,
                            154,
                            341,
                            421,
                            110,
                            458
                        ],
                        "text": "when"
                    },
                    {
                        "boundingBox": [
                            436,
                            145,
                            607,
                            118,
                            565,
                            385,
                            394,
                            412
                        ],
                        "text": "you"
                    },
                    {
                       "boundingBox": [
                            644,
                            112,
                            873,
                            76,
                            831,
                            343,
                            602,
                            379
                        ],
                        "text": "write"
                    },
                    {
                        "boundingBox": [
                            895,
                            72,
                            1092,
                            41,
                            1050,
                            308,
                            853,
                            339
                        ],
                        "text": "them"
                    },
                    {
                        "boundingBox": [
                            1140,
                            33,
                            1400,
                            0,
                            1359,
                            258,
                            1098,
                            300
                        ],
                        "text": "down"
                    }
                ]
            },
            {
                "boundingBox": [
                    142,
                    222,
                    1252,
                    62,
                    1269,
                    180,
                    159,
                    340
                ],
                "text": "You remember things better",
                "words": [
                    {
                        "boundingBox": [
                            140,
                            223,
                            267,
                            205,
                            288,
                            324,
                            162,
                            342
                        ],
                        "text": "You"
                    },
                    {
                        "boundingBox": [
                            314,
                            198,
                            740,
                            137,
                            761,
                            256,
                            335,
                            317
                        ],
                        "text": "remember"
                    },
                    {
                        "boundingBox": [
                            761,
                            134,
                            1026,
                            95,
                            1047,
                            215,
                            782,
                            253
                        ],
                        "text": "things"
                    },
                    {
                        "boundingBox": [
                            1046,
                            92,
                            1285,
                            58,
                            1307,
                            177,
                            1068,
                            212
                        ],
                        "text": "better"
                    }
                ]
            },
            {
                "boundingBox": [
                    155,
                    405,
                    537,
                    338,
                    557,
                    449,
                    175,
                    516
                ],
                "text": "by hand",
                "words": [
                    {
                        "boundingBox": [
                            146,
                            408,
                            266,
                            387,
                            301,
                            495,
                            181,
                            516
                        ],
                        "text": "by"
                    },
                    {
                        "boundingBox": [
                            290,
                            383,
                            569,
                            334,
                            604,
                            443,
                            325,
                            491
                        ],
                        "text": "hand"
                    }
                ]
            }
        ]
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Visual Studio 솔루션을 삭제합니다. 이렇게 하려면 파일 탐색기를 열고, Visual Studio 솔루션을 만든 폴더로 이동한 후, 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 기본 Windows 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API C# 자습서](../Tutorials/CSharpTutorial.md)
