---
title: '빠른 시작: 썸네일 생성 - SDK, C#'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Computer Vision Windows C# 클라이언트 라이브러리를 사용하여 이미지의 썸네일을 생성합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 78ffd9628c7a65ae60d457bbff3631ea261649d5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603449"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-sdk-and-c"></a>빠른 시작: Computer Vision SDK 및 C#을 사용하여 썸네일 생성

이 빠른 시작에서는 C#용 Computer Vision SDK를 사용하여 이미지에서 스마트하게 자른 썸네일을 생성합니다. 원한다면 GitHub의 [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) 리포지토리에서 전체 샘플 앱으로 이 가이드의 코드를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Computer Vision 구독 키입니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다.
* [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) 클라이언트 라이브러리 NuGet 패키지. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="generatethumbnailasync-method"></a>GenerateThumbnailAsync 메서드

 이러한 메서드를 사용하여 이미지의 썸네일을 생성할 수 있습니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Computer Vision 클라이언트 라이브러리 NuGet 패키지를 설치합니다.
    1. 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭하고, **검색** 상자에 "Microsoft.Azure.CognitiveServices.Vision.ComputerVision"을 입력합니다.
    1. **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**이 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. `Program.cs`를 다음 코드로 바꿉니다. `GenerateThumbnailAsync` 및 `GenerateThumbnailInStreamAsync` 메서드는 원격 및 로컬 이미지 각각에 대해 [썸네일 API 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)를 래핑합니다. 

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageThumbnail
    {
        class Program
        {
            private const bool writeThumbnailToDisk = false;

            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

            private const int thumbnailWidth = 100;
            private const int thumbnailHeight = 100;

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

                Console.WriteLine("Images being analyzed ...\n");
                var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
                var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Create a thumbnail from a remote image
            private static async Task GetRemoteThumbnailAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                    thumbnailWidth, thumbnailHeight, imageUrl, true);

                string path = Environment.CurrentDirectory;
                string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
                string thumbnailFilePath =
                    path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

                // Save the thumbnail to the current working directory,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }

            // Create a thumbnail from a local image
            private static async Task GetLocalThumbnailAsnc(
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
                    Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                        thumbnailWidth, thumbnailHeight, imageStream, true);

                    string thumbnailFilePath =
                        localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    SaveThumbnail(thumbnail, thumbnailFilePath);
                }
            }

            // Save the thumbnail locally.
            // NOTE: This will overwrite an existing file of the same name.
            private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
            {
                if (writeThumbnailToDisk)
                {
                    using (Stream file = File.Create(thumbnailFilePath))
                    {
                        thumbnail.CopyTo(file);
                    }
                }
                Console.WriteLine("Thumbnail {0} written to: {1}\n",
                    writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
            }
        }
    }
    ```

1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `computerVision.Endpoint`를 구독 키와 연결된 Azure 지역으로 변경합니다.
1. 필요에 따라 `<LocalImage>`를 로컬 이미지의 경로 및 파일 이름으로 바꿉니다(설정되지 않으면 무시됨).
1. 필요에 따라 `remoteImageUrl`을 다른 이미지로 설정합니다.
1. 필요에 따라 `writeThumbnailToDisk`를 `true`로 설정하여 썸네일을 디스크에 저장합니다.
1. 프로그램을 실행합니다.


## <a name="examine-the-response"></a>응답 검사

성공적인 응답은 로컬로 각 이미지의 썸네일을 저장하고 썸네일의 위치를 표시합니다. 예:

```console
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
