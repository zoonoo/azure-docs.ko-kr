---
title: '빠른 시작: 이미지에서 얼굴 감지 - SDK, C#'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Face Windows C# 클라이언트 라이브러리를 사용하여 이미지의 얼굴을 감지합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: b5fd10aaab28303079d3eef9c4c5c25207bb672e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344985"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>빠른 시작: C&#35;을 사용하여 이미지에서 얼굴 감지 - 얼굴

이 빠른 시작에서는 Face Windows 클라이언트 라이브러리를 사용하여 이미지에서 사람 얼굴을 감지합니다.

## <a name="prerequisites"></a>필수 조건

* 샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
* [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) 클라이언트 라이브러리 NuGet 패키지. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="detectwithurlasync-method"></a>DetectWithUrlAsync 메서드

> [!TIP]
> [Github](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face)에서 최신 코드를 Visual Studio 솔루션으로 가져옵니다.

`DetectWithUrlAsync` 및 `DetectWithStreamAsync` 메서드는 원격 및 로컬 이미지 각각에 대해 [Face - 감지 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 래핑합니다. 이 메서드를 사용하여 이미지에서 얼굴을 감지하고 다음을 포함한 얼굴 특성을 반환할 수 있습니다.

* 얼굴 Face: 여러 Face API 시나리오에 사용되는 고유 ID입니다.
* 얼굴 사각형: 이미지에서 얼굴의 위치를 나타내는 왼쪽, 위쪽, 너비 및 높이입니다.
* 랜드마크: 얼굴 구성 요소의 중요한 위치를 가리키는 27포인트 얼굴 랜드마크 배열입니다.
* 연령, 성별, 웃는 정도, 머리 포즈, 얼굴의 털 등을 포함한 얼굴 특성입니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Face 클라이언트 라이브러리 NuGet 패키지를 설치합니다.
    1. 맨 위의 메뉴에서 **도구**를 클릭하고, **NuGet 패키지 관리자**를 선택한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
    1. **찾아보기** 탭을 클릭한 다음, **시험판 포함**을 선택합니다.
    1. **검색** 상자에서 “Microsoft.Azure.CognitiveServices.Vision.Face”를 입력합니다.
    1. **Microsoft.Azure.CognitiveServices.Vision.Face**가 표시될 때 선택한 다음, 프로젝트 이름 옆의 확인란을 클릭하고, **설치**를 클릭합니다.
1. *Program.cs*를 다음 코드로 바꿉니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `faceEndpoint`를 구독 키와 연결된 Azure 지역으로 변경합니다.
1. 필요에 따라 <`LocalImage>`를 로컬 이미지의 경로 및 파일 이름으로 바꿉니다(설정되지 않으면 무시됨).
1. 필요에 따라 `remoteImageUrl`을 다른 이미지로 설정합니다.
1. 프로그램을 실행합니다.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>DetectWithUrlAsync 응답

성공적인 응답에는 이미지에 있는 각 얼굴에 대한 성별과 나이가 표시됩니다.

원시 JSON 출력의 예제가 필요하면 [API 빠른 시작: C#을 사용하여 이미지에서 얼굴 감지](CSharp.md)를 참조하세요.

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>다음 단계

Face 서비스를 사용하여 이미지에서 얼굴을 감지하는 WPF Windows 응용 프로그램을 만드는 방법에 대해 알아봅니다. 응용 프로그램이 각 얼굴 주위에 프레임을 그린 다음, 상태 표시줄에 얼굴에 대한 설명을 표시합니다.

> [!div class="nextstepaction"]
> [자습서: 이미지에서 얼굴을 감지하고 포착하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)
