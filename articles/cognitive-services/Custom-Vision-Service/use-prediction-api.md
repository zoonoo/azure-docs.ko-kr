---
title: 예측 엔드포인트를 사용하여 Custom Vision 분류자를 통해 이미지를 프로그래밍 방식으로 테스트
titlesuffix: Azure Cognitive Services
description: API를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046073"
---
# <a name="use-your-model-with-the-prediction-api"></a>예측 API 사용 하 여 모델을 사용 하 여

모델을 학습 한 후에 예측 API 끝점에 제출 하 여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다.

> [!NOTE]
> 이 문서에서는 C#을 사용하여 예측 API에 이미지를 제출하는 방법을 보여 줍니다. 자세한 내용 및 예제에 대 한 참조를 [예측 API 참조](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)합니다.

## <a name="publish-your-trained-iteration"></a>학습된 반복 게시

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다.

예측 API에 대 한 이미지를 제출 하려면 먼저 선택 하 여 수행할 수 있는 예측에 대 한 반복을 게시할 __게시__ 게시 된 반복에 대 한 이름을 지정 합니다. 이렇게 하면 모델 사용자 지정 비전 Azure 리소스의 예측 API에 액세스할 수 있습니다.

![게시 단추 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/unpublished-iteration.png)

모델이 성공적으로 게시 되 면 왼쪽 사이드바에서 반복 옆에 있는 표시 "게시" 레이블을 표시 하 고 해당 이름을 반복의 설명에 표시 됩니다.

![게시 된 반복의 이름과 게시 레이블 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

모델에 게시 되 면 선택 하 여 필요한 정보를 검색할 수 있습니다 __예측 URL__합니다. 예측 API를 사용 하 여에 대 한 정보를 사용 하 여 대화 상자가 열립니다 포함 된 __예측 URL__ 및 __예측 키__합니다.

![성능 탭은 예측 URL 단추 주위에 빨간색 사각형으로 표시 됩니다.](./media/use-prediction-api/published-iteration-prediction-url.png)

![이미지 파일 및 예측 키 값을 사용 하 여에 대 한 예측 URL 값 주위에 빨간색 직사각형이 있는 성능 탭 표시 됩니다.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 프로그램 __예측 키__ 에서 찾을 수 있습니다를 [Azure portal](https://portal.azure.com) 프로젝트를 사용 하 여 사용자 지정 비전 Azure 리소스의 연결에 대 한 페이지를 __키__ 블레이드입니다.

이 가이드에서는 사용 하는 로컬 이미지에서 URL 복사 **이미지 파일이 있는 경우** 임시 위치에 있습니다. 해당 복사 __예측 키__ 값도 합니다.

## <a name="create-the-application"></a>애플리케이션 만들기

1. Visual Studio에서 만들 새 C# 콘솔 응용 프로그램입니다.

1. 다음 코드를 __Program.cs__ 파일의 본문으로 사용합니다.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

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

1. 다음 정보를 변경합니다.
   * 설정 된 `namespace` 필드를 프로젝트의 이름입니다.
   * 자리 표시자 `<Your prediction key>` 이전에 검색 하는 키 값을 사용 하 여 합니다.
   * 자리 표시자 `<Your prediction URL>` 이전에 검색 URL을 사용 하 여 합니다.

## <a name="run-the-application"></a>애플리케이션 실행

응용 프로그램을 실행 하면 콘솔에서 이미지 파일 경로 입력 하 라는 메시지가 표시 됩니다. 이미지는 예측 API에 제출 하 고 예측 결과 JSON 형식 문자열로 반환 됩니다. 다음은 예제 응답입니다.

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

이 가이드에서는 사용자 지정 이미지 분류자/탐지기 이미지 하 고 사용 하 여 프로그래밍 방식으로 응답을 받을 제출 하는 방법을 배웠습니다 합니다 C# SDK. 다음을 사용 하 여 종단 간 시나리오를 완료 하는 방법을 알아봅니다 C#, 또는 다른 언어 SDK를 사용 하 여 시작 합니다.

* [빠른 시작:.NET SDK](csharp-tutorial.md)
* [빠른 시작: Python SDK](python-tutorial.md)
* [빠른 시작: Java SDK](java-tutorial.md)
* [빠른 시작: Node SDK](node-tutorial.md)
* [빠른 시작: Go SDK](go-tutorial.md)
