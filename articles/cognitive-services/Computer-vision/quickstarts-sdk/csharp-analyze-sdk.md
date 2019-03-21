---
title: '빠른 시작: 이미지 분석 - SDK, C#'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Computer Vision Windows C# 클라이언트 라이브러리를 사용하여 이미지를 분석합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 21ee4f8b0fe20588646287945ba35efa5bc55606
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542984"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>빠른 시작: Computer Vision SDK 및 C#을 사용하여 이미지 분석

이 빠른 시작에서는 C#용 Computer Vision 클라이언트 라이브러리를 사용하여 시각적 기능을 추출하기 위해 로컬 및 원격 이미지를 모두 분석합니다. 원한다면 GitHub의 [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) 리포지토리에서 전체 샘플 앱으로 이 가이드의 코드를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.
* [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) 클라이언트 라이브러리 NuGet 패키지. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="create-and-run-the-sample-application"></a>애플리케이션 예제 만들기 및 실행

샘플을 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Computer Vision 클라이언트 라이브러리 NuGet 패키지를 설치합니다.
    1. 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭하고, **검색** 상자에 "Microsoft.Azure.CognitiveServices.Vision.ComputerVision"을 입력합니다.
    1. **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**이 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. *Program.cs* 내용을 다음 코드로 바꿉니다. `AnalyzeImageAsync` 및 `AnalyzeImageInStreamAsync` 메서드는 원격 및 로컬 이미지 각각에 대해 [이미지 REST API 분석](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)을 래핑합니다. 

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
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
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
            }
        }
    }
    ```

1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `computerVision.Endpoint`를 구독 키와 연결된 Azure 지역으로 변경합니다.
1. `<LocalImage>`를 로컬 이미지의 경로 및 파일 이름으로 바꿉니다.
1. 필요에 따라 `remoteImageUrl`을 다른 이미지 URL로 설정합니다.
1. 프로그램을 실행합니다.

## <a name="examine-the-response"></a>응답 검사

성공적인 응답은 각 이미지에 대해 가장 관련성이 높은 캡션을 표시합니다. 다른 이미지 데이터를 출력하도록 `DisplayResults` 메서드를 변경할 수 있습니다. 자세한 내용은 [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) 메서드를 참조하세요.

원시 JSON 출력의 예제는 [API 빠른 시작: C#을 사용하여 로컬 이미지 분석](../QuickStarts/CSharp-analyze.md#examine-the-response)을 참조하세요.

```
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
