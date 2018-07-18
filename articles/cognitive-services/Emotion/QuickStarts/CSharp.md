---
title: Emotion API C# 빠른 시작 | Microsoft Docs
description: Cognitive Services에서 C#으로 Emotion API를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016568"
---
# <a name="emotion-api-c-quick-start"></a>Emotion API C# 빠른 시작

> [!IMPORTANT]
> Video API 미리 보기는 2017년 10월 30일에 종료됩니다. 비디오의 정보를 빠르게 확인하려면 새 [Video Indexer API 미리 보기](https://azure.microsoft.com/services/cognitive-services/video-indexer/)를 사용해 보세요. 음성, 얼굴, 성격 및 감정을 감지하여 검색 결과와 같은 콘텐츠 검색 환경을 향상시키는 데 사용할 수 있습니다. 자세히 알아보려면 [Video Indexer 미리 보기](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) 개요를 참조하세요.

이 문서에서는 C#에서 [Emotion API Recognize 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다. 이미지의 한 명 이상의 사람이 나타내는 감정을 인식하는 데 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
* Cognitive Services [Emotion API Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)를 구합니다.
* 무료 [구독 키](https://azure.microsoft.com/try/cognitive-services/)를 가져옵니다.

## <a name="emotion-recognition-c-example-request"></a>감정 인식 C# 예제 요청

Visual Studio에서 새 콘솔 솔루션을 만든 후 Program.cs를 다음 코드로 바꿉니다. 구독 키를 획득한 지역을 사용하도록 `string uri`를 변경합니다. **Ocp-Apim-Subscription-Key** 값을 유효한 구독 키로 바꿉니다. 구독 키를 찾으려면 Azure Portal로 이동합니다. 왼쪽의 탐색 창에 있는 **키** 섹션 아래에서 Emotion API 리소스를 찾습니다. 마찬가지로 **끝점** 아래에 나열된 리소스에 대한 **개요** 패널에서 적절한 연결 URI를 가져올 수 있습니다.

![API 리소스 키](../../media/emotion-api/keys.png)

요청의 응답을 처리하려면 `Newtonsoft.Json`과 같은 라이브러리를 사용합니다. 이러한 방식으로 JSON 문자열을 토큰이라는 관리 가능한 개체 시리즈로 처리할 수 있습니다. 패키지에 이 라이브러리를 추가하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 그런 후 **Newtonsoft**를 검색합니다. 첫 번째 결과는 **Newtonsoft.Json**이 됩니다. **설치**를 선택합니다. 이제 응용 프로그램에서 이 라이브러리를 참조할 수 있습니다.

![Newtonsoft.Json 설치](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>감정 인식 샘플 응답
호출이 성공하면 얼굴 항목 및 연결된 감정 점수 배열이 반환됩니다. 이러한 결과는 얼굴 사각형 크기의 내림차순으로 순위가 지정됩니다. 빈 응답은 검색된 얼굴이 없는 것을 나타냅니다. 감정 항목에는 다음 필드가 포함됩니다.

* faceRectangle: 이미지에서 얼굴의 사각형 위치입니다.
* scores: 이미지의 각 얼굴에 대한 감정 점수입니다. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
