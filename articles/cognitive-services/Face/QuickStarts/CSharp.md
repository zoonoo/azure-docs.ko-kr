---
title: '빠른 시작: Azure REST API 및 C#으로 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 이미지에서 얼굴을 감지하기 위해 C#과 함께 Azure Face REST API를 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 587005fa183c11a24ea2083ee28c8faca616b4a4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866647"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>빠른 시작: Face REST API 및 C#을 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 이미지에서 사람 얼굴을 감지하기 위해 C#과 함께 Azure Face REST API를 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **FaceDetection**으로 이름을 지정합니다. 
1. 솔루션에 다른 프로젝트가 있는 경우 이것을 단일 시작 프로젝트로 선택합니다.

## <a name="add-face-detection-code"></a>얼굴 감지 코드 추가

새 프로젝트의 *Program.cs* 파일을 엽니다. 여기에 이미지를 로드하고 얼굴을 감지하는 데 필요한 코드를 추가합니다.

### <a name="include-namespaces"></a>네임스페이스 포함

*Program.cs* 파일 위에 다음 `using` 문을 추가합니다.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>필수 필드 추가

**Program** 클래스에 다음 필드를 추가합니다. 이 데이터는 Face 서비스에 연결하는 방법과 입력 데이터를 가져올 위치를 지정합니다. `subscriptionKey` 필드를 구독 키의 값으로 업데이트해야 하며 올바른 지역 식별자가 포함되도록 `uriBase` 문자열을 변경해야 할 수도 있습니다(모든 지역 엔드포인트 목록에 대해서는 [Face API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조).


```csharp
// Replace <Subscription Key> with your valid subscription key.
const string subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.
const string uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>이미지 입력 수신

**Program** 클래스의 **Main** 메서드에 다음 코드를 추가합니다. 이렇게 하면 사용자에게 이미지 URL을 입력하라는 메시지가 콘솔에 표시됩니다. 그런 다음, 다른 메서드인 **MakeAnalysisRequest**를 호출하여 해당 위치에서 이미지를 처리합니다.

```csharp
// Get the path and filename to process from the user.
Console.WriteLine("Detect faces:");
Console.Write(
    "Enter the path to an image with faces that you wish to analyze: ");
string imageFilePath = Console.ReadLine();

if (File.Exists(imageFilePath))
{
    try
    {
        MakeAnalysisRequest(imageFilePath);
        Console.WriteLine("\nWait a moment for the results to appear.\n");
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
    }
}
else
{
    Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
}
Console.ReadLine();
```

### <a name="call-the-face-detection-rest-api"></a>얼굴 감지 REST API 호출

**Program** 클래스에 다음 메서드를 추가합니다. Face API에 대한 REST 호출을 구성하여 원격 이미지의 얼굴 정보를 감지합니다(검색할 얼굴 특성을 지정하는 `requestParameters` 문자열). 그런 다음, 출력 데이터를 JSON 문자열에 기록합니다.

다음 단계에서는 도우미 메서드를 정의합니다.

```csharp
// Gets the analysis of the specified image by using the Face REST API.
static async void MakeAnalysisRequest(string imageFilePath)
{
    HttpClient client = new HttpClient();

    // Request headers.
    client.DefaultRequestHeaders.Add(
        "Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request parameters. A third optional parameter is "details".
    string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
        "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
        "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

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

        // Execute the REST API call.
        response = await client.PostAsync(uri, content);

        // Get the JSON response.
        string contentString = await response.Content.ReadAsStringAsync();

        // Display the JSON response.
        Console.WriteLine("\nResponse:\n");
        Console.WriteLine(JsonPrettyPrint(contentString));
        Console.WriteLine("\nPress Enter to exit...");
    }
}
```

### <a name="process-the-input-image-data"></a>입력 이미지 데이터 처리

**Program** 클래스에 다음 메서드를 추가합니다. 지정된 URL의 이미지를 바이트 배열로 변환합니다.

```csharp
// Returns the contents of the specified file as a byte array.
static byte[] GetImageAsByteArray(string imageFilePath)
{
    using (FileStream fileStream =
        new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
    {
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }
}
```

### <a name="parse-the-json-response"></a>JSON 응답 구문 분석

**Program** 클래스에 다음 메서드를 추가합니다. 이렇게 하면 JSON 입력을 보다 쉽게 읽을 수 있도록 형식화합니다. 앱이 이 문자열 데이터를 콘솔에 씁니다.

```csharp
// Formats the given JSON string by adding line breaks and indents.
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    int offset = 0;
    int indentLength = 3;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\'':
                if (quote) ignore = !ignore;
                break;
        }

        if (quote)
            sb.Append(ch);
        else
        {
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', ++offset * indentLength));
                    break;
                case '}':
                case ']':
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', --offset * indentLength));
                    sb.Append(ch);
                    break;
                case ',':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', offset * indentLength));
                    break;
                case ':':
                    sb.Append(ch);
                    sb.Append(' ');
                    break;
                default:
                    if (ch != ' ') sb.Append(ch);
                    break;
            }
        }
    }

    return sb.ToString().Trim();
}
```

## <a name="run-the-app"></a>앱 실행

성공적인 응답은 얼굴 데이터를 쉽게 읽을 수 있는 JSON 형식으로 표시합니다. 예: 

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Face API와 함께 REST 호출을 사용하여 이미지의 얼굴을 감지하고 해당 특성을 반환하는 간단한 .NET 콘솔 애플리케이션을 만들었습니다. 다음으로, Face API 참조 설명서를 살펴보고 지원되는 시나리오에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
