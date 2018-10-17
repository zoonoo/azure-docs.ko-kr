---
title: '예: 예측 엔드포인트를 사용하여 Custom Vision 분류자를 통해 이미지를 프로그래밍 방식으로 테스트'
titlesuffix: Azure Cognitive Services
description: API를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 3a81f3cef6aaeb5c98022d9fc93f4d84f3f58a6e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363652"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>예측 엔드포인트를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트합니다.

모델을 학습한 후 해당 모델을 예측 API에 제출하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 

> [!NOTE]
> 이 문서에서는 C#을 사용하여 예측 API에 이미지를 제출하는 방법을 보여 줍니다. 자세한 내용과 API 사용 예제는 [예측 API 참조](https://go.microsoft.com/fwlink/?linkid=865445)를 참조하세요.

## <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다. __예측 키__를 포함하여 예측 API 사용에 대한 정보를 표시하려면 __예측 URL__을 선택합니다. Azure 리소스에 연결된 프로젝트의 경우 __예측 키__는 __키__ 아래의 연결된 Azure 리소스에 대한 [Azure Portal](https://portal.azure.com) 페이지에서 찾을 수도 있습니다. 응용 프로그램에서 사용하기 위해 다음 정보를 복사합니다.

* __이미지 파일__을 사용하기 위한 __URL__.
* __Prediction-key__ 값.

> [!TIP]
> 여러 개의 반복이 있는 경우, 기본값으로 설정하여 사용되는 반복을 제어할 수 있습니다. __반복__ 섹션에서 반복을 선택하고, 페이지 맨 위에서 __기본값으로 설정__을 선택합니다.

![예측 URL이 빨간색 사각형으로 둘러싸인 성능 탭이 표시됩니다.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>응용 프로그램 만들기

1. Visual Studio에서 새 C# 콘솔 응용 프로그램을 만듭니다.

2. 다음 코드를 __Program.cs__ 파일의 본문으로 사용합니다.

    > [!IMPORTANT]
    > 다음 정보를 변경합니다.
    >
    > * __namespace__를 프로젝트 이름으로 설정합니다.
    > * `client.DefaultRequestHeaders.Add("Prediction-Key",`로 시작하는 줄에서 이전에 받은 __Prediction-Key__ 값을 설정합니다.
    > * `string url =`로 시작하는 줄에서 이전에 받은 __URL__ 값을 설정합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>응용 프로그램 사용

응용 프로그램을 실행하는 경우 이미지 파일의 경로를 입력합니다. 이미지가 API에 제출되고 결과가 JSON 문서로 반환됩니다. 다음 JSON은 응답의 예입니다.

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>다음 단계

[모바일 사용을 위해 모델 내보내기](export-your-model.md)
