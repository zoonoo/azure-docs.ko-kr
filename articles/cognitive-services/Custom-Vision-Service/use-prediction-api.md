---
title: '예제: 예측 엔드포인트를 사용하여 Custom Vision 분류자를 통해 이미지를 프로그래밍 방식으로 테스트'
titlesuffix: Azure Cognitive Services
description: API를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472730"
---
#  <a name="use-your-model-with-the-prediction-api"></a>예측 API 사용 하 여 모델을 사용 하 여

모델을 학습한 후 해당 모델을 예측 API에 제출하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다.

> [!NOTE]
> 이 문서에서는 C#을 사용하여 예측 API에 이미지를 제출하는 방법을 보여 줍니다. 자세한 내용과 API 사용 예제는 [예측 API 참조](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)를 참조하세요.

## <a name="publish-your-trained-iteration"></a>학습된 반복 게시

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다.

예측 API에 대 한 이미지를 제출 하려면 먼저 선택 하 여 수행할 수 있는 예측에 대 한 반복을 게시할 __게시__ 게시 된 반복에 대 한 이름을 지정 합니다. 이렇게 하면 모델을 사용자 지정 비전 Azure 리소스의 예측 API에 액세스할 수 있습니다. 

![게시 단추 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/unpublished-iteration.png)

모델이 성공적으로 게시 되 면 반복의 설명에 게시 된 반복의 이름 뿐만 아니라 왼쪽 사이드바에서 반복 옆에 있는 표시 "게시" 레이블이 표시 됩니다.

![게시 된 반복의 이름과 게시 레이블 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

모델에 게시 되 면 선택 하 여 예측 API를 사용 하는 방법에 대 한 정보를 검색할 수 있습니다 __예측 URL__합니다. 예측 API를 사용 하 여에 대 한 정보를 사용 하 여 아래와 같은 대화 상자가 열립니다 포함 된 __예측 URL__ 및 __예측 키__합니다.

![성능 탭은 예측 URL 단추 주위에 빨간색 사각형으로 표시 됩니다.](./media/use-prediction-api/published-iteration-prediction-url.png)

![이미지 파일 및 예측 키 값을 사용 하 여에 대 한 예측 URL 값 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 프로그램 __예측 키__ 에서 찾을 수 있습니다 합니다 [Azure Portal](https://portal.azure.com) 아래에 있는 사용자 지정 비전 Azure 리소스를 프로젝트에 연결 된 페이지 __키__. 

대화 상자에서 응용 프로그램에서 사용 하기 위해 다음 정보를 복사 합니다.

* __예측 URL__ 사용에 대 한는 __이미지 파일__합니다.
* __예측-키__ 값입니다.

## <a name="create-the-application"></a>애플리케이션 만들기

1. Visual Studio에서 새 C# 콘솔 애플리케이션을 만듭니다.

1. 다음 코드를 __Program.cs__ 파일의 본문으로 사용합니다.

    > [!IMPORTANT]
    > 다음 정보를 변경합니다.
    >
    > * __namespace__를 프로젝트 이름으로 설정합니다.
    > * 설정 된 __예측 키__ 시작 하는 줄 앞에서 검색 된 값 `client.DefaultRequestHeaders.Add("Prediction-Key",`합니다.
    > * 설정 된 __예측 URL__ 시작 하는 줄 앞에서 검색 된 값 `string url =`합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

## <a name="use-the-application"></a>애플리케이션 사용

응용 프로그램을 실행할 때 콘솔에서 이미지 파일 경로 입력할가 있습니다. 이미지는 예측 API에 전송 되 고 예측 결과 JSON 문서로 반환 됩니다. 다음 JSON은 응답의 예입니다.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>다음 단계

[모바일 사용을 위해 모델 내보내기](export-your-model.md)

[.NET Sdk 시작](csharp-tutorial.md)

[Python Sdk를 사용 하 여 시작](python-tutorial.md)

[Java Sdk 시작](java-tutorial.md)

[Node Sdk를 사용 하 여 시작](node-tutorial.md)

[Go Sdk를 사용 하 여 시작](go-tutorial.md)
