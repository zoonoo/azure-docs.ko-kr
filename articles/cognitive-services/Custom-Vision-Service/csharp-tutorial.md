---
title: '자습서: C#을 사용하여 Custom Vision Service를 위한 Windows 앱 빌드'
titlesuffix: Azure Cognitive Services
description: 기본 엔드포인트를 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 예측을 수행합니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366644"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>자습서: C# 응용 프로그램에서 Custom Vision Service 사용

C# 응용 프로그램에서 Custom Vision Service를 사용하는 방법을 알아봅니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 오픈 소스 예제를 Custom Vision Service API를 사용하여 사용자 고유의 Windows용 앱을 빌드하기 위한 템플릿으로 사용합니다.

## <a name="prerequisites"></a>필수 조건

* Windows용 Visual Studio 2017의 모든 버전

## <a name="get-the-custom-vision-sdk-and-samples"></a>Custom Vision SDK 및 샘플 다운로드
이 예제를 빌드하려면 다음 Custom Vision SDK NuGet 패키지가 필요합니다.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[C# 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision)과 함께 이미지를 다운로드할 수 있습니다.

## <a name="get-the-training-and-prediction-keys"></a>학습 및 예측 키 가져오기

이 예제에서 사용된 키를 가져오려면 [Custom Vision 웹 페이지](https://customvision.ai)로 가서 오른쪽 위에 있는 __기어 아이콘__을 선택합니다. __계정__ 섹션에서 __학습 키__ 및 __예측 키__ 필드의 값을 복사합니다.

![키 UI의 이미지](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>코드 이해

Visual Studio에서 SDK 프로젝트의 `Samples/CustomVision.Sample/` 디렉터리에 있는 프로젝트를 엽니다.

__내 새 프로젝트__라는 새 프로젝트를 만들려면 이 응용 프로그램은 앞서 검색한 학습 키를 사용합니다. 그런 다음, 분류자를 학습하고 테스트하려면 이미지를 업로드합니다. 분류자는 트리가 __솔송나무__인지 __벗나무__인지를 식별합니다.

다음 코드 조각은 이 예제의 기본 기능을 구현합니다.

* __새 Custom Vision Service 프로젝트를 만듭니다__.

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __프로젝트에서 태그를 만듭니다__.

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __이미지를 업로드하고 태그를 지정합니다__.

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __분류자 학습합니다__.

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __예측 엔드포인트에 대한 기본 반복을 설정합니다__.

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __예측 엔드포인트를 만듭니다__.
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __이미지를 예측 엔드포인트에 보냅니다__.

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>응용 프로그램 실행

1. 응용 프로그램에 학습 및 예측 키를 추가하려면 다음과 같이 변경합니다.

    * 다음 줄에 __학습 키__를 추가합니다.

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * 다음 줄에 __예측 키__를 추가합니다.

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. 응용 프로그램을 실행합니다. 응용 프로그램이 실행될 때 다음 출력이 콘솔에 기록됩니다.

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. 응용 프로그램을 종료하려면 아무 키나 누릅니다.
