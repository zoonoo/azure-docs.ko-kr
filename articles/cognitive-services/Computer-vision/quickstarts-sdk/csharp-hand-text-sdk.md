---
title: '빠른 시작: 필기 텍스트 추출 - SDK, C#'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Computer Vision Windows C# 클라이언트 라이브러리를 사용하여 이미지에서 텍스트를 추출합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 656e2519dc814baffa2f1c427d46e66054969e25
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357163"
---
# <a name="quickstart-extract-handwritten-text-using-the-computer-vision-c-sdk"></a>빠른 시작: Computer Vision C# SDK를 사용하여 필기 텍스트 추출

이 빠른 시작에서는 C#용 Computer Vision SDK를 사용하여 이미지에서 필기 또는 인쇄 텍스트를 추출합니다. 원한다면 GitHub의 [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) 리포지토리에서 전체 샘플 앱으로 이 가이드의 코드를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Computer Vision 구독 키입니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다.
* [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) 클라이언트 라이브러리 NuGet 패키지. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="create-and-run-the-sample-app"></a>샘플 앱 만들기 및 실행

샘플을 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Computer Vision 클라이언트 라이브러리 NuGet 패키지를 설치합니다.
    1. 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭하고, **검색** 상자에 "Microsoft.Azure.CognitiveServices.Vision.ComputerVision"을 입력합니다.
    1. **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**이 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. `Program.cs`를 다음 코드로 바꿉니다. `BatchReadFileAsync` 및 `BatchReadFileInStreamAsync` 메서드는 원격 및 로컬 이미지 각각에 대해 [일괄 읽기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)를 래핑합니다. `GetReadOperationResultAsync` 메서드는 [읽기 작업 결과 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d)를 래핑합니다.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<Subscription key>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the westcentralus
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Read text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to read the text
                BatchReadFileHeaders textHeaders =
                    await computerVision.BatchReadFileAsync(
                        imageUrl);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    BatchReadFileInStreamHeaders textHeaders =
                        await computerVision.BatchReadFileInStreamAsync(
                            imageStream);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                ReadOperationResult result =
                    await computerVision.GetReadOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetReadOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var recResults = result.RecognitionResults;
                foreach (TextRecognitionResult recResult in recResults)
                {
                    foreach (Line line in recResult.Lines)
                    {
                        Console.WriteLine(line.Text);
                    }
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `computerVision.Endpoint`를 구독 키와 연결된 Azure 지역으로 변경합니다.
1. `<LocalImage>`를 로컬 이미지의 경로 및 파일 이름으로 바꿉니다.
1. 필요에 따라 `remoteImageUrl`을 다른 이미지로 설정합니다.
1. 프로그램을 실행합니다.

## <a name="examine-the-response"></a>응답 검사

성공적인 응답에는 각 이미지에 대해 인식된 텍스트 행이 출력됩니다.

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

[빠른 시작: 필기 텍스트 추출 - REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response)에서 API 호출에서 원시 JSON 출력 예를 참조하세요.

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
