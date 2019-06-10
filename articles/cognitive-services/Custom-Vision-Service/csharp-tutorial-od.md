---
title: '빠른 시작: C#용 Custom Vision SDK를 사용하여 개체 검색 프로젝트 만들기'
titlesuffix: Azure Cognitive Services
description: C#과 함께 .NET SDK를 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 교육하고, 개체를 검색합니다.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 6f4b13e5fb2dc8ed595999bfc8a2abe4db15dcbb
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393904"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>빠른 시작: Custom Vision .NET SDK를 사용하여 개체 검색 프로젝트 만들기

이 문서에서는 C#과 함께 Custom Vision SDK를 사용하여 개체 검색 모델 빌드를 시작할 수 있도록 도와주는 정보와 샘플 코드를 제공합니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. .NET 응용 프로그램을 빌드하기 위한 템플릿으로 이 예제를 사용하세요. 

## <a name="prerequisites"></a>필수 조건

- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK 및 샘플 코드 다운로드

Custom Vision을 사용하는 .NET 앱을 작성하려면 Custom Vision NuGet 패키지가 필요합니다. 이러한 요소는 여러분이 다운로드할 샘플 프로젝트에 포함되어 있지만, 여기서 개별적으로 액세스할 수도 있습니다.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) 프로젝트를 복제 또는 다운로드합니다. **CustomVision/ObjectDetection** 폴더로 이동하여 Visual Studio에서 _ObjectDetection.csproj_를 엽니다.

이 Visual Studio 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)를 통해 액세스할 수 있는 __My New Project__ 라는 새로운 Custom Vision 프로젝트를 만듭니다. 그런 다음, 이미지를 업로드하여 개체 검색 모델을 교육하고 테스트합니다. 이 프로젝트의 모델은 이미지에서 포트와 가위를 검색하는 교육을 받습니다.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>코드 이해

_Program.cs_ 파일을 열고 코드를 검사합니다. **Main** 메서드의 적절한 정의에 구독 키를 삽입합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

엔드포인트 매개 변수는 Custom Vision 리소스를 포함하는 Azure 리소스 그룹이 생성된 위치를 가리켜야 합니다. 이 예제에서는 미국 중남부 지역이라고 가정하고 다음 항목을 사용하겠습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>새 Custom Vision Service 프로젝트 만들기

다음 코드는 개체 검색 프로젝트를 만듭니다. 생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. 다음 코드는 각 샘플 이미지를 태그가 지정된 지역과 연결합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

그런 다음, 이 연결 맵을 사용하여 해당 지역 좌표로 각 샘플 이미지를 업로드합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

이제 모든 샘플 이미지가 업로드되었으며, 각 이미지에는 태그(**포크** 또는 **가위**) 및 해당 태그와 연결된 픽셀 사각형이 있습니다.

### <a name="train-the-project"></a>프로젝트 학습

이 코드는 프로젝트의 첫 번째 교육 반복을 만듭니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>현재 반복 게시

게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>예측 엔드포인트 만들기

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>예측 엔드포인트 사용

이 스크립트 부분은 테스트 이미지를 로드하고, 모델 엔드포인트를 쿼리하고, 예측 데이터를 콘솔에 출력합니다.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, stream);

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>애플리케이션 실행

응용 프로그램이 실행되면 콘솔 창이 열리고 다음 출력이 기록됩니다.

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

그런 다음, 테스트 이미지(**Images/Test/** 에 있음)에 태그가 적절하게 지정되는지, 검색 지역이 올바른지 확인할 수 있습니다. 이제 아무 키를 눌러 응용 프로그램을 종료할 수 있습니다.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 교육을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 교육하고 테스트해야 하는 경우가 많습니다. 다음 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](test-your-model.md)
